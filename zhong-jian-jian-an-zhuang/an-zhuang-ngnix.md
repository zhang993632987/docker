# 安装ngnix

## 方式一：命令行直接启动

```bash
$ docker pull nginx:alpine3.18-slim
$ docker run -d --name nginx-server \
    -v ~/nginx/config/nginx.conf:/etc/nginx/nginx.conf:ro \
    -v ~/nginx/static:/usr/share/nginx/html:ro \
    -p 8080:80 nginx:alpine3.18-slim
```

{% hint style="info" %}
获取默认配置

首先，随便启动一个nginx的容器：

```bash
$ docker run -d --name tmp-nginx nginx:alpine3.18-slim
```

然后，将容器中的配置文件复制到宿主机的当前目录：

```bash
$ docker cp tmp-nginx:/etc/nginx/nginx.conf .
$ ls 
nginx.conf
```
{% endhint %}

## 方式二：Dockerfile

1.  创建工作目录：

    ```bash
    $ mkdir nginx-custom
    $ cd nginx-custom
    ```
2.  新建**nginx.conf**文件（下面的内容是配置文件的默认内容）：

    ```nginx
    user  nginx;
    worker_processes  auto;

    error_log  /var/log/nginx/error.log notice;
    pid        /var/run/nginx.pid;


    events {
        worker_connections  1024;
    }


    http {
        include       /etc/nginx/mime.types;
        default_type  application/octet-stream;

        log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                          '$status $body_bytes_sent "$http_referer" '
                          '"$http_user_agent" "$http_x_forwarded_for"';

        access_log  /var/log/nginx/access.log  main;

        sendfile        on;
        #tcp_nopush     on;

        keepalive_timeout  65;

        #gzip  on;

        include /etc/nginx/conf.d/*.conf;
    }

    ```
3.  新建静态index.html页面：

    ```html
    <!DOCTYPE html>
    <html>
        <body>
            <p>Hello, Docker! </p>
        </body>
    </html>
    ```
4.  新建Dockerfile：

    ```docker
    FROM nginx:alpine3.18-slim
    COPY index.html /usr/share/nginx/html/index.html
    COPY nginx.conf /etc/nginx/nginx.conf
    ```
5.  构建镜像：

    ```bash
    $ docker build -t custom-nginx:latest .
    $ docker images
    REPOSITORY                 TAG               IMAGE ID       CREATED         SIZE
    custom-nginx               latest            9d56d9d2cefc   7 seconds ago   17MB
    ```
6.  启动容器：

    {% code overflow="wrap" %}
    ```bash
    $ docker run -d --name my-nginx -p 8080:80 custom-nginx
    $ docker ps
    CONTAINER ID   IMAGE          COMMAND                   CREATED         STATUS        PORTS                                   NAMES
    693636377aa8   custom-nginx   "/docker-entrypoint.…"   4 seconds ago   Up 1 second   0.0.0.0:8080->80/tcp, :::8080->80/tcp   my-nginx
    ```
    {% endcode %}
