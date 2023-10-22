# RUN

<mark style="color:blue;">**运行指定命令**</mark>。格式为**RUN \<command>**或**RUN \["executable", "param1", "param2"]**。

注意：

* 后者指令会被解析为JSON数组，因此必须用双引号。
* 前者默认将在shell终端中运行命令，即/bin/sh -c；后者则使用exec执行，不会启动shell环境。

指定使用其他终端类型可以通过第二种方式实现，例如**RUN \["/bin/bash", "-c", "echo hello"]**。

{% hint style="info" %}
<mark style="color:blue;">**每条RUN指令将在当前镜像基础上执行指定命令，并提交为新的镜像层。**</mark>

当命令较长时可以使用**\\**来换行。
{% endhint %}
