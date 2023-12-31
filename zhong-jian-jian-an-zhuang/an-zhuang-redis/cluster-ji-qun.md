# Cluster集群

一个 Redis 集群中的节点数量至少需要 6 个才能保证组成完整高可用的集群。每个节点都需要开启 cluster-enabled yes，让 Redis 运行在集群模式下。建议为集群内所有节点统一目录，一般划分为三个目录：conf、data、log，分别存放配置、数据和日志相关文件。

> * 集群模式的 Redis 除了原有的配置文件之外，又增加了一份<mark style="color:blue;">**集群配置文件**</mark>（由参数 cluster-config-file 设置）。
>   * 当集群内节点信息发生变化，如添加节点、节点下线、故障转移等，节点会自动保存集群状态到集群配置文件中。
>   * Redis 自动维护集群配置文件，不要手动修改，防止节点重启时产生集群信息错误。
> *   <mark style="color:blue;">**节点 ID**</mark>：它是一个长度为 40 位的 16 进制字符串，用于唯一标识集群内一个节点。
>
>     节点 ID 不同于运行 ID，节点 ID 在集群初始化时创建，节点重启时会加载集群配置文件进行重用，而 Redis 的运行 ID 每次重启都会变化。&#x20;

## **创建目录**

```bash
mkdir -p /media/redis/node1/conf
mkdir -p /media/redis/node1/data

vim /media/redis/node1/conf/redis.conf
```

注意：redis.conf中必须配置以下属性：

* cluster-enabled yes
* bind \* -::\*
* protected-mode no

```bash
cp -r /media/redis/node1 /media/redis/node2 
cp -r /media/redis/node1 /media/redis/node3
cp -r /media/redis/node1 /media/redis/node4
cp -r /media/redis/node1 /media/redis/node5
cp -r /media/redis/node1 /media/redis/node6
```

## **启动集群服务**

### **第一步：准备节点**

{% code overflow="wrap" %}
```bash
docker run -v /media/redis/node1/conf:/etc/redis -v /media/redis/node1/data:/data --name redis-node1 --net redis-net -d redis:7.0.12 redis-server /etc/redis/redis.conf

docker run -v /media/redis/node2/conf:/etc/redis -v /media/redis/node2/data:/data --name redis-node2 --net redis-net -d redis:7.0.12 redis-server /etc/redis/redis.conf

docker run -v /media/redis/node3/conf:/etc/redis -v /media/redis/node3/data:/data --name redis-node3 --net redis-net -d redis:7.0.12 redis-server /etc/redis/redis.conf

docker run -v /media/redis/node4/conf:/etc/redis -v /media/redis/node4/data:/data --name redis-node4 --net redis-net -d redis:7.0.12 redis-server /etc/redis/redis.conf

docker run -v /media/redis/node5/conf:/etc/redis -v /media/redis/node5/data:/data --name redis-node5 --net redis-net -d redis:7.0.12 redis-server /etc/redis/redis.conf

docker run -v /media/redis/node6/conf:/etc/redis -v /media/redis/node6/data:/data --name redis-node6 --net redis-net -d redis:7.0.12 redis-server /etc/redis/redis.conf
```
{% endcode %}

此时，各个redis服务均处于独立状态，彼此并无联系

```properties
[root@docker-host redis]# docker exec -it redis-node1 redis-cli cluster nodes
bd88e14a2634abc4ab38308e300a513b8ac253fc :6379@16379 myself,master - 0 0 0 connected
[root@docker-host redis]# docker exec -it redis-node2 redis-cli cluster nodes
e5ceee80bb04c59c72e50f8b6a194c44dbeba1ac :6379@16379 myself,master - 0 0 0 connected
...
```

### **第二步：节点握手，并分配分区槽**

{% code overflow="wrap" %}
```bash
docker exec -it redis-node1 \
redis-cli --cluster create --cluster-replicas 1 redis-node1:6379 redis-node2:6379 redis-node3:6379 redis-node4:6379 redis-node5:6379 redis-node6:6379
```
{% endcode %}

### **第三步：检查集群状态**

<details>

<summary>redis-cli cluster nodes</summary>

{% code overflow="wrap" lineNumbers="true" %}
```properties
[root@docker-host redis]# docker exec -it redis-node1 redis-cli cluster nodes
c26b99f08a621581431b98d43d29caaa506b141d 172.18.0.5:6379@16379 slave ede4128545e99cbd4c1275e3f2f9c9f97e91ad54 0 1689671423000 3 connected
ede4128545e99cbd4c1275e3f2f9c9f97e91ad54 172.18.0.4:6379@16379 master - 0 1689671423558 3 connected 10923-16383
54e9eb4e2fda25815f157e682a062e7efc0a76fc 172.18.0.7:6379@16379 slave e5ceee80bb04c59c72e50f8b6a194c44dbeba1ac 0 1689671422551 2 connected
bd88e14a2634abc4ab38308e300a513b8ac253fc 172.18.0.2:6379@16379 myself,master - 0 1689671423000 1 connected 0-5460
e5ceee80bb04c59c72e50f8b6a194c44dbeba1ac 172.18.0.3:6379@16379 master - 0 1689671424566 2 connected 5461-10922
2246d056901e369cf170c514550f260a687182bf 172.18.0.6:6379@16379 slave bd88e14a2634abc4ab38308e300a513b8ac253fc 0 1689671423000 1 connected
```
{% endcode %}

</details>

<details>

<summary>redis-cli cluster info</summary>

<pre class="language-properties"><code class="lang-properties"><strong>[root@docker-host redis]# docker exec -it redis-node1 redis-cli cluster info
</strong>cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:6
cluster_size:3
cluster_current_epoch:6
cluster_my_epoch:1
cluster_stats_messages_ping_sent:103
cluster_stats_messages_pong_sent:111
cluster_stats_messages_sent:214
cluster_stats_messages_ping_received:106
cluster_stats_messages_pong_received:103
cluster_stats_messages_meet_received:5
cluster_stats_messages_received:214
total_cluster_links_buffer_limit_exceeded:0
</code></pre>

</details>

<details>

<summary>redis-cli --cluster check redis-node1:6379</summary>

{% code overflow="wrap" lineNumbers="true" %}
```properties
[root@docker-host redis]# docker exec -it redis-node1 redis-cli --cluster check redis-node1:6379
redis-node1:6379 (bd88e14a...) -> 0 keys | 5461 slots | 1 slaves.
172.18.0.4:6379 (ede41285...) -> 0 keys | 5461 slots | 1 slaves.
172.18.0.3:6379 (e5ceee80...) -> 0 keys | 5462 slots | 1 slaves.
[OK] 0 keys in 3 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node redis-node1:6379)
M: bd88e14a2634abc4ab38308e300a513b8ac253fc redis-node1:6379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: c26b99f08a621581431b98d43d29caaa506b141d 172.18.0.5:6379
   slots: (0 slots) slave
   replicates ede4128545e99cbd4c1275e3f2f9c9f97e91ad54
M: ede4128545e99cbd4c1275e3f2f9c9f97e91ad54 172.18.0.4:6379
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: 54e9eb4e2fda25815f157e682a062e7efc0a76fc 172.18.0.7:6379
   slots: (0 slots) slave
   replicates e5ceee80bb04c59c72e50f8b6a194c44dbeba1ac
M: e5ceee80bb04c59c72e50f8b6a194c44dbeba1ac 172.18.0.3:6379
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 2246d056901e369cf170c514550f260a687182bf 172.18.0.6:6379
   slots: (0 slots) slave
   replicates bd88e14a2634abc4ab38308e300a513b8ac253fc
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```
{% endcode %}

</details>

### **访问redis集群**

```bash
[root@docker-host redis]# docker exec -it redis-node1 redis-cli -c
127.0.0.1:6379> set k1 v1
-> Redirected to slot [12706] located at 172.18.0.4:6379
OK
172.18.0.4:6379> set k2 v2
-> Redirected to slot [449] located at 172.18.0.2:6379
OK
172.18.0.2:6379> exit

```

<mark style="color:orange;">**redis-cli命令中的 -c 的作用时，当收到重定向信息时，进行自动重定向**</mark>

## **集群扩容**

### **第一步：准备节点**

```bash
cp -r /media/redis/node1 /media/redis/node7 
rm -rf /media/redis/node7/data/*

cp -r /media/redis/node1 /media/redis/node8
rm -rf /media/redis/node8/data/*
```

{% code overflow="wrap" %}
```bash
docker run -v /media/redis/node7/conf:/etc/redis -v /media/redis/node7/data:/data --name redis-node7 --net redis-net -d redis:7.0.12 redis-server /etc/redis/redis.conf

docker run -v /media/redis/node8/conf:/etc/redis -v /media/redis/node8/data:/data --name redis-node8 --net redis-net -d redis:7.0.12 redis-server /etc/redis/redis.conf
```
{% endcode %}

### **第二步：新节点加入集群**

```bash
docker exec -it redis-node1 \
redis-cli --cluster add-node redis-node7:6379 redis-node1:6379
```

### **第三步：槽迁移**

```bash
docker exec -it redis-node1 \
redis-cli --cluster reshard redis-node1:6379 \
--cluster-from all \
--cluster-to bca02a4b4d88a03bb3bd6bcd338196b2ca6d31fe \
--cluster-slots 4096 --cluster-yes --cluster-timeout 100 --cluster-pipeline 1000
```

### **第四步：检查集群状态**

<details>

<summary>redis-cli --cluster check redis-node1:6379</summary>

{% code overflow="wrap" lineNumbers="true" %}
```properties
[root@docker-host redis]# docker exec -it redis-node1 redis-cli --cluster check redis-node1:6379
172.18.0.6:6379 (2246d056...) -> 0 keys | 4096 slots | 1 slaves.
172.18.0.8:6379 (bca02a4b...) -> 1 keys | 4096 slots | 0 slaves.
172.18.0.3:6379 (e5ceee80...) -> 0 keys | 4096 slots | 1 slaves.
172.18.0.4:6379 (ede41285...) -> 1 keys | 4096 slots | 1 slaves.
[OK] 2 keys in 4 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node redis-node1:6379)
S: bd88e14a2634abc4ab38308e300a513b8ac253fc redis-node1:6379
   slots: (0 slots) slave
   replicates 2246d056901e369cf170c514550f260a687182bf
M: 2246d056901e369cf170c514550f260a687182bf 172.18.0.6:6379
   slots:[1365-5460] (4096 slots) master
   1 additional replica(s)
S: c26b99f08a621581431b98d43d29caaa506b141d 172.18.0.5:6379
   slots: (0 slots) slave
   replicates ede4128545e99cbd4c1275e3f2f9c9f97e91ad54
M: bca02a4b4d88a03bb3bd6bcd338196b2ca6d31fe 172.18.0.8:6379
   slots:[0-1364],[5461-6826],[10923-12287] (4096 slots) master
S: 54e9eb4e2fda25815f157e682a062e7efc0a76fc 172.18.0.7:6379
   slots: (0 slots) slave
   replicates e5ceee80bb04c59c72e50f8b6a194c44dbeba1ac
M: e5ceee80bb04c59c72e50f8b6a194c44dbeba1ac 172.18.0.3:6379
   slots:[6827-10922] (4096 slots) master
   1 additional replica(s)
M: ede4128545e99cbd4c1275e3f2f9c9f97e91ad54 172.18.0.4:6379
   slots:[12288-16383] (4096 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```
{% endcode %}

</details>

### **第五步：增加slave节点**

{% code overflow="wrap" %}
```bash
# bca02a4b4d88a03bb3bd6bcd338196b2ca6d31fe为新增从节点的跟踪主节点的node id
docker exec -it redis-node1 \
redis-cli --cluster add-node redis-node8 redis-node1 --cluster-slave --cluster-master-id bca02a4b4d88a03bb3bd6bcd338196b2ca6d31fe
```
{% endcode %}

## **集群缩容**

### **第一步：移除slave节点**

```bash
# d143b322b356edce52909b6686384555968ceb34是待移除节点的node id
docker exec -it redis-node1 \
redis-cli --cluster del-node redis-node1:6379 \
d143b322b356edce52909b6686384555968ceb34
```

### **第二步：槽迁移**

**为了满足负载均衡条件，在归还节点的slot时，应该将这些slot**<mark style="color:orange;">**均匀**</mark>**地分配给各个主节点。**

而因为reshard命令只能向一个目标节点迁移slot，因此需要多次执行reshard命令以完成slot的迁移。

```bash
docker exec -it redis-node1 \
redis-cli --cluster reshard redis-node1:6379 \
--cluster-from bca02a4b4d88a03bb3bd6bcd338196b2ca6d31fe \
--cluster-to ede4128545e99cbd4c1275e3f2f9c9f97e91ad54 \
--cluster-slots 1366 --cluster-yes --cluster-timeout 100 --cluster-pipeline 1000


docker exec -it redis-node1 \
redis-cli --cluster reshard redis-node1:6379 \
--cluster-from bca02a4b4d88a03bb3bd6bcd338196b2ca6d31fe \
--cluster-to 2246d056901e369cf170c514550f260a687182bf \
--cluster-slots 1366 --cluster-yes --cluster-timeout 100 --cluster-pipeline 1000


docker exec -it redis-node1 \
redis-cli --cluster reshard redis-node1:6379 \
--cluster-from bca02a4b4d88a03bb3bd6bcd338196b2ca6d31fe \
--cluster-to e5ceee80bb04c59c72e50f8b6a194c44dbeba1ac \
--cluster-slots 1366 --cluster-yes --cluster-timeout 100 --cluster-pipeline 1000
```

<details>

<summary>redis-cli --cluster check redis-node1:6379</summary>

{% code overflow="wrap" lineNumbers="true" %}
```properties
[root@docker-host redis]# docker exec -it redis-node1 redis-cli --cluster check redis-node1:6379

172.18.0.6:6379 (2246d056...) -> 0 keys | 5461 slots | 1 slaves.
172.18.0.3:6379 (e5ceee80...) -> 0 keys | 5461 slots | 1 slaves.
172.18.0.4:6379 (ede41285...) -> 2 keys | 5462 slots | 2 slaves.
[OK] 2 keys in 3 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node redis-node1:6379)
S: bd88e14a2634abc4ab38308e300a513b8ac253fc redis-node1:6379
   slots: (0 slots) slave
   replicates 2246d056901e369cf170c514550f260a687182bf
M: 2246d056901e369cf170c514550f260a687182bf 172.18.0.6:6379
   slots:[1366-5460],[6826],[10923-12287] (5461 slots) master
   1 additional replica(s)
S: c26b99f08a621581431b98d43d29caaa506b141d 172.18.0.5:6379
   slots: (0 slots) slave
   replicates ede4128545e99cbd4c1275e3f2f9c9f97e91ad54
S: bca02a4b4d88a03bb3bd6bcd338196b2ca6d31fe 172.18.0.8:6379
   slots: (0 slots) slave
   replicates ede4128545e99cbd4c1275e3f2f9c9f97e91ad54
S: 54e9eb4e2fda25815f157e682a062e7efc0a76fc 172.18.0.7:6379
   slots: (0 slots) slave
   replicates e5ceee80bb04c59c72e50f8b6a194c44dbeba1ac
M: e5ceee80bb04c59c72e50f8b6a194c44dbeba1ac 172.18.0.3:6379
   slots:[5461-6825],[6827-10922] (5461 slots) master
   1 additional replica(s)
M: ede4128545e99cbd4c1275e3f2f9c9f97e91ad54 172.18.0.4:6379
   slots:[0-1365],[12288-16383] (5462 slots) master
   2 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```
{% endcode %}

</details>

### **第三步：移除master节点**

```bash
docker exec -it redis-node1 \
redis-cli --cluster del-node redis-node1:6379 \
bca02a4b4d88a03bb3bd6bcd338196b2ca6d31fe
```

<details>

<summary>redis-cli cluster nodes</summary>

{% code overflow="wrap" lineNumbers="true" %}
```properties
# 3主3从
[root@docker-host redis]# docker exec -it redis-node1 redis-cli cluster nodes
2246d056901e369cf170c514550f260a687182bf 172.18.0.6:6379@16379 master - 0 1689679937000 12 connected 1366-5460 6826 10923-12287
c26b99f08a621581431b98d43d29caaa506b141d 172.18.0.5:6379@16379 slave ede4128545e99cbd4c1275e3f2f9c9f97e91ad54 0 1689679936985 13 connected
bd88e14a2634abc4ab38308e300a513b8ac253fc 172.18.0.2:6379@16379 myself,slave 2246d056901e369cf170c514550f260a687182bf 0 1689679935000 12 connected
54e9eb4e2fda25815f157e682a062e7efc0a76fc 172.18.0.7:6379@16379 slave e5ceee80bb04c59c72e50f8b6a194c44dbeba1ac 0 1689679936000 10 connected
e5ceee80bb04c59c72e50f8b6a194c44dbeba1ac 172.18.0.3:6379@16379 master - 0 1689679937987 10 connected 5461-6825 6827-10922
ede4128545e99cbd4c1275e3f2f9c9f97e91ad54 172.18.0.4:6379@16379 master - 0 1689679934000 13 connected 0-1365 12288-16383

```
{% endcode %}

</details>
