# CMD

<mark style="color:blue;">**CMD指令用来指定启动容器时默认执行的命令**</mark>。

支持三种格式：

* **CMD \["executable", "param1", "param2"]**：相当于执行executable param1 param2，推荐方式；
* **CMD command param1 param2**：在默认的Shell中执行，提供给需要交互的应用；
* **CMD \["param1", "param2"]**：提供给ENTRYPOINT的默认参数。

{% hint style="info" %}
<mark style="color:blue;">**每个Dockerfile只能有一条CMD命令。如果指定了多条命令，只有最后一条会被执行。**</mark>
{% endhint %}

{% hint style="info" %}
如果用户启动容器时候手动指定了运行的命令（作为run命令的参数），则会覆盖掉CMD指定的命令。
{% endhint %}
