# FROM

<mark style="color:blue;">**指定所创建镜像的基础镜像**</mark>。格式为**FROM \<image> \[AS \<name>]**或**FROM \<image>:\<tag> \[AS \<name>]**或**FROM \<image>@\<digest> \[AS \<name>]**。

{% hint style="warning" %}
<mark style="color:orange;">**任何Dockerfile中第一条指令必须为FROM指令**</mark>。并且，如果在同一个Dockerfile中创建多个镜像时，可以使用多个FROM指令（每个镜像一次）。
{% endhint %}

{% hint style="info" %}
<mark style="color:blue;">**为了保证镜像精简，可以选用体积较小的镜像如Alpine或Debian作为基础镜像。**</mark>
{% endhint %}
