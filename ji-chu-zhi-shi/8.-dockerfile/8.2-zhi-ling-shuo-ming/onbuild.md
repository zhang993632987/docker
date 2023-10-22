# ONBUILD

<mark style="color:blue;">**指定当基于所生成镜像创建子镜像时，自动执行的操作指令**</mark>。格式为**ONBUILD \[INSTRUCTION]**。

例如，使用如下的Dockerfile创建**父镜像ParentImage**，指定**ONBUILD**指令：

```docker
# Dockerfile for ParentImage
[...]
ONBUILD ADD . /app/src
ONBUILD RUN /usr/local/bin/python-build --dir /app/src
[...]

```

**使用docker build命令创建子镜像ChildImage时（FROM ParentImage），会首先执行ParentImage中配置的ONBUILD指令**：

```docker
# Dockerfile for ChildImage
FROM ParentImage
```

{% hint style="warning" %}
<mark style="color:orange;">**由于ONBUILD指令是隐式执行的，推荐在使用它的镜像标签中进行标注，例如ruby:2.1-onbuild。**</mark>
{% endhint %}

{% hint style="info" %}
<mark style="color:blue;">**ONBUILD指令在创建专门用于自动编译、检查等操作的基础镜像时，十分有用。**</mark>
{% endhint %}
