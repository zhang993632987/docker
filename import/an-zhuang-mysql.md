# 安装mysql

## 获取镜像

**进入**[**docker hub**](https://hub.docker.com/)**网站，搜索mysql的镜像**

选择官方认证的排在前面的镜像，点击进入详情页面，通过 tag 选择版本号

阅读页面中How to use this image.

## **拉取镜像：**

```bash
docker pull mysql[:tag]
```

## 单节点安装

### **创建目录**

```bash
# 增加mysql用户
useradd mysql
passwd mysql

# 创建归属于mysql的目录，用于存储mysql相关文件
mkdir /media/mysql
mkdir /media/mysql/conf.d
mkdir /media/mysql/datadir
chown -R mysql:mysql /media/mysql

# 进入mysql用户的bash环境
su mysql

mkdir -p /media/mysql/password
# 输入密码(不规范的，只是用作说明)
echo "123456" > /media/mysql/password/root-pw.txt
echo "123456" > /media/mysql/password/hive-pw.txt
exit
```

### **添加配置**

**注意：配置文件应使用 **_**.cnf**_** 扩展名**

文件全限定名：**/media/mysql/conf.d/mysql.cnf**

```bash
vim /media/mysql/conf.d/mysql.cnf
```

```xml
[client]
default-character-set=utf8mb4

[mysql]
default-character-set=utf8mb4

[mysqld]
character-set-server=utf8mb4
collation-server=utf8mb4_general_ci
```

```bash
chown mysql:mysql /media/mysql/conf.d/mysql.cnf
```

### **运行容器**

```bash
# 获取用户的UID和GID
id mysql

# some-mysql：自定义容器名称
# --user 1001:1001：以指定用户（UID:GID）启动mysql服务（只能是ID,不能是用户名和组名）
# -v /my/custom:/etc/mysql/conf.d：自定义配置文件的防止目录
# -v /my/own/datadir:/var/lib/mysql: mysql数据放入宿主机的/my/own/datadir目录下
# MYSQL_ROOT_PASSWORD_FILE：指定用于存储root用户的密码的文件
# MYSQL_ROOT_PASSWORD：root用户的密码，，以命令行方式进行指定，存在安全隐患
# MYSQL_PASSWORD_FILE：指定用于存储新建用户的密码的文件，如果设置了MYSQL_DATABASE，则该用户会成为db的#					  超级用户
# MYSQL_PASSWORD：新建用户的密码，以命令行方式进行指定
docker run --name mysql-server \
--user 1001:1001 \
-p 3306:3306 \
-v /media/mysql/password:/run/secrets \
-v /media/mysql/conf.d:/etc/mysql/conf.d \
-v /media/mysql/datadir:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD_FILE=/run/secrets/root-pw.txt \
-e MYSQL_DATABASE=metastore \
-e MYSQL_USER=hive \
-e MYSQL_PASSWORD_FILE=/run/secrets/hive-pw.txt \
-d mysql[:tag]
```

### **测试**

```bash
# 进入容器
docker exec -it mysql-server /bin/bash

# 进入mysql的命令行客户端
mysql -uroot -p123456
```

```sql
mysql> SHOW VARIABLES LIKE "character%";
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8mb4                    |
| character_set_connection | utf8mb4                    |
| character_set_database   | utf8mb4                    |
| character_set_filesystem | binary                     |
| character_set_results    | utf8mb4                    |
| character_set_server     | utf8mb4                    |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.00 sec)

mysql> SHOW CREATE DATABASE warehouse;
+-----------+-----------------------------------------------------------------------+
| Database  | Create Database                                                       |
+-----------+-----------------------------------------------------------------------+
| warehouse | CREATE DATABASE `warehouse` /*!40100 DEFAULT CHARACTER SET utf8mb4 */ |
+-----------+-----------------------------------------------------------------------+
1 row in set (0.00 sec)

```

## 主从复制安装
