# tag

为了方便在后续工作中使用特定镜像，还可以使用<mark style="color:blue;">**docker tag**</mark>命令来为本地镜像任意添加新的标签。例如，添加一个新的myubuntu:latest镜像标签：

```bash
$ docker tag ubuntu:latest myubuntu:latest
$ docker images
REPOSITORY       TAG       IMAGE ID       CREATED        SIZE
myubuntu         latest    ba6acccedd29   2 years ago    72.8MB
ubuntu           latest    ba6acccedd29   2 years ago    72.8MB
ubuntu           18.04     5a214d77f5d7   2 years ago    63.1MB
```

myubuntu:latest镜像的ID跟ubuntu:latest是完全一致的，它们实际上指向了同一个镜像文件，只是别名不同而已。docker tag命令添加的标签实际上起到了类似链接的作用。
