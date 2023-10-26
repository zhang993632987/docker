# 安装Apache

DockerHub官方提供的Apache镜像，并不带PHP环境。如果需要PHP环境支持，可以选择PHP镜像（[https://registry.hub.docker.com/\_/php/](https://hub-stage.docker.com/\_/php)），并请使用含-apache标签的镜像，如7.0.7-apache。如果仅需要使用Apache运行静态HTML文件，则使用默认官方镜像即可。

## 1. 创建工作目录

<pre class="language-bash"><code class="lang-bash"><strong>$ mkdir ~/apache-alpine
</strong></code></pre>

## 2. 创建项目

创建项目目录public-html，并在此目录下创建index.html：

```bash
$ mkdir ~/apache-alpine/public-html

$ vim ~/apache-alpine/public-html/index.html
```

index.html中的内容如下：

```html
<! DOCTYPE html>
<html>
    <body>
        <p>Hello, Docker! </p>
    </body>
</html>
```

## 3.编辑Dockerfile

```bash
$ vim ~/apache-alpine/Dockerfile
```

```docker
FROM httpd:2.4.58-alpine3.18

COPY public-html /usr/local/apache2/htdocs/
```

## 4. 创建镜像

```bash
$ docker build -t myapp:1.0 ~/apache-alpine

$ docker images
REPOSITORY       TAG       IMAGE ID       CREATED          SIZE
myapp            1.0       4d02b25bc32a   16 seconds ago   59.8MB
```

## 5. 创建apache容器

```bash
$ docker run -d --name myapp -p 80:80 myapp:1.0
0fc44ec95a9f5894c0ecd5fd851c14b1b0de83b701cb93a24684be6c312cce0b

$ curl http://localhost:80/
<! DOCTYPE html>
<html>
    <body>
        <p>Hello, Docker! </p>
    </body>
</html>
```
