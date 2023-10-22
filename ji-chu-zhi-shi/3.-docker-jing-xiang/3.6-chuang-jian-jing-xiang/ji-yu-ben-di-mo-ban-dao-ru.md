# 基于本地模板导入

用户也可以直接从一个操作系统模板文件导入一个镜像，主要使用<mark style="color:blue;">**docker \[container] import**</mark>命令。命令格式为**docker \[image] import \[OPTIONS] file|URL|- \[REPOSITORY \[:TAG]]。**

要直接导入一个镜像，可以使用OpenVZ提供的模板来创建，或者用其他已导出的镜像模板来创建。OPENVZ模板的下载地址为http://openvz.org/Download/templates/precreated。

<pre class="language-bash"><code class="lang-bash"><strong>$ wget http://download.openvz.org/template/precreated/ubuntu-12.04-x86.tar.gz
</strong>
$ ls
ubuntu-12.04-x86.tar.gz

$ cat ubuntu-12.04-x86.tar.gz | docker import - ubuntu:12.04
sha256:40b2868f4156ea59fb9f7ce232a5554941e5ec022aa3302fa81292364c324812

$ docker images
REPOSITORY       TAG       IMAGE ID       CREATED          SIZE
ubuntu           12.04     40b2868f4156   40 seconds ago   322MB
</code></pre>

