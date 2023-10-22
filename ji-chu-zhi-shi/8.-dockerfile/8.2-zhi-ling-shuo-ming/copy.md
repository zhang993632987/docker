# COPY

<mark style="color:blue;">**复制内容到镜像**</mark>。格式为**COPY \<src> \<dest>**。

复制本地主机的**\<src>**（为Dockerfile所在目录的相对路径，文件或目录）下的内容到镜像中的**\<dest>**。目标路径不存在时，会自动创建。

{% hint style="info" %}
**路径同样可以为正则表达式。**
{% endhint %}

{% hint style="warning" %}
COPY与ADD指令功能类似，<mark style="color:orange;">**当使用本地目录为源目录时，推荐使用COPY**</mark>。
{% endhint %}
