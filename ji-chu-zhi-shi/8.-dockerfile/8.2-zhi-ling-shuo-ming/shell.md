# SHELL

<mark style="color:blue;">**指定其他命令使用shell时的默认shell类型**</mark>，格式为**SHELL \["executable", "parameters"]**，默认值为\["/bin/sh", "-c"]。

{% hint style="warning" %}
对于Windows系统，Shell路径中使用了“\”作为分隔符，<mark style="color:orange;">**建议在Dockerfile开头添加# escape=’来指定转义符。**</mark>
{% endhint %}
