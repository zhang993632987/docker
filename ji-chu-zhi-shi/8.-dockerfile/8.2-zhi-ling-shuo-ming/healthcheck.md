# HEALTHCHECK

<mark style="color:blue;">**配置所启动容器如何进行健康检查（如何判断健康与否）**</mark>。

格式有两种：

*   **HEALTHCHECK \[OPTIONS] CMD command**：根据所执行命令返回值是否为0来判断；

    OPTION支持如下参数：

    * \--interval=DURATION (default: 30s)：过多久检查一次；
    * \--timeout=DURATION (default: 30s)：每次检查等待结果的超时；
    * \--retries=N (default: 3)：如果失败了，重试几次才最终确定失败。
* **HEALTHCHECK NONE**：禁止基础镜像中的健康检查。
