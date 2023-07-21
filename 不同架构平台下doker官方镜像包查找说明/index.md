# 不同架构平台下doker官方镜像包查找说明


### 问题出现的原因

我的服务器的平台架构是IBM-Z无法拉取默认的x86平台的镜像，所以需要到docker-hub上找到IBM-Z对应的镜像才能使用。

首先进入[Docker Hub](https://registry.hub.docker.com/search?q=&type=image)查找所需镜像
例如我需要下载mysql镜像的IBM-Z版本

搜索mysql ，左侧筛选IBM Z.

![不同平台镜像拉取1](/postimages/不同平台镜像拉取1.webp)

选择ibmcom/mysql-s390x

![不同平台镜像拉取2](/postimages/不同平台镜像拉取2.webp)

选择tags

![不同平台镜像拉取3](/postimages/不同平台镜像拉取3.webp)

复制5.7.34版本的下载路径，粘贴到服务器中即可下载。






