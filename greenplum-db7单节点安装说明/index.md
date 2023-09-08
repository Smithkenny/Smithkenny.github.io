# Greenplum Db7单节点安装说明


## 一、安装环境

- 系统：Rocky8.8
- 数据库版本：greenplum-db7.0.0-beta5
- 注意：greenplum数据库只能用普通用户运行，不可用root管理员直接运行！！！！

## 二、安装过程

#### 1.Rocky8.8 系统更新

```shell
yum update -y 
```

#### 2.关闭防火墙

```shell
sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
systemctl stop firewalld
systemctl disable firewalld
setenforce 0
reboot
```

#### 3.安装依赖

```sh
yum install -y gcc gcc-c++ make readline-devel zlib-devel flex bison python-devel openssl-devel pam-devel
```

推荐将[官方](https://github.com/greenplum-db/gpdb/blob/main/README.Linux.md)依赖也安装上.

#### Rhel-Rocky.bash

```sh
install needed packages. Please add to this list if you discover additional prerequisites
sudo yum group install -y "Development Tools"
INSTALL_PKGS="apr-devel bison bzip2-devel cmake3 epel-release flex gcc gcc-c++ krb5-devel libcurl-devel libevent-devel libkadm5 libxml2-devel libzstd-devel openssl-devel python39 python39-devel python39-psutil python3-pip perl-ExtUtils-MakeMaker.noarch perl-ExtUtils-Embed.noarch readline-devel rsync xerces-c-devel zlib-devel python3-psutil python3-pyyaml"

for i in $INSTALL_PKGS; do
  sudo yum install -y $i
done

sudo yum install -y postgresql 
sudo yum install -y postgresql-devel
sudo yum install -y python3-psycopg2

pip3.9 install -r python-dependencies.txt

#Remove the python3 softlink because on Rocky8 its pointing to python3.6 by default. We need python3.9 for gpdb7.
sudo rm -rf /usr/bin/python3
sudo ln -s /usr/bin/python3.9 /usr/bin/python3

#For all Greenplum Database host systems running RHEL, CentOs or Rocky8, SELinux must either be Disabled or configured to allow unconfined access to Greenplum processes, directories, and the gpadmin user.
setenforce 0
sudo tee -a /etc/selinux/config << EOF
SELINUX=disabled
EOF

#To prevent SELinux-related SSH authentication denials that could occur even with SELinux deactivated
sudo tee -a /etc/sssd/sssd.conf << EOF
selinux_provider=none
EOF

sudo systemctl stop firewalld.service

#Configure kernel settings so the system is optimized for Greenplum Database.
sudo tee -a /etc/sysctl.d/10-gpdb.conf << EOF
kernel.msgmax = 65536
kernel.msgmnb = 65536
kernel.msgmni = 2048
kernel.sem = 500 2048000 200 8192
kernel.shmmni = 1024
kernel.core_uses_pid = 1
kernel.core_pattern=/var/core/core.%h.%t
kernel.sysrq = 1
net.core.netdev_max_backlog = 2000
net.core.rmem_max = 4194304
net.core.wmem_max = 4194304
net.core.rmem_default = 4194304
net.core.wmem_default = 4194304
net.ipv4.tcp_rmem = 4096 4224000 16777216
net.ipv4.tcp_wmem = 4096 4224000 16777216
net.core.optmem_max = 4194304
net.core.somaxconn = 10000
net.ipv4.ip_forward = 0
net.ipv4.tcp_congestion_control = cubic
net.ipv4.tcp_tw_recycle = 0
net.core.default_qdisc = fq_codel
net.ipv4.tcp_mtu_probing = 0
net.ipv4.conf.all.arp_filter = 1
net.ipv4.conf.default.accept_source_route = 0
net.ipv4.ip_local_port_range = 10000 65535
net.ipv4.tcp_max_syn_backlog = 4096
net.ipv4.tcp_syncookies = 1
net.ipv4.ipfrag_high_thresh = 41943040
net.ipv4.ipfrag_low_thresh = 31457280
net.ipv4.ipfrag_time = 60
net.ipv4.ip_local_reserved_ports=65330
vm.overcommit_memory = 2
vm.overcommit_ratio = 95
vm.swappiness = 10
vm.dirty_expire_centisecs = 500
vm.dirty_writeback_centisecs = 100
vm.zone_reclaim_mode = 0
EOF

RAM_IN_KB=`cat /proc/meminfo | grep MemTotal | awk '{print $2}'`
RAM_IN_BYTES=$(($RAM_IN_KB*1024))
echo "vm.min_free_kbytes = $(($RAM_IN_BYTES*3/100/1024))" | sudo tee -a /etc/sysctl.d/10-gpdb.conf > /dev/null
echo "kernel.shmall = $(($RAM_IN_BYTES/2/4096))" | sudo tee -a /etc/sysctl.d/10-gpdb.conf > /dev/null
echo "kernel.shmmax = $(($RAM_IN_BYTES/2))" | sudo tee -a /etc/sysctl.d/10-gpdb.conf > /dev/null
if [ $RAM_IN_BYTES -le $((64*1024*1024*1024)) ]; then
    echo "vm.dirty_background_ratio = 3" | sudo tee -a /etc/sysctl.d/10-gpdb.conf > /dev/null
    echo "vm.dirty_ratio = 10" | sudo tee -a /etc/sysctl.d/10-gpdb.conf > /dev/null
else
    echo "vm.dirty_background_ratio = 0" | sudo tee -a /etc/sysctl.d/10-gpdb.conf > /dev/null
    echo "vm.dirty_ratio = 0" | sudo tee -a /etc/sysctl.d/10-gpdb.conf > /dev/null
    echo "vm.dirty_background_bytes = 1610612736 # 1.5GB" | sudo tee -a /etc/sysctl.d/10-gpdb.conf > /dev/null
    echo "vm.dirty_bytes = 4294967296 # 4GB" | sudo tee -a /etc/sysctl.d/10-gpdb.conf > /dev/null
fi

sudo sysctl -p

sudo tee -a /etc/security/limits.d/10-nproc.conf << EOF
* soft nofile 524288
* hard nofile 524288
* soft nproc 131072
* hard nproc 131072
* soft core unlimited
EOF


ulimit -n 65536 65536

```

执行完毕后，开始准备安装数据库。

#### 4.系统基本设置

更改主机名

```sh
hostnamectl set-hostname mdw
```

#### 5.安装Greenplum 7

##### 5.1 配置用户

```sh
# groupadd gpadmin
# useradd gpadmin -r -m -g gpadmin
# passwd gpadmin
New password: <changeme>
Retype new password: <changeme>
```

##### 5.2 切换到gpadmin用户，生成ssh密钥

```sh
$ su gpadmin
$ ssh-keygen -t rsa -b 4096
Generating public/private rsa key pair.
Enter file in which to save the key (/home/gpadmin/.ssh/id_rsa):
Created directory '/home/gpadmin/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

##### 5.3  向用户gpadmin授予 sudo 访问权限(生产环境可以不做此步骤)。

```sh
$ vi /etc/sudoers
%wheel        ALL=(ALL)       NOPASSWD: ALL
```

使用此命令将用户gpadmin添加到组wheel。

```sh
# usermod -aG wheel gpadmin
```

##### 5.4 官方下载数据库rpm包

```sh
https://github.com/greenplum-db/gpdb/releases/download/7.0.0-beta.5/open-source-greenplum-db-7.0.0-beta.5-el8-x86_64.rpm
```

##### 5.5 安装数据库

```sh
sudo yum install open-source-greenplum-db-7.0.0-beta.5-el8-x86_64.rpm
```

完成后，授权以下目录给gpadmin。没有的目录需要提前创建！！！

```sh
$ sudo chown -R gpadmin:gpadmin /usr/local/greenplum*
$ sudo chgrp -R gpadmin /usr/local/greenplum*
$ sudo chown -R gpadmin:gpadmin /home/gpadmin/data
```

##### 5.6 登录gpadmin

```sh
su - gpadmin
source /usr/local/greenplum-db-7.0.0-beta.5/greenplum_path.sh
```

##### 5.7 编写一个主机清单

```sh
$ vi /home/gpadmin/gpconfigs/all_host
mdw
```

加入主机与IP映射关系

```sh
vi /etc/hosts
192.168.1.158 mdw
```

然后执行：

```sh
$ gpssh-exkeys -f all_host
```

##### 5.8 创建数据存储目录

```sh
mkdir -p /home/gpadmin/data/coordinator/  
mkdir -p /home/gpadmin/data/mirror/  
mkdir -p /home/gpadmin/data/primary/
```

##### 5.9 初始化数据库

```sh
cp /usr/local/greenplum-db-7.0.0-beta.5/docs/cli_help/gpconfigs/gpinitsystem_config \ 
/home/gpadmin/gpconfigs/gpinitsystem_config

$ vi gpconfigs/gpinitsystem_config
SEG_PREFIX=gpseg
PORT_BASE=6000 
declare -a DATA_DIRECTORY=(/home/gpadmin/data/primary /home/gpadmin/data/primary /home/gpadmin/data/primary /home/gpadmin/data/primary /home/gpadmin/data/primary /home/gpadmin/data/primary)
COORDINATOR_HOSTNAME=mdw 
COORDINATOR_DIRECTORY=/home/gpadmin/data/coordinator
COORDINATOR_PORT=5432 
TRUSTED SHELL=ssh
CHECK_POINT_SEGMENTS=8
```

##### 5.10配置环境变量

```sh
vi  .bashrc
export GPHOME=/usr/local/greenplum-db
export PATH=$GPHOME/bin:$PATH
export LD_LIBRARY_PATH=$GPHOME/lib:$LD_LIBRARY_PATH
```

应用：

```sh
. .bashrc
```

##### 5.11数据库初始化

```sh
cd ~
gpinitsystem -c gpconfigs/gpinitsystem_config -h gpconfigs/all_host -y
```

完成后，显示以下内容即为成功初始化

```sh
=> Greenplum Database instance successfully created.
```

最后一步，配置greenplum参数

```sh
$ vi ~/.bashrc
source /usr/local/greenplum-db/greenplum_path.sh
export COORDINATOR_DATA_DIRECTORY=/gpdata/coordinator/gpseg-1
export PGPORT=5432
export PGUSER=gpadmin
export PGDATABASE=gpadmin
export LD_PRELOAD=/lib64/libz.so.1 ps
```

应用

```sh
. .bashrc
```

#### 6.数据库设置

##### 6.1 运行数据库

```sh
gpstart -a
```

#### 7.报错总结

运行`gpstart -a `报错

```sh
[gpadmin@mdw ~]$ gpstate -a
Traceback (most recent call last):
  File "/usr/local/greenplum-db/bin/gpstate", line 11, in <module>
    from gppylib.mainUtils import *
ModuleNotFoundError: No module named 'gppylib'
```

环境变量没有配置好,运行下面命令再次运行即可。

```sh
[gpadmin@mdw ~]$ source /usr/local/greenplum-db/greenplum_path.sh
```

启动成功后显示

```sh
20230907:21:43:51:134504 gpstart:mdw:gpadmin-[INFO]:-Database successfully started
```

##### 查看端口信息

```sh
[gpadmin@mdw ~]$ netstat -an | grep 5432 
tcp        0      0 0.0.0.0:5432            0.0.0.0:*               LISTEN     
tcp6       0      0 :::5432                 :::*                    LISTEN     
tcp6       0      0 ::1:56022               ::1:5432                TIME_WAIT  
unix  2      [ ACC ]     STREAM     LISTENING     461400   /tmp/.s.PGSQL.5432
```

##### 查看数据库当前状态

```sh
[gpadmin@mdw ~]$ gpstate
20230907:21:56:53:135664 gpstate:mdw:gpadmin-[INFO]:-Starting gpstate with args: 
20230907:21:56:54:135664 gpstate:mdw:gpadmin-[INFO]:-local Greenplum Version: 'postgres (Greenplum Database) 7.0.0-beta.5 build commit:08189b6c918790e7bc32184a6661de14fe22c8d5 Open Source'
20230907:21:56:54:135664 gpstate:mdw:gpadmin-[INFO]:-coordinator Greenplum Version: 'PostgreSQL 12.12 (Greenplum Database 7.0.0-beta.5 build commit:08189b6c918790e7bc32184a6661de14fe22c8d5 Open Source) on x86_64-pc-linux-gnu, compiled by gcc (GCC) 8.5.0 20210514 (Red Hat 8.5.0-18), 64-bit compiled on Aug  1 2023 20:56:54 Bhuvnesh C.'
20230907:21:56:54:135664 gpstate:mdw:gpadmin-[INFO]:-Obtaining Segment details from coordinator...
20230907:21:56:54:135664 gpstate:mdw:gpadmin-[INFO]:-Gathering data from segments...
..........
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-Greenplum instance status summary
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-----------------------------------------------------
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Coordinator instance                              = Active
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Coordinator standby                               = No coordinator standby configured
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Total segment instance count from metadata        = 6
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-----------------------------------------------------
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Primary Segment Status
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-----------------------------------------------------
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Total primary segments                            = 6
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Total primary segment valid (at coordinator)      = 6
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Total primary segment failures (at coordinator)   = 0
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Total number of postmaster.pid files missing      = 0
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Total number of postmaster.pid files found        = 6
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Total number of postmaster.pid PIDs missing       = 0
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Total number of postmaster.pid PIDs found         = 6
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Total number of /tmp lock files missing           = 0
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Total number of /tmp lock files found             = 6
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Total number postmaster processes missing         = 0
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Total number postmaster processes found           = 6
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-----------------------------------------------------
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Mirror Segment Status
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-----------------------------------------------------
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-   Mirrors not configured on this array
20230907:21:57:05:135664 gpstate:mdw:gpadmin-[INFO]:-----------------------------------------------------
```

##### 连接数据库

```sh
psql postgres
```

#### 8.开启数据库远程访问

[参考](https://blog.csdn.net/MasterLeon/article/details/97702406)

```sh
[gpadmin@mdw ~]$ psql -h 192.168.1.158 -p 5432 postgres gpadmin
psql (12.12)
Type "help" for help.
postgres=# alter role gpadmin with password 'gpadmin';
ALTER ROLE
postgres=# exit
```

##### 修改postgresql.conf

```sh
[gpadmin@mdw gpseg-1]$ vi postgresql.conf

# - Connection Settings -

listen_addresses = '*'                  # what IP address(es) to listen on;
                                        # comma-separated list of addresses;
                                        # defaults to '*', '*' = all
                                        # (change requires restart)
```

##### 修改pg_hba.conf

```sh
vi /home/gpadmin/data/coordinator/gpseg-1/pg_hba.conf
host    all             all             0.0.0.0/0            md5
```

##### 重启greenplum数据库

通过gpstop -u 命令使配置生效

```sh
[gpadmin@mdw ~]$ gpstop -u
```

##### psql远程连接数据库

再次使用dbeaver连接数据库








