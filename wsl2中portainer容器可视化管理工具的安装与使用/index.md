# wsl2中Portainer容器可视化管理工具的安装与使用


## 前言

Portainer是一款轻量级的Docker图形化管理的方案，相比k8s和k3s，只需要创建一个容器！

**功能点：**

- 多平台支持：Linux/Windows/OSX
- 控制面板分类展示
- 技术栈分类展示：使用swarm或docker-compose等启动的服务/集群会在这里出现
- 管理容器生命周期
- 应用模板快速部署
- 管理Swarm集群或多服务器的镜像/容器/卷/网络
- 操作事件日志记录
- 容器控制台日志展示
- 使用多个Docker仓库私服pull/push镜像
- 用户权限管理功能（需付费$9.9）

## 安装

[官方文档](https://docs.portainer.io/v/ce-2.9/start/install/server/docker/wsl)

首先，创建 Portainer Server 将用于存储其数据库的卷：

```bash
docker volume create portainer_data
```

然后，下载并安装 Portainer Server 容器:

```bash
docker run -d -p 8000:8000 -p 9000:9000 -p 9443:9443 \
    --name=portainer --restart=always \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v portainer_data:/data \
    portainer/portainer-ce:2.11.1
```

查看是否启动

```bash
➜  ~ docker ps
CONTAINER ID   IMAGE     COMMAND      CREATED          STATUS   PORTS   NAMES
bb2b73d4a4a6   portainer/portainer-ce:2.11.1   "/portainer"             2 hours ago      Up About an hour   0.0.0.0:8000->8000/tcp, :::8000->8000/tcp, 0.0.0.0:9443->9443/tcp, :::9443->9443/tcp, 9000/tcp   portainer
```

登录

```
https://localhost:9443
```

注意:localhost可以改成IP

第一次登录需要初始化环境，创建超级管理员用户名和密码。

![portainer1](/postimages/portainer1.webp)

进入后会自动扫描容器环境

![portainer2](/postimages/portainer2.webp)

升级参考[官方文档](https://docs.portainer.io/v/ce-2.11/start/upgrade/docker)

