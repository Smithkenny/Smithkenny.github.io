# Apache开源项目seatunnel及web搭建


网上看到的适合新手的部署教程，特此记录下。

[教程](https://blog.csdn.net/qq_41865652/article/details/134574104)

### 遇到的问题

#### 1.启动web进程后，浏览器访问404。

##### 1.1 一定要在bin的上级目录执行启动脚本。

比如：web项目的启动脚本目录是：`/appdata/apache-seatunnel-web-1.0.0/bin`,需要在他的上级目录内执行`/appdata/apache-seatunnel-web-1.0.0`，才能访问。

##### 1.2 解压后的web包名称需要重新命名

官网下载的源码包`apache-seatunnel-2.3.3-bin.tar.gz`

解压后重新命名为：`apache-seatunnel-2.3.3`




