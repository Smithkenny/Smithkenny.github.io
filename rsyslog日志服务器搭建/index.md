# rsyslog日志服务器搭建


## rsyslog日志服务器搭建

rsyslog客户端：负责发送日志到远程日志服务器，支持udp,tcp,relp协议。

rsyslog服务端：负责接收客户端发来的日志并存储在本地，支持文件存储和数据库存储方式。

保存日志的路径格式为：源IP地址/设施名（日志类别）/年月.log。

#### 安装rsyslog软件

```bash
yum install rsyslog -y 
```

#### 配置rsyslog.conf文件

#### 开放514 tcp 和 udp 监听

```bash
# Provides UDP syslog reception
$ModLoad imudp
$UDPServerRun 514

# Provides TCP syslog reception
$ModLoad imtcp
$InputTCPServerRun 514
```

#### 服务端核心配置

```bash
#### GLOBAL DIRECTIVES ####

# Where to place auxiliary files
$WorkDirectory /var/lib/rsyslog

# Use default timestamp format
$ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat
##这里是服务端添加的配置 begin
# 使用 RemoteLogs 模板接收客户端的日志，保存到本地的/var/log/remote目录下，每台客户端的ip_年份_月份_日期的log
$template RemoteLogs,"/var/log/remote/%fromhost-ip%/%fromhost-ip%_%$YEAR%-%$MONTH%-%$DAY%.log"
# 所有服务所有级别的日志都记录
*.* ?RemoteLogs
#服务端本机的日志不记录
:fromhost-ip, !isequal, "127.0.0.1" ?Remote

#指示rsyslog在将消息写入文件后停止处理消息，如果不包含"& ~",则消息将被写入本地文件，导致消息被记录2次。
& ~
## 这里是服务端添加的配置 end
```

#### 重启rsyslog服务

```shell
systemctl restart rsyslog
```

#### 最后到配置目录下查看是否有日志生成

```shell
[root@localhost 127.0.0.1]# ll
total 4
-rw------- 1 root root 1639 Oct 25 10:01 127.0.0.1_2022-10-25.log
[root@localhost 127.0.0.1]# pwd 
/var/log/remote/127.0.0.1
```

#### rsyslog客户端配置

表示将用户登录日志和定时任务日志发送到192.168.1.74的514端口，默认采用TCP进行发送。

```shell
vi /etc/rsyslog.conf
###rule for 192.168.1.74 ###
auth.*;authpriv.*;cron.*  @@192.168.1.74:514
```




