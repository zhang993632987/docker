# ARG

<mark style="color:blue;">**定义**</mark><mark style="color:orange;">**创建镜像过程中**</mark><mark style="color:blue;">**使用的变量**</mark>。格式为：**ARG \<name>\[=\<default\_value>]**。

在执行<mark style="color:blue;">**docker build**</mark>时，可以通过<mark style="color:blue;">**--build-arg**</mark>来为变量赋值。当镜像编译成功后，ARG指定的变量将不再存在（ENV指定的变量将在镜像中保留）。

{% hint style="info" %}
<mark style="color:blue;">**Docker内置了一些镜像创建变量，用户可以直接使用而无须声明，包括（不区分大小写）HTTP\_PROXY、HTTPS\_PROXY、FTP\_PROXY、NO\_PROXY。**</mark>
{% endhint %}
