---
coverY: 0
layout:
  cover:
    visible: false
    size: full
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# ☑ Alpine

Alpine操作系统是一个面向安全的轻型Linux发行版，关注安全，性能和资源效能。不同于其他发行版，Alpine采用了musl libc和BusyBox以减小系统的体积和运行时资源消耗，比BusyBox功能上更完善。在保持瘦身的同时，Alpine还提供了**包管理工具apk**查询和安装软件包。

Alpine Docker镜像继承了Alpine Linux发行版的这些优势。相比于其他镜像，它的容量非常小，仅仅只有5 MB左右。

<mark style="color:blue;">**目前Docker官方推荐使用Alpine作为默认的基础镜像环境**</mark>，这可以带来多个优势，如镜像下载速度加快、镜像安全性提高、主机之间的切换更方便、占用更少磁盘空间等。

如果使用Alpine镜像，安装软件包时可以使用apk工具：

```bash
$ apk add --no-cache <package>
```

{% hint style="info" %}
Alpine中软件安装包的名字可能会与其他发行版有所不同，可以在[https://pkgs.alpinelinux.org/packages](https://pkgs.alpinelinux.org/packages)网站搜索并确定安装包名称。

如果需要的安装包不在主索引内，但是在测试或社区索引中。那么首先需要更新仓库列表，如下所示：

{% code overflow="wrap" %}
```bash
$ echo "http://dl-4.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories
$ apk --update add --no-cache <package>
```
{% endcode %}
{% endhint %}
