# 基于commit命令创建

## 1. 生成密钥对

在需要利用ssh命令远程访问docker容器的机器上，利用ssh-keygen命令生成密钥对：

<pre class="language-bash"><code class="lang-bash"><strong>$ ssh-keygen -t rsa
</strong></code></pre>

## 2.启动docker容器

下载并启动一个用于提供ssh服务的docker容器：

```bash
$ docker run -itd --name sshd -p 10220:22 ubuntu:18.04
```

## 3.安装ssh服务

在容器中安装openssh-server：

```bash
$ docker exec sshd apt-get update
$ docker exec sshd apt-get install -y openssh-server
```

## 4. 启动ssh服务

如果需要正常启动SSH服务，则目录 /var/run/sshd必须存在。下面手动创建它，并启动SSH服务：

```bash
$ docker exec sshd mkdir -p /var/run/sshd
$ docker exec -d sshd /usr/sbin/sshd -D
```

## 5. 免密登录

将宿主机的公钥复制到容器的authorized\_keys中：

{% code overflow="wrap" %}
```bash
$ docker exec sshd \
    sh -c "mkdir -p ~/.ssh"
$ docker exec sshd \
    sh -c "echo $(cat ~/.ssh/id_rsa.pub) >> ~/.ssh/authorized_keys"
```
{% endcode %}

## 6. 登录测试

使用ssh命令访问容器，注意：只有配置了免密登录的用户才能够访问，其他未配置免密的用户会报Permission Denied错误！

<pre class="language-bash"><code class="lang-bash"><strong>$ ssh root@localhost -p 10220
</strong>Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 3.10.0-1160.el7.x86_64 x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
Last login: Thu Oct 19 13:08:02 2023 from 172.17.0.1
root@6a02d44fc0cf:~# 
</code></pre>

## 7. 提交镜像并试用

1.  使用docker commit命令提交镜像：

    ```bash
    $ docker commit -a zhang -m "add sshd service" sshd sshd:1.0
    sha256:1583d2c535cbdd412f11e4320f75f801646f668a83c2bea47c42b067f9e5b7c1

    $ docker images
    REPOSITORY       TAG       IMAGE ID       CREATED         SIZE
    sshd             1.0       1583d2c535cb   4 seconds ago   224MB
    ```
2.  使用镜像：

    {% code overflow="wrap" %}
    ```bash
    $ docker run -d --name sshd2 -p 10221:22 sshd:1.0 /usr/sbin/sshd -D
    01dde85847fecb73ea1a27a98e54b63ab0ef720962b34941da019368f3cc4155

    $ ssh root@localhost -p 10221
    The authenticity of host '[localhost]:10221 ([::1]:10221)' can't be established.
    ECDSA key fingerprint is SHA256:MA3b1evMkCpTsuv6AGpy3DUsuFSxlqmwtMNtg837G/M.
    ECDSA key fingerprint is MD5:84:69:9d:12:6b:15:4d:c6:0a:66:53:91:78:4d:61:68.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '[localhost]:10221' (ECDSA) to the list of known hosts.
    Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 3.10.0-1160.el7.x86_64 x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    This system has been minimized by removing packages and content that are
    not required on a system that users do not log into.

    To restore this content, you can run the 'unminimize' command.
    Last login: Thu Oct 19 13:12:07 2023 from 172.17.0.1
    root@01dde85847fe:~#
    ```
    {% endcode %}
