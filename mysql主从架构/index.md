# mysql主从架构


### 实验环境

```Shell
[root@hdss7-11 ~]# uname -r 
3.10.0-1062.el7.x86_64
[root@hdss7-11 ~]# cat /etc/redhat-release 
CentOS Linux release 7.7.1908 (Core)
[root@hdss7-11 ~]# 
```

master 10.4.7.11

slave1 10.4.7.12

slave2 10.4.7.21

### 安装mysql5.7

#### 查询并删除旧的MySQL(没有可以忽略)

````Shell
[root@hdss7-11 ~]# rpm  -qa |grep mysql
php-mysql-5.4.16-48.el7.x86_64
mysql57-community-release-el7-9.noarch

[root@hdss7-11 ~]# rpm  -e php-mysql-5.4.16-48.el7.x86_64
````

#### 下载MySQL源

```Shell
[root@hdss7-11 ~]# wget https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
```

#### repo的安装

```Shell
[root@hdss7-11 ~]# rpm -ivh mysql57-community-release-el7-9.noarch.rpm 

warning: mysql57-community-release-el7-9.noarch.rpm: Header V3 DSA/SHA1 Signature, key ID 5072e1f5: NOKEY
Preparing...                          ################################# [100%]
Updating / installing...
   1:mysql57-community-release-el7-9  ################################# [100%]
```

#### 开始安装MySQL

```Shell
yum -y install mysql-community-server
```

#### 启动mysql并查看状态

```Shell
systemctl start  mysqld.service
systemctl status  mysqld.service
```

● mysqld.service - MySQL Server
   Loaded: **loaded** (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: active (running) since Mon 2022-01-03 18:12:46 CST; 15s ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
  Process: 3241 ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid $MYSQLD_OPTS (code=exited, status=0/SUCCESS)
  Process: 3187 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
 Main PID: 3244 (mysqld)
   CGroup: /system.slice/mysqld.service
           └─3244 /usr/sbin/mysqld --daemonize --pid-file=/var/run/m...

Jan 03 18:12:41 hdss7-11.host.com systemd[1]: Starting MySQL Server...
Jan 03 18:12:46 hdss7-11.host.com systemd[1]: Started MySQL Server.
Hint: Some lines were ellipsized, use -l to show in full.

#### 查看版本

```Shell
mysql -V
```

mysql  Ver 14.14 Distrib 5.7.36, for Linux (x86_64) using  EditLine wrapper

#### 查找系统默认密码

```Shell
cd /var/log
grep -e "password" mysqld.log

A temporary password is generated for root@localhost: kaNNUw-2y,Gc
```



#### 设置新密码，密码修改为IThaipeng123.

```Shell
mysql -uroot -p 
输入密码kaNNUw-2y,Gc


mysql> alter user 'root'@'localhost' identified by 'IThaipeng123.';
Query OK, 0 rows affected (0.01 sec)
```

**如果有以下报错说明密码强度不够，需要加特殊字符。**

mysql> alter user 'root'@'localhost' identified by 'IThaipeng123';
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements

**注意：三台服务器安装方法相同，这里只给出一台的说明。**

三台MySQL搭建好之后，需要把时间同步开启，防火墙关闭

#### NTP配置

NTP服务端：10.4.7.11

NTP客户端：10.4.7.12 10.4.7.21 

**安装ntp服务（三台都需安装）**

```Shell
yum install -y ntp
```

查看ntp安装包

```Shell
[root@hdss7-11 ~]# rpm -qa |grep ntp
ntp-4.2.6p5-29.el7.centos.2.x86_64
ntpdate-4.2.6p5-29.el7.centos.2.x86_64
```

7.11上修改NTP配置文件

```Shell
vim /etc/ntp.conf
把配置文件下面四行注释掉：
server 0.centos.pool.ntp.org iburst
server 1.centos.pool.ntp.org iburst
server 2.centos.pool.ntp.org iburst
server 3.centos.pool.ntp.org iburst
然后在下面添加这几行：
server 0.cn.pool.ntp.org iburst
server 1.cn.pool.ntp.org iburst
server 2.cn.pool.ntp.org iburst
server 3.cn.pool.ntp.org iburst
```

启动ntp服务，并开机自启

```Shell
systemctl start ntpd
systemctl enable ntpd
```

查看ntp是否同步

```Shell
[root@hdss7-11 ~]# ntpq -p
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
 ntp5.flashdance 194.58.202.20    2 u    1   64    1  318.796   49.178   1.689
 time.cloudflare 10.4.3.52        3 u    2   64    1  183.991   28.217   4.578
 ntp6.flashdance 194.58.202.148   2 u    1   64    1  331.954   46.528   5.128
 139.199.214.202 100.122.36.196   2 u    2   64    1   72.218   10.343   0.000
```

防火墙开启ntp默认端口udp123(可选，如果没开启防火墙则不用操作此步)

```Shell
[root@hdss7-11 ~]# firewall-cmd --permanent --zone=public --add-port=123/udp
success
[root@hdss7-11 ~]# firewall-cmd --reload
success
```

NTP 客户端配置

7.12 7.21 上修改ntp配置文件

```Shell
vim /etc/ntp.conf
#配置允许NTP Server时间服务器主动修改本机的时间
restrict 10.4.7.11 nomodify notrap noquery
#注释掉其他时间服务器
#server 0.centos.pool.ntp.org iburst
#server 1.centos.pool.ntp.org iburst
#server 2.centos.pool.ntp.org iburst
#server 3.centos.pool.ntp.org iburst
#配置时间服务器为本地搭建的NTP Server服务器
server 10.4.7.11
```

#### 同步主服务器(7.12 7.21 上操作）

```Shell
[root@hdss7-21 ~]# ntpdate -u 10.4.7.11
 3 Jan 10:53:42 ntpdate[6675]: adjust time server 10.4.7.11 offset 0.015245 sec
```

#### 查看NTP同步状态

```Shell
[root@hdss7-21 ~]# ntpq -p
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
 10.4.7.11       139.199.214.202  3 u    1   64    7    0.327   -6.267  15.285

```

### 配置MySQL master主服务器

#### 在/etc/my.cnf 中修改或者增加以下内容

```Shell
[root@hdss7-11 ~]# tail -n 3 /etc/my.cnf
server-id = 11
log-bin=master-bin
log-slave-updates=true
```

#### 重启mysql服务

```Shell
systemctl  restart mysqld
```

#### 登录MySQL程序，给服务器授权

```Shell
mysql -uroot -p
输入密码

mysql>grant replication slave on *.* to 'myslave'@'10.4.7.%' identified by 'Haipengv123456?';
Query OK, 0 rows affected, 1 warning (0.00 sec)
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)

mysql> show master status;
+-------------------+----------+--------------+------------------+-------------------+
| File              | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+-------------------+----------+--------------+------------------+-------------------+
| master-bin.000001 |      599 |              |                  |                   |
+-------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)

```

file列显示的日志名，position 列显示偏移量。

### 配置从服务器

7-12  server-id = 22

7-21 server-id = 23

#### 在/etc/my.cnf 中修改或者增加以下内容：

````Shell
[root@hdss7-12 ~]# tail -n 3 /etc/my.cnf
server-id = 22   ## 这里的ID不能与主服务器相同，也不能和其他从服务器相同
relay-log=relay-log-bin  ## 从主服务器上同步日志到本地
relay-log-index=slave-relay-bin.index  ## 定义relay-log的位置和名称
````

#### 重启从服务器的MySQL

登录mysql

```Shell
# mysql -uroot -p
Enter password:
```

配置同步

根据主服务器的结果来更改下面的master_log_file 和master_log_post 的参数

```Shell
mysql> change master to master_host='10.4.7.11',master_user='myslave',master_password='Haipengv123456?',master_log_file='master-bin.000001',master_log_pos=599;

Query OK, 0 rows affected, 2 warnings (0.02 sec)
```

#### 启动同步

```Shell
mysql> start slave;
Query OK, 0 rows affected (0.35 sec)

mysql> 
```

#### 查看slave确保两个值都为yes

```Shell
mysql> show slave status \G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 10.4.7.11
                  Master_User: myslave
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: master-bin.000001
          Read_Master_Log_Pos: 599
               Relay_Log_File: relay-log-bin.000002
                Relay_Log_Pos: 321
        Relay_Master_Log_File: master-bin.000001
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
```

### 验证主从复制结果

#### 在主服务器上创建数据库

10.4.7.11 主服务器

```Shell
mysql> create database db_test;
Query OK, 1 row affected (0.00 sec)
```

#### 查看主从服务器的数据库是否同步

主服务器：

```Shell
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| db_test            |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
```

slave1

```Shell
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| db_test            |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
```

slave2

```Shell
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| db_test            |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
```

### 拓展server-id的用途

`server-id`用于标识数据库实例，防止在链式主从、多主多从拓扑中导致`SQL`语句的无限循环：

1. 标记`binlog event`的源实例
2. 过滤主库`binlog`，当发现`server-id`相同时，跳过该`event`执行，避免无限循环执行。
3. 如果设置了`replicate-same-server-id=1`，则执行所有`event`，但有可能导致无限循环执行`SQL`语句。

我们用两个例子来说明`server-id`为什么不要重复：

### 当主库和备库`server-id`重复时

由于默认情况`replicate-same-server-id=0`，因此备库会跳过所有主库同步的数据，导致主从数据的不一致。

### 当两个备库`server-id`重复时

会导致从库跟主库的连接时断时连，产生大量异常。根据`MySQL`的设计，主库和从库通过事件机制进行连接和同步，当新的连接到来时，如果发现`server-id`相同，主库会断开之前的连接并重新注册新连接。当`A`库连接上主库时，此时`B`库连接到来，会断开`A`库连接，`A`库再进行重连，周而复始导致大量异常信息。

### 生成`server-id`的规则

既然`server-id`不能相同，而当我们有`10`个实例时，怎么保证每个都不同呢？有几种常用的方法：

1. 随机数
2. 时间戳
3. `IP`地址+端口
4. 在管理中心集中分配，生成自增`ID`

上面的这些方法都可以，但是注意不要超过了最大值`2^32-1`，同时值最好`>2`。我采用的方法是`IP`地址后两位+本机`MySQL`实例序号，但如果是通过`docker`来进行管理多实例时，这个怎么生成大家可以想下有没有什么优美的解决方案。

## `server-uuid`配置

`MySQL`服务会自动创建并生成`server-uuid`配置：

1. 读取`${data_dir}/auto.cnf`文件中的`UUID`
2. 如果不存在，自动创建文件和生成新的`UUID`并读取

```Shell
shell> cat ~/mysql/data/auto.cnf
[auto]
server-uuid=fd5d03bc-cfde-11e9-ae59-48d539355108
```

这个`auto.cnf`配置风格类似于`my.cnf`，但这个文件只包含一个`auto`配置块和一行`server-uuid`配置。它是自动创建的，因此不要修改它的内容。

在主从拓扑中，主从可以知道互相的`UUID`，在主机上使用`show slave hosts`，在从机上使用`show slave status`查看`Master_UUID`字段。

`server-uuid`参数并不能取代`server-id`，他们有不同的作用。当主从同步时如果主从实例的`server-uuid`相同会报错退出，不过我们可以通过设置`replicate-same-server-id=1`来避免报错（不推荐）。

### 参考资料

[参考](https://segmentfault.com/a/1190000020315036)

