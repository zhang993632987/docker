# 添加用户认证

## 前提

registry规定使用认证时必须配置TSL，因此需要先配置HTTPS。

{% content-ref url="shi-yong-https-xie-yi.md" %}
[shi-yong-https-xie-yi.md](shi-yong-https-xie-yi.md)
{% endcontent-ref %}

## 创建用户和密码

创建一个用户：用户名testuser，密码为123456。

```bash
$ mkdir auth
$ docker run \
  --entrypoint htpasswd \
  httpd:2 -Bbn testuser 123456 > auth/htpasswd
```

在Windows平台上，应该运行下述命令：

{% code overflow="wrap" %}
```sh
docker run --rm --entrypoint htpasswd httpd:2 -Bbn testuser 123456 | Set-Content -Encoding ASCII auth/htpasswd
```
{% endcode %}

## 启动registry

假设已经通过上述途径获取到了**密钥对domain.key**和**证书domain.crt**，这两个文件放在了**目录certs**下，此时可以使用以下方式之一进行配置。

### 方式一：docker run

```bash
$ docker run -d \
  -p 443:443 \
  --restart=always \
  --name registry \
  -v "$(pwd)"/auth:/auth \
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  -v "$(pwd)"/certs:/certs \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  registry:2
```

### 方式二：docker compose

首先创建**Dockerfile**文件，文件内容如下：

```dockerfile
FROM registry:2

LABEL MAINTAINCE="bohanz838@gmail.com"

ADD certs /certs
ADD auth /auth
```

创建**compose.yaml**文件，内容如下：

```yaml
services:
  registry:
    restart: always
    build:
      context: .
      tags:
        - "registry:v1"
    ports:
      - 443:443
    environment:
      REGISTRY_HTTP_ADDR: 0.0.0.0:443
      REGISTRY_HTTP_TLS_CERTIFICATE: /certs/domain.crt
      REGISTRY_HTTP_TLS_KEY: /certs/domain.key
      REGISTRY_AUTH: htpasswd
      REGISTRY_AUTH_HTPASSWD_PATH: /auth/htpasswd
      REGISTRY_AUTH_HTPASSWD_REALM: Registry Realm
```

## 关闭安全性检查（可选）

[#guan-bi-an-quan-xing-jian-cha-ke-xuan](tian-jia-yong-hu-ren-zheng.md#guan-bi-an-quan-xing-jian-cha-ke-xuan "mention")

## 测试

首先，增加本机DNS映射（必须使用root）：

```bash
sudo su -
echo "192.168.10.110 example.com" >> /etc/hosts
```

然后，登录registry，输入用户名testuser，密码123456：

```bash
docker login example.com
```

向registry提交镜像：

```bash
docker tag ubuntu:20.04 example.com/ubuntu:v1
docker push example.com/ubuntu:v1
```

从registry拉取镜像：

```bash
docker rmi ubuntu:20.04 example.com/ubuntu:v1
docker pull example.com/ubuntu:v1
docker images example.com/ubuntu
```
