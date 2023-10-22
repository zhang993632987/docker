# ENV

<mark style="color:blue;">**指定环境变量，在镜像生成过程中会被后续RUN指令使用，在镜像启动的容器中也会存在**</mark>。格式为**ENV \<key> \<value>**或**ENV \<key>=\<value> ...**。

{% hint style="info" %}
指令指定的环境变量在运行时可以被覆盖掉，如**docker run --env \<key>=\<value> built\_image**。
{% endhint %}
