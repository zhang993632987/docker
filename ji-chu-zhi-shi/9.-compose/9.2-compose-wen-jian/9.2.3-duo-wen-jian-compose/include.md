# Include

使用顶层元素 "<mark style="color:blue;">**include**</mark>"，您可以直接在本地Compose文件中包含一个独立的Compose文件。这解决了[**Extend**](extend.md)和[**Merge**](merge.md)中存在的<mark style="color:orange;">**相对路径问题**</mark>。

"include"使得将复杂应用模块化为子Compose文件变得更加容易。这可以使应用程序配置更加简单和明确。这也有助于在配置文件组织中反映出负责代码的工程团队。

<mark style="color:blue;">**在 "include" 部分列出的每个路径都会加载为一个独立的Compose应用程序模型，具有自己的项目目录。**</mark>

一旦包含的Compose应用程序加载，所有资源都会复制到当前Compose应用程序模型中。

```yaml
include:
  - my-compose-include.yaml  #with serviceB declared
services:
  serviceA:
    build: .
    depends_on:
      - serviceB #use serviceB directly as if it was declared in this Compose file
```

"my-compose-include.yaml" 管理着 "serviceB"，其中详细说明了一些复制品、用于检查数据的Web用户界面、隔离的网络、用于数据持久性的卷等等。依赖于 "serviceB" 的应用程序无需知道基础设施的详细信息，并将Compose文件视为可靠的构建模块。

这意味着管理 "serviceB" 的团队可以重新设计其自己的数据库组件，以引入附加服务，而不会影响任何依赖的团队。这也意味着依赖的团队无需在运行每个Compose命令时包含附加标志。
