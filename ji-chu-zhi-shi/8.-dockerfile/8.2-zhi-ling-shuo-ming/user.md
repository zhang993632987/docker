# USER

<mark style="color:blue;">**指定运行容器时的用户名或UID，后续的RUN等指令也会使用指定的用户身份**</mark>。格式为**USER daemon**。

当服务不需要管理员权限时，可以通过该命令指定运行用户，并且可以在Dockerfile中创建所需要的用户。
