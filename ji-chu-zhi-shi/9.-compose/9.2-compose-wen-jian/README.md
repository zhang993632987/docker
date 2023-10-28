# 9.2 Compose文件

默认的Compose文件名是<mark style="color:blue;">**compose.yaml**</mark>或<mark style="color:blue;">**compose.yml**</mark>，也向后兼容老版本的docker-compose.yaml和docker-compose.yml。

```yaml
services:
    webapp:
        image: examples/web
        deploy:
            replicas: 2
            resources:
                limits:
                    cpus: "0.1"
                    memory: 100M
                restart_policy:
                    condition: on-failure
        ports:
            - "80:80"
        networks:
            - mynet
        volumes:
            - "/data"
networks:
    mynet:

```

在上述示例中，Compose文件包括两个一级Key：services和networks。

在新版本的docker compose中，<mark style="color:orange;">**version可以省略**</mark>，并不是一个必选项，其存在的意义在于保持向后兼容性。
