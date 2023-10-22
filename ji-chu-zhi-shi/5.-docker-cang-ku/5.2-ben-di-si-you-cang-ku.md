# 5.2 本地私有仓库

安装Docker后，可以通过官方提供的<mark style="color:blue;">**registry镜像**</mark>来简单搭建一套本地私有仓库环境。默认情况下，仓库会被创建在容器的**/var/lib/registry**目录下。可以通过 **-v** 参数来将镜像文件存放在本地的指定路径。

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>$ docker run -d -p 5000:5000 -v /opt/data/registry:/var/lib/registry registry:2
</strong><strong>
</strong>$ docker ps
CONTAINER ID   IMAGE           COMMAND                   CREATED          STATUS         PORTS                                       NAMES
576e442796e4   registry:2      "/entrypoint.sh /etc…"   10 seconds ago   Up 7 seconds   0.0.0.0:5000->5000/tcp, :::5000->5000/tcp   angry_kapitsa
</code></pre>

## 1. 关闭安全性检查

较新的Docker版本对安全性要求较高，会要求仓库支持SSL/TLS证书。**对于内部使用的私有仓库，可以自行配置证书或关闭对仓库的安全性检查。**

首先，修改**/etc/docker/daemon.json**，添加如下参数，表示信任这个私有仓库，不进行安全证书检查：

```json
"insecure-registries": ["192.168.10.110:5000"]
```

其中，**192.168.10.110是registry的宿主机IP地址，5000是registry容器映射出的端口号**。

然后，使用**sudo systemctl restart docker**命令重启docker服务。

更多关于daemon.json的配置细节，参见路径：[https://docs.docker.com/engine/reference/commandline/dockerd/#insecure-registries](https://docs.docker.com/engine/reference/commandline/dockerd/#insecure-registries)

## 2. 提交镜像

首先，使用**docker tag**改变待提交镜像的标签：

```bash
$ docker tag test:2.0 192.168.10.110:5000/test:2.0

$ docker images
REPOSITORY                 TAG       IMAGE ID       CREATED         SIZE
192.168.10.110:5000/test   2.0       a7077dfd797c   3 hours ago     72.8MB
```

然后，使用**docker push**命令提交镜像：

```bash
$ docker push 192.168.10.110:5000/test:2.0
```

{% hint style="warning" %}
为了方便后续观察，在将镜像提交到私有仓库后，会使用以下命令删除已有的镜像：

```bash
$ docker rmi 192.168.10.110:5000/test:2.0

$ docker rmi test:2.0
```
{% endhint %}

## 3. 拉取镜像

使用**docker pull**命令拉取提交至私有仓库的镜像：

```bash
$ docker pull 192.168.10.110:5000/test:2.0
```

下载后，还可以添加一个更通用的标签test:2.0，方便后续使用：

```bash
$ docker tag 192.168.10.110:5000/test:2.0 test:2.0

$ docker images
REPOSITORY                 TAG       IMAGE ID       CREATED         SIZE
192.168.10.110:5000/test   2.0       a7077dfd797c   3 hours ago     72.8MB
test                       2.0       a7077dfd797c   3 hours ago     72.8MB

```
