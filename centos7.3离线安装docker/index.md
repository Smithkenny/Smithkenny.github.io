# centos7.3离线安装docker

## 离线安装

```sh
[root@HZ ~]$ uname -a 
Linux HZ 3.10.0-514.el7.centos.x86_64 #1 SMP Fri Jan 13 15:16:57 CST 2017 x86_64 x86_64 x86_64 GNU/Linux
CentOS Linux release 7.3.1611 (Core)
```

### 离线包下载

```
https://download.docker.com/linux/static/stable/x86_64/
在这里我选在下载的版本是：docker-20.10.9.tgz 
```

**上传到服务器上**

### 开始安装

创建数据存储目录，如果不创建默认是`/var/lib/docker`

```bash
mkdir -p /tmpdisk/docker
```

### 创建解压目录

```bash
mkdir -p /tmpdisk/docker20.10.9
```

### 解压离线docker安装包

```bash
tar -zxvf docker-20.10.9.tgz -C /tmpdisk/docker20.10.9
```

### 进入解压目录

```bash
cd /tmpdisk/docker20.10.9/docker/bin
cp * /usr/bin
```

### 创建开机自启文件

```bash
vi /etc/systemd/system/docker.service
```

### 输入以下内容

```bash
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target
[Service]
Type=notify
ExecStart=/usr/bin/dockerd --selinux-enabled=false --insecure-registry=127.0.0.1
ExecReload=/bin/kill -s HUP $MAINPID
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
TimeoutStartSec=0
Delegate=yes
KillMode=process
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s
[Install]
WantedBy=multi-user.target
```

### 赋予文件执行权限

```bash
chmod +x /etc/systemd/system/docker.service
```

### 重新加载配置

```bash
systemctl daemon-reload
```

### 设为开机启动

```bash
systemctl enable docker.service
```

### 启动docker

```sh
systemctl start docker.service
```

### 更改docker默认存储路径，新建文件`daemon.json`

```sh
vi /etc/docker/daemon.json
{
  "storage-driver": "vfs",
"graph": "/tmpdisk/docker"
}
```

### 赋予权限

```sh
chmod 755 /etc/docker/daemon.json
```

重启docker服务,如果多次启动失败，检查`daemon.json`配置文件是否有多余字符。可以使用`systemctl reset-failed docker.service`重置失败。

## 错误排查

### 安装mysql57容器

```sh
docker pull mysql:5.7
docker run -itd --name mysql2 --restart=always  -p 13306:3306 -e MYSQL_ROOT_PASSWORD=123456 -v /etc/localtime:/etc/localtime:ro -v /tmpdisk/mysqldata:/var/lib/mysql  mysql:5.7
```

### 启动时报错

```sh
failed to register layer: ApplyLayer exit status 1 stdout:  stderr: open /etc/.pwd.lock: no such device or address
```

**更改存储驱动,重启docker**

```sh
vi /etc/docker/daemon.json
{
"storage-driver": "vfs"
}
```

**报错2**

```sh
write /var/lib/docker/vfs/dir/3bf1a7e9505dff94e1c4ba0293a90e1d17c1b5b4318ec7a355f74185f421c240/usr/sbin/mysqld: no space left on device
```

**更改docker默认存储路径**

```sh
vi /etc/docker/daemon.json
{
  "storage-driver": "vfs",
"data-root": "/tmpdisk/mysql5.7"
}
```

### 安装tomcat9.0容器

**创建项目路径，将容器内部路径映射进来。**

```sh
docker pull tomcat9.0
docker run -itd -p 18080:8080 --restart=always --name tomcat04 -v /tmpdisk/tomcat9.0.56/webapps:/usr/local/tomcat/webapps -v /etc/localtime:/etc/localtime:ro  tomcat:9.0.56
```

**进入容器，启动项目时报错**

```sh
# Cannot create worker GC thread. Out of system resources.
# An error report file with more information is saved as:
# /usr/local/tomcat/bin/hs_err_pid46.log
NOTE: Picked up JDK_JAVA_OPTIONS:  --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.io=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.util.concurrent=ALL-UNNAMED --add-opens=java.rmi/sun.rmi.transport=ALL-UNNAMED
[0.002s][warning][os,thread] Failed to start thread "GC Thread#0" - pthread_create failed (EPERM) for attributes: stacksize: 1024k, guardsize: 4k, detached.
#
# There is insufficient memory for the Java Runtime Environment to continue.
# Cannot create worker GC thread. Out of system resources.
# An error report file with more information is saved as:
# /usr/local/tomcat/bin/hs_err_pid68.log
```

**解决方法，更换tomcat镜像。**

```sh
docker pull tomcat9.0.56
重新创建容器
docker run -itd -p 18080:8080 --name tomcat05 -v /tmpdisk/tomcat9.0.56/webapps:/usr/local/tomcat/webapps tomcat:9.0.56
```

后续可以添加tomcat项目的war包到宿主机目录下，包会自动加载。

## 整体访问流程

tomcat项目访问mysql数据库

## 容器内时区同步

启动容器时添加以下参数，会和宿主机保持一致。

```sh
-v /etc/localtime:/etc/localtime:ro 
```

**查看所有在线的容器IP**

````sh
docker inspect -f='{{.Name}} {{.NetworkSettings.IPAddress}} {{.HostConfig.PortBindings}}' $(docker ps -aq)
````

docker 镜像释放报错

```
docker load -i mysql.tgz 
open /etc/.pwd.lock: no such device or address
```

原因是docker 所在安装目录硬盘空间不足。更改docker硬盘安装路径后正常了。

docker 离线安装包：

```bash
https://download.docker.com/linux/static/stable/x86_64/
```

docker-compose 安装包

下载`docker-compose-linux-x86_64`

```bash
https://github.com/docker/compose/releases 
```

