# 使用脚本获取本机IP并复制到registry配置文件中

## 前言

由于wsl2每次启动IP都会改变，查阅微软官方文档后发现wsl2无法设置静态IP。然而dockers私有仓库registry中IP配置是固定的。所以需要自定义脚本实时获取wsl2本机IP，并复制到/etc/docker/daemon.json中对应的IP位置。还需要创建系统服务启动脚本。以下内容均在ubuntu 20.04 LTS中实现。

## 思路

要想实现需求，可以使用bash中多脚本之间变量传递。[参考](https://blog.csdn.net/xixihahalelehehe/article/details/104935683?spm=1035.2023.3001.6557&utm_medium=distribute.pc_relevant_bbs_down_v2.none-task-blog-2~default~OPENSEARCH~Rate-2.pc_relevant_bbs_down_v2_default&depth_1-utm_source=distribute.pc_relevant_bbs_down_v2.none-task-blog-2~default~OPENSEARCH~Rate-2.pc_relevant_bbs_down_v2_default)

### 获取wsl2本机IP

vi /root/init_code/get-ip.sh && chmod +x /root/init_code/get-ip.sh

```bash
var=`hostname -I | awk '{print $1}'`
echo $var
```

### 查看文本格式

cat /etc/docker/daemon.json 

```json
{
  "registry-mirrors": ["https://8xpk5wnt.mirror.aliyuncs.com"],
  "insecure-registries": ["172.26.133.115:5000"]
}
```

### 创建脚本来更改daemon.json中对应IP

vi chang-daemon.sh && chmod +x chang-daemon.sh

```bash
#!/bin/bash
source /root/init_code/get-ip.sh
echo "{
  \"registry-mirrors\": [\"https://8xpk5wnt.mirror.aliyuncs.com\"],
  \"insecure-registries\": [\"$var:5000\"]
}
" > /etc/docker/daemon.json
```

在脚本2中使用source 来引用脚本1中的变量。如何打印出双引号？双引号前加反斜杠。

例如：

打印出"Hello World!"

```bash
echo "\"Hello World!"\"
```

### 创建开机自启动脚本

vi /root/init_code/init_myservice.sh 

```bash
#启动ssh服务和docker服务
sudo service ssh restart
sudo service docker start
```

### 创建查看仓库中镜像脚本

vi /root/init_code/watch-myregistry.sh

```bash
#!/bin/bash
source /root/init_code/get-ip.sh
curl -XGET http://$var:5000/v2/_catalog
```

搭建私有仓库命令

```bash
docker run -d --name registry -v /opt/registry:/var/lib/registry -p 5000:5000 --restart=always registry
```

## 附加

wsl2中使用systemctl启动服务报错如下

```
➜  ~ systemctl restart docker
System has not been booted with systemd as init system (PID 1). Can't operate.
Failed to connect to bus: Host is down
```

wsl2中默认使用System V init 来管理系统服务，而不是systemd。所以不能运行systemd中的命令。

**Systemd command**和**Sysvinit command**命令对照表

|      **Systemd command**       |     **Sysvinit command**     |
| :----------------------------: | :--------------------------: |
|  systemctl start service_name  |  service service_name start  |
|  systemctl stop service_name   |  service service_name stop   |
| systemctl restart service_name | service service_name restart |
| systemctl status service_name  | service service_name status  |
| systemctl enable service_name  |  chkconfig service_name on   |
| systemctl disable service_name |  chkconfig service_name off  |

wsl2中使用chkconfig  命令设置服务开机自启动没效果，所以最后还是使用脚本来实现。

查看当前系统服务管理机制

使用sysvinit管理服务

```bash
➜  ~ ps -p 1 -o comm=
init
```

使用systemd管理服务

```bash
[root@localhost ~]# ps -p 1 -o comm=
systemd
```


