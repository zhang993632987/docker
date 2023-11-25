# 使用push时遇到的一些坑

## hosts相关

因为学习时会经常进行虚拟机的销毁和重建，或者会经常更新一些新的技术栈，从而导致私有仓库所在的虚拟机经常变更，为了降低经常变更IP地址带来的不利影响，因此考虑用一个统一的内部域名进行映射，之后如果变更了本地仓库的部署位置，只需修改hosts文件即可。

而本人使用的学习环境是将docker服务安装在Linux虚拟机上，并开放Docker的TCP远程访问链接。然后通过在本地的Windows机器上设置DOCKER\_HOST环境变量来使用远程的docker机器。

#### 问题1：远程与本地经常发生混淆

**在使用docker login hostname:port命令时，混淆了该命令实际的执行地点——在远程的Linux机器上**，因而遇到了不断在本地的hosts文件上增加配置，但是命令却提示连接超时，如下所示：

{% code overflow="wrap" %}
```properties
$ docker login example.com:5000
Username: testuser
Password: 
Error response from daemon: Get "https://example.com:5000/v2/": net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
```
{% endcode %}

<mark style="color:blue;">**问题出现的原因在于并未在实际部署本地registry服务的Linux机器上配置域名映射！**</mark>

#### 问题2：并未push到本地仓库

因为在部署registry服务时，使用了443这个https的默认端口号，所以**在docker tag新建镜像标签时省略了443这个端口号**，在加上使用了简单的域名，因此导致docker将镜像push到了其他地址，如下所示：

{% code lineNumbers="true" %}
```properties
$ docker push local-registry/hello:2
The push refers to repository [docker.io/local-registry/hello]
aeccf26589a7: Preparing
f640be0d5aad: Preparing
aa4330046b37: Preparing
ad10b481abe7: Preparing
69715584ec78: Preparing
denied: requested access to the resource is denied
```
{% endcode %}

注意第二行的内容，根据该行的信息，可知push的目的地并不是本地仓库，而是docker的公共仓库，docker出现了误判。

上述问题的解决方案有两种：

*   <mark style="color:blue;">**直接使用IP地址**</mark>

    ```properties
    $ docker push 192.168.10.124/hello:1.0
    The push refers to repository [192.168.10.124/hello]
    ```
*   <mark style="color:blue;">**添加端口号**</mark>

    ```properties
    $ docker push local-registry:443/hello:1.0
    The push refers to repository [local-registry:443/hello]
    ```

## docker的insecure-registries配置相关

注意，<mark style="color:blue;">**该配置属性是会区分端口号的**</mark>。也就是说，不是只配置了一个IP地址或域名地址就万事大吉了，如果在访问registry的时候添加了端口号，docker会报错，错误信息如下所示：

{% code overflow="wrap" %}
```bash
$ docker login local-registry:443
Username: testuser
Password: 
Error response from daemon: Get "https://local-registry:443/v2/": tls: failed to verify certificate: x509: certificate is not valid for any names, but wanted to match local-registry:443
```
{% endcode %}

{% code overflow="wrap" %}
```bash
$ docker push local-registry:443/web-redis:1.0
aeccf26589a7: Preparing
f640be0d5aad: Preparing
aa4330046b37: Preparing
ad10b481abe7: Preparing
69715584ec78: Preparing
denied: requested access to the resource is denied
```
{% endcode %}

上述错误信息很容易将人误导向“认为是本地registry服务的TLS证书配置出现了差错”。
