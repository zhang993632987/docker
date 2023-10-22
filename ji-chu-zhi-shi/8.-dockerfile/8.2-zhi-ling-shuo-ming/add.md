# ADD

<mark style="color:blue;">**添加内容到镜像**</mark>。格式为**ADD \<src> \<dest>**。

该命令将复制指定的**\<src>**路径下的内容到容器中的**\<dest>**路径下。

其中：

* **\<src>**可以是Dockerfile所在目录的一个**相对路径**（文件或目录）；也可以是一个**URL**；还可以是一个**tar文件**（自动解压为目录）；
* **\<dest>**可以是镜像内的**绝对路径**，或者相对于工作目录（WORKDIR）的**相对路径**。

{% hint style="info" %}
路径可以是正则表达式。
{% endhint %}
