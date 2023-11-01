# 9.3.1 Extend

Docker Compose的extends属性允许你在不同的文件，甚至是完全不同的项目之间<mark style="color:blue;">**共享公共配置**</mark>。

如果你有几个service需要重用一组公共配置选项，那么Extend服务非常有用。通过Extend，你可以在一个地方定义一组公共服务选项，并从任何地方引用它。您可以引用另一个Compose文件，并选择您希望在自己的应用程序中也使用的服务，并能够根据自己的需要覆盖某些属性。

{% code title="compose.yml" %}
```yaml
services:
  web:
    extends:
      file: common-services.yml
      service: webapp
```
{% endcode %}

这指示Compose重用在文件common-services.yml中定义的web应用服务的配置：

{% code title="common-services.yml" %}
```yaml
services:
  webapp:
    build: .
    ports:
      - "8000:8000"
    volumes:
      - "/data"
```
{% endcode %}

在这种情况下，Docker Compose执行的compose.yml文件内容大致如下：

```yaml
services:
  web:
    build: .
    ports:
      - "8000:8000"
    volumes:
      - "/data"
```

{% hint style="warning" %}
<mark style="color:red;">**Volumes\_from和depends\_on永远不会在使用extends的服务之间共享。**</mark>

这是为了避免<mark style="color:blue;">**隐式依赖**</mark>**，**这样可以确保在读取当前文件时清楚地看到服务之间的依赖关系。
{% endhint %}
