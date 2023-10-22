# 安装tomcat

**进入**[**docker hub**](https://hub-stage.docker.com/)**网站，搜索tomcat的镜像**

选择官方认证的排在前面的镜像，点击进入详情页面，通过 tag 选择版本号

阅读页面中How to use this image.

## **拉取镜像：**

```bash
$ docker pull tomcat[:tag]
```

## **启动容器**

```bash
$ docker run --name tomcat -d -p 8080:8080 tomcat[:tag]
```

## **测试**

在上述设置下，宿主机的8080端口将作为进入tomcat服务器的入口，若宿主机的ip地址为222.20.77.242，在浏览器中输入http:\\\222.20.77.242:8080。

{% hint style="warning" %}
注意：

此时浏览器中展示的应该是404错误异常页面，这是因为tomcat容器的webapps是一个空的文件夹，其中的内容被移动到了webapps.dist中，若想要展示tomcat的欢迎界面，可以执行以下命令：

<pre class="language-bash"><code class="lang-bash"><strong>$ docker exec tomcat docker exec tomcat \
</strong><strong>    sh -c "mv webapps webapp_bak;mv webapps.dist webapps"
</strong></code></pre>
{% endhint %}
