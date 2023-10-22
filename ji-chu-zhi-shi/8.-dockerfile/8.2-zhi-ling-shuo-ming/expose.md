# EXPOSE

<mark style="color:blue;">**声明镜像内服务监听的端口**</mark>。格式为**EXPOSE \<port> \[\<port>/\<protocol> ...]**。

{% hint style="warning" %}
<mark style="color:orange;">**该指令只是起到声明作用，并不会自动完成端口映射。**</mark>

如果要映射端口出来，在启动容器时可以使用-P参数（Docker主机会自动分配一个宿主机的临时端口）或-p HOST\_PORT:CONTAINER\_PORT参数（具体指定所映射的本地端口）。
{% endhint %}
