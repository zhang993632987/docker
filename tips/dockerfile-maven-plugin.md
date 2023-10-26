# dockerfile-maven-plugin

**使用该插件可以在mvn命令中直接构建出Docker镜像并完成推送**。

## 1. Goals

| Goal               | 描述                                       | Default Phase |
| ------------------ | ---------------------------------------- | ------------- |
| `dockerfile:build` | Builds a Docker image from a Dockerfile. | package       |
| `dockerfile:tag`   | Tags a Docker image.                     | package       |
| `dockerfile:push`  | Pushes a Docker image to a repository.   | deploy        |

执行顺序依次为：

1. package&#x20;
2. dockerfile:build&#x20;
3. verify&#x20;
4. dockerfile:push&#x20;
5. deploy

{% hint style="info" %}
根据上述内容可知，并不需要添加额外的配置将插件的goal与maven的执行阶段进行绑定，默认的绑定规则已能够满足使用需求。

<mark style="color:blue;">**执行mvn package即可以完成项目打包，又能够完成镜像构建。**</mark>
{% endhint %}

## 2. 开启docker远程访问

{% content-ref url="docker-yin-qing-kai-qi-yuan-cheng-fang-wen.md" %}
[docker-yin-qing-kai-qi-yuan-cheng-fang-wen.md](docker-yin-qing-kai-qi-yuan-cheng-fang-wen.md)
{% endcontent-ref %}

## 3. 环境变量DOCKER\_HOST

添加环境变量<mark style="color:blue;">**DOCKER\_HOST=tcp://192.168.10.110:2375**</mark>，只有指定该环境变量，才能改变插件默认使用本地的docker引擎的默认行为，利用远程docker引擎进行镜像构建。
