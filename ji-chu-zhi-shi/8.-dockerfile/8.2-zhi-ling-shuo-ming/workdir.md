# WORKDIR

<mark style="color:blue;">**为后续的RUN、CMD、ENTRYPOINT指令配置工作目录**</mark>。格式为**WORKDIR /path/to/workdir**。

{% hint style="info" %}
可以使用多个WORKDIR指令，后续命令如果参数是**相对路径**，则会基于之前命令指定的路径。

例如，下面示例的最终路径**/a/b/c**：

```docker
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
```
{% endhint %}

{% hint style="danger" %}
<mark style="color:red;">**为了避免出错，推荐WORKDIR指令中只使用绝对路径。**</mark>
{% endhint %}
