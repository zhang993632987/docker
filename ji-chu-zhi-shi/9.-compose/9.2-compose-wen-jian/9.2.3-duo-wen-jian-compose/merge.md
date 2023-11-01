# 9.3.2 Merge

默认情况下，Compose 会读取两个文件，一个是<mark style="color:blue;">**compose.yml**</mark>，另一个是可选的<mark style="color:blue;">**compose.override.yml**</mark>文件。按照惯例，compose.yml 包含了基本配置信息，而 override 文件可以包含现有服务的配置覆盖或全新服务的配置信息。

要使用多个配置覆盖文件或具有不同名称的配置覆盖文件，您可以使用 **-f** 选项来指定文件列表。Compose 会按照在命令行中指定它们的顺序合并这些文件。

{% hint style="info" %}
<mark style="color:blue;">**合并规则：**</mark>

Compose 会将原始服务的配置复制到本地服务。如果一个配置选项在原始服务和本地服务中都有定义，本地值会替代或扩展原始值。

* <mark style="color:blue;">**对于像 image、command 或 mem\_limit 这样的单值选项，新值会替代旧值。**</mark>
* <mark style="color:blue;">**对于多值选项如 ports、expose、external\_links、dns、dns\_search 和 tmpfs，Compose 会将两组值连接在一起。**</mark>
*   <mark style="color:blue;">**对于 environment、labels、volumes 和 devices，Compose 会将条目"合并"在一起，本地定义的值具有优先权。对于 environment 和 labels，环境变量或标签的名称决定了使用哪个值：**</mark>

    {% code title="original（原始服务）" %}
    ```yaml
    services:
      myservice:
        # ...
        environment:
          - FOO=original
          - BAR=original
    ```
    {% endcode %}

    {% code title="local（本地服务）" %}
    ```yaml
    services:
      myservice:
        # ...
        environment:
          - BAR=local
          - BAZ=local
    ```
    {% endcode %}

    {% code title="result（结果）" %}
    ```yaml
    services:
      myservice:
        # ...
        environment:
          - FOO=original
          - BAR=local
          - BAZ=local
    ```
    {% endcode %}
*   <mark style="color:blue;">**对于 volumes 和 devices 条目，将使用容器中的挂载路径进行合并**</mark><mark style="color:blue;">：</mark>

    {% code title="original（原始服务）" %}
    ```
    services:
      myservice:
        # ...
        volumes:
          - ./original:/foo
          - ./original:/bar
    ```
    {% endcode %}

    {% code title="local（本地服务）" %}
    ```yaml
    services:
      myservice:
        # ...
        volumes:
          - ./local:/bar
          - ./local:/baz
    ```
    {% endcode %}

    {% code title="result（结果）" %}
    ```yaml
    services:
      myservice:
        # ...
        volumes:
          - ./original:/foo
          - ./local:/bar
          - ./local:/baz
    ```
    {% endcode %}
{% endhint %}

{% hint style="danger" %}
<mark style="color:orange;">**当您使用多个Compose文件时，必须确保文件中的所有路径都相对于基础Compose文件（即使用 -f 指定的第一个Compose文件）而言。**</mark>

**Docker Compose支持许多资源的相对路径**，这些资源包括服务镜像的构建上下文、定义环境变量的文件位置以及绑定挂载卷中使用的本地目录路径。在这种情况下，对于在monorepo中进行代码组织，一个自然的选择是为每个团队或组件创建专用文件夹，但这会导致Compose文件中的相对路径变得不相关。
{% endhint %}
