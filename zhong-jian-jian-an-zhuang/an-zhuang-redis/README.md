# 安装redis

## 获取镜像

```bash
docker pull redis:7.0.12
```

## 创建目录

```bash
mkdir /media/redis
```

##

## **redis-cli --cluster的子命令**

### **create子命令**

````bash
​```
	创建集群
	该命令会尽可能保证主从节点不分配在同一台机器上
​```
create --cluster-replicas <arg> new_host_1:new_port_1 new_host_2:new_port_2 ...

# --cluster-replicas <arg> -> <arg>为一个整数，表示复制系数，即每一个主节点有几个从节点
# new_host_1:new_port_1 new_host_2:new_port_2 ... -> 节点列表，节点间由空格隔开
````

### **check子命令**

````bash
​```
	集群完整性检查
	集群完整性指所有的槽都被分配到了存活的主节点上，只要16384各槽中有一个没有被分配则表示集群不完整
​```
check host:port

# host:port -> 集群内任意节点的地址，用来获取整个集群信息
````

### **add-node子命令**

````bash
​```
	为集群添加添加节点
​```
add-node new_host:new_port host:port --cluster-slave --cluster-master-id <arg>

# new_host:new_port -> 新加入节点的地址
# host:port -> 集群内任意节点的地址，用来获取整个集群信息
# --cluster-slave -> 新加入节点为从节点
# --cluster-master-id <arg> -> 新加入的从节点复制哪一个主节点，<arg>为该主节点的node id
# 若去除--cluster-slave和--cluster-master-id，则新加入的节点为一个主节点
````

### **del-node子命令**

````bash
​```
	删除并忘记节点
	在执行该命令前，请先保证已经利用reshard命令移除了节点上的所有槽
​```
del-node host:port <downNodeId>

# host:port -> 集群内任意节点的地址，用来获取整个集群信息
# <downNodeId> -> 需要删除的节点的节点ID
````

### **reshard子命令**

````bash
​```
	槽迁移
	除了host:port是必传参数外，其他参数均是可选参数。
	对于参数--cluster-from，--cluster-to，--cluster-slots，如果不设置，命令会以交互的方式执行，并在交互的过程中对这些参数进行设置
​```
reshard host:port --cluster-from <arg> --cluster-to <arg> --cluster-slots <arg> --cluster-yes --cluster-timeout <arg> --cluster-pipeline <arg>

# host:port -> 必传参数，集群内任意节点的地址，用来获取整个集群信息
# --cluster-from -> 源节点的节点ID（槽移出的节点），如果有多个源节点，使用逗号分隔; all表示集群内所有主节点
# --cluster-to -> 目标节点的节点ID（槽迁入的节点），与源节点不同目标节点只能有一个
# --cluster-slots -> 需要迁移的槽的总数量
# --cluster-yes -> 不需要用户输入yes来确认执行计划
# --cluster-timeout -> 每次migrate操作的超时时间，默认为60000毫秒
# --cluster-pipeline -> 每次批量迁移键的数量，默认为10

````
