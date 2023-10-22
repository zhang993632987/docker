# 8.2 指令说明

RUNDockerfile中指令的一般格式为**INSTRUCTION arguments**，包括“**配置指令**”（配置镜像信息）和“**操作指令**”（具体执行操作）：

{% tabs %}
{% tab title="配置指令" %}
| 指令                            | 说明                |
| ----------------------------- | ----------------- |
| [ARG](arg.md)                 | 定义创建镜像过程中使用的变量    |
| [FROM](from.md)               | 指定所创建镜像的基础镜像      |
| [LABEL](label.md)             | 为生成的镜像添加元数据标签信息   |
| [EXPOSE](expose.md)           | 声明镜像内服务监听的端口      |
| [ENV](env.md)                 | 指定环境变量            |
| [ENTRYPOINT](entrypoint.md)   | 指定镜像的默认入口命令       |
| [VOLUME](volume.md)           | 创建一个数据挂载卷         |
| [USER](user.md)               | 指定运行容器时的用户名或UID   |
| [WORKDIR](workdir.md)         | 配置工作目录            |
| [ONBUILD](onbuild.md)         | 创建子镜像时指定自动执行的操作指令 |
| [STOPSIGNAL](stopsignal.md)   | 指定退出的信号值          |
| [HEALTHCHECK](healthcheck.md) | 配置所启动容器如何进行健康检查   |
| [SHELL](shell.md)             | 指定默认shell类型       |
{% endtab %}

{% tab title="操作指令" %}
| 指令              | 说明             |
| --------------- | -------------- |
| [RUN](run.md)   | 运行指定命令         |
| [CMD](cmd.md)   | 启动容器时指定默认执行的命令 |
| [ADD](add.md)   | 添加内容到镜像        |
| [COPY](copy.md) | 复制内容到镜像        |
{% endtab %}
{% endtabs %}
