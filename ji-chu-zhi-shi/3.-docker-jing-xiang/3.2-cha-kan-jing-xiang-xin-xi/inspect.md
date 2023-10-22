# inspect

使用<mark style="color:blue;">**docker \[image] inspect**</mark>命令可以获取该镜像的详细信息，包括制作者、适应架构、各层的数字摘要等：

```bash
$ docker inspect ubuntu
```

上面代码返回的是一个**JSON**格式的消息，如果我们只要其中一项内容时，可以使用**-f**来指定，例如，获取镜像的Architecture：

```bash
$ docker inspect -f {{".Architecture"}} ubuntu
amd64
```
