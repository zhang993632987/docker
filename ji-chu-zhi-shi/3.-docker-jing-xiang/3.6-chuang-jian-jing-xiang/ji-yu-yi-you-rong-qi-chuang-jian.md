# 基于已有容器创建

该方法主要是使用<mark style="color:blue;">**docker \[container] commit**</mark>命令。命令格式为**docker \[container] commit \[OPTIONS] CONTAINER \[REPOSITORY \[:TAG]]**，主要选项包括：

* \-a, --author=""：作者信息；
* \-c, --change=\[]：提交的时候执行Dockerfile指令，包括CMD、ENTRYPOINT、ENV、EXPOSE、LABEL、ONBUILD、USER、VOLUME、WORKDIR等；
* \-m, --message=""：提交消息；
* \-p, --pause=true：提交时暂停容器运行。

下面将演示如何使用该命令创建一个新镜像。

首先，启动一个镜像，并在其中进行修改操作：

```bash
$ docker run -it ubuntu /bin/bash
root@b4a056b73d12:/# touch test 
root@b4a056b73d12:/# exit
```

然后，使用docker \[container] commit命令来提交为一个新的镜像。提交时可以使用ID或名称来指定容器：

```bash
$ docker commit -m "Added a new file" -a "zhang" b4a056b73d12 test:1.0
sha256:e9689ed390e205e57276beb2c5d51ff377b81f3460970152d7ed871a096424b4
```

最后，查看本地镜像列表：

{% code overflow="wrap" %}
```bash
$ docker images
REPOSITORY       TAG       IMAGE ID       CREATED          SIZE
test             1.0       e9689ed390e2   51 seconds ago   72.8MB

$ docker history test:1.0
IMAGE          CREATED         CREATED BY                                       SIZE      COMMENT
e9689ed390e2   2 minutes ago   /bin/bash                                        16B       Added a new file
ba6acccedd29   2 years ago     /bin/sh -c #(nop)  CMD ["bash"]                  0B        
<missing>      2 years ago     /bin/sh -c #(nop) ADD file:5d68d27cc15a80653…   72.8MB

$ docker inspect -f {{".Author"}} test:1.0
zhang
```
{% endcode %}
