# ENTRYPOINT

<mark style="color:blue;">**指定镜像的默认入口命令，该入口命令会在启动容器时作为根命令执行，所有传入值作为该命令的参数。**</mark>

支持两种格式：

* **ENTRYPOINT \["executable", "param1", "param2"]**：exec调用执行；
* **ENTRYPOINT command param1 param2：**shell中执行，此时，CMD指令指定值将作为根命令的参数。

{% hint style="info" %}
每个Dockerfile中**只能有一个ENTRYPOINT**，当指定多个时，只有最后一个起效。

在运行时，可以被-**-entrypoint**参数覆盖掉，如**docker run --entrypoint**。
{% endhint %}
