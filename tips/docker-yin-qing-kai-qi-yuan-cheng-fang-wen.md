# docker引擎开启远程访问

1.  修改**/lib/systemd/system/docker.service**：

    ```bash
    $ sudo vim /lib/systemd/system/docker.service
    ```

    在**dockerd**命令后增加**-H tcp://0.0.0.0:2375**

    ```perl
    ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock
    ```
2.  重启docker服务：

    ```bash
    $ sudo systemctl daemon-reload
    $ sudo systemctl restart docker
    ```
3.  校验：

    {% code overflow="wrap" %}
    ```bash
    $ curl http://localhost:2375/info
    $ docker -H 192.168.10.110:2375 images
    ```
    {% endcode %}
