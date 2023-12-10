# 使用HTTPS协议

## 获取密钥对和密钥

获取证书有两种途径：

* [**自签名证书**](https://app.gitbook.com/s/XswOsPx3RsMmwwn0j7vt/group-3/pei-zhi-ssl)，如果开发者只是想测试HTTPS，最快速的途径就是生成自签名证书，非常方便。
* 使用**CA机构签发的证书**，如果对证书安全性、兼容性、功能有特殊需求，可以向CA机构申请证书。

## 启动registry

假设已经通过上述途径获取到了**密钥对domain.key**和**证书domain.crt**，这两个文件放在了**目录certs**下，此时可以使用以下方式之一进行配置。

### 方式一：docker run

```bash
$ docker run -d \
  --restart=always \
  --name registry \
  -v "$(pwd)"/certs:/certs \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  -p 443:443 \
  registry:2
```

### 方式二：docker compose

首先创建**Dockerfile**文件，文件内容如下：

```dockerfile
FROM registry:2

LABEL MAINTAINCE="bohanz838@gmail.com"

ADD certs /certs
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
```

## 关闭安全性检查（可选）

<mark style="color:orange;">**在使用自签名证书的情况下，也需要关闭安全性检查。**</mark>

[#1.-guan-bi-an-quan-xing-jian-cha](./#1.-guan-bi-an-quan-xing-jian-cha "mention")

<mark style="color:blue;">**注意：因为https协议的默认端口是443，所以在配置时可以省略端口号。**</mark>

## 测试

首先，增加本机DNS映射（必须使用root）：

```bash
sudo su -
echo "192.168.10.110 example.com" >> /etc/hosts
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
