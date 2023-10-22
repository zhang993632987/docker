# images

使用<mark style="color:blue;">**docker images**</mark>或<mark style="color:blue;">**docker image ls**</mark>命令可以列出本地主机上已有镜像的基本信息。

{% code overflow="wrap" fullWidth="false" %}
```bash
$ docker images
REPOSITORY       TAG       IMAGE ID       CREATED        SIZE
ubuntu           latest    ba6acccedd29   2 years ago    72.8MB
ubuntu           18.04     5a214d77f5d7   2 years ago    63.1MB
```
{% endcode %}

在列出信息中，可以看到几个字段信息：

* 来自于哪个仓库，比如ubuntu表示ubuntu系列的基础镜像；
* 镜像的标签信息，比如18.04、latest表示不同的版本信息。标签只是标记，并不能标识镜像内容；
*   镜像的ID（唯一标识镜像），如果两个镜像的ID相同，说明它们实际上指向了同一个镜像，只是具有不同标签名称而已；

    镜像的ID信息十分重要，它唯一标识了镜像。在使用镜像ID的时候，一般可以使用该ID的前若干个字符组成的可区分串来替代完整的ID。
* 创建时间，说明镜像最后的更新时间；
* 镜像大小，镜像大小信息只是表示了该镜像的**逻辑体积**大小，**实际上由于相同的镜像层本地只会存储一份，物理上占用的存储空间会小于各镜像逻辑体积之和。**
