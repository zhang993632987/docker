# history

使用<mark style="color:blue;">**docker history**</mark>命令可以列出各层的创建信息。

{% code overflow="wrap" %}
```bash
$ docker history ubuntu
IMAGE          CREATED       CREATED BY                                       SIZE      COMMENT
ba6acccedd29   2 years ago   /bin/sh -c #(nop)  CMD ["bash"]                  0B        
<missing>      2 years ago   /bin/sh -c #(nop) ADD file:5d68d27cc15a80653…   72.8MB
```
{% endcode %}
