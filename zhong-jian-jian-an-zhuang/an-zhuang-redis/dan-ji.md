# 单机

### **创建目录**

```bash
mkdir /media/redis/conf
mkdir /media/redis/data

vim /media/redis/conf/redis.conf
```

### **启动服务**

{% code overflow="wrap" %}
```bash
docker run -v /media/redis/conf:/etc/redis -v /media/redis/data:/data --name standalone -d redis:7.0.12 redis-server /etc/redis/redis.conf
```
{% endcode %}

### **访问redis服务**

```bash
docker exec -it standalone redis-cli
```

