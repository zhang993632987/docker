# 使用Dockerfile创建

## 1. 创建工作目录

```bash
$ mkdir ~/ssh_ubuntu
```

## 2. 编写authorized\_keys文件

```bash
$ cat ~/.ssh/id_rsa.pub > ~/ssh_ubuntu/authorized_keys
```

## 3. 编写Dockerfile

```bash
$ vim Dockerfile
```

文件内容如下：

```docker
FROM ubuntu:18.04

LABEL MAINTAINER=zhang

USER root

RUN apt-get update &&\
        apt-get install -y openssh-server
RUN mkdir -p /var/run/sshd /root/.ssh
COPY authorized_keys /root/.ssh/authorized_keys 

EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]
```

## 4.创建镜像

```bash
$ docker build -t sshd:2.0 ~/ssh_ubuntu

$ docker images
REPOSITORY       TAG       IMAGE ID       CREATED         SIZE
sshd             2.0       2ce2cf1f5904   9 seconds ago   224MB
```

## 5.测试镜像，运行容器

```bash
$ docker run -d --name sshd -p 10223:22 sshd:2.0
e39bba79569d1ce7b37271b6d0d6b205bcd47ce8922c2983332207538852c180

$ ssh root@localhost -p 10223
The authenticity of host '[localhost]:10223 ([::1]:10223)' can't be established.
ECDSA key fingerprint is SHA256:1paj3O3XUCMDfiAnW2uOibArt89U1AY3T3w736tPTjg.
ECDSA key fingerprint is MD5:71:57:4c:2f:54:45:9b:cd:48:81:ee:4c:64:23:ec:09.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[localhost]:10223' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 3.10.0-1160.el7.x86_64 x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

root@e39bba79569d:~# 
```
