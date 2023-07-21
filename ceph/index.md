# ceph


### 基于k8s的ceph集群搭建

### 环境

- master-139 mon,osd 既做管理节点又做子节点
- node1-140 mon,osd  做子节点
- node2-141 mon,osd  做子节点
- ceph 版本 13.2.10 mimic (stable)

## 系统配置

系统配置工作， 三台节点依次执行

#### 1、修改主机名称

```bash
[root@master-139 ~]# vi /etc/hostname
master-139
```

#### 2、编辑hosts文件

```bash
10.4.7.139   master-139
10.4.7.140   node1-140
10.4.7.141	 node2-141
```

注意， 这里面的主机名称要和节点名称保持一致， 否则安装的时候会出现问题

#### 3.修改yum源

```bash
cat << EOF > /etc/yum.repos.d/ceph.repo 
[ceph] 
name=Ceph packages for $basearch
baseurl=https://mirrors.tuna.tsinghua.edu.cn/ceph/rpm-mimic/el7/x86_64/ 
enabled=1 
gpgcheck=1 
type=rpm-md 
gpgkey=https://download.ceph.com/keys/release.asc
 
[ceph-noarch] 
name=Ceph noarch packages 
# 官方源 
#baseurl=http://download.ceph.com/rpm-mimic/el7/noarch 
# 清华源 
baseurl=https://mirrors.tuna.tsinghua.edu.cn/ceph/rpm-mimic/el7/noarch/ 
enabled=1 
gpgcheck=1 
type=rpm-md 
gpgkey=https://download.ceph.com/keys/release.asc
 
[ceph-source] 
name=Ceph source packages
baseurl=https://mirrors.tuna.tsinghua.edu.cn/ceph/rpm-mimic/el7/SRPMS/ 
enabled=1 
gpgcheck=1 
type=rpm-md 
gpgkey=https://download.ceph.com/keys/release.asc
EOF
```

#### 4、安装ceph与ceph-deploy组件

``` 
yum clean all && yum makecache 
yum -y install python-setuptools epel-release
yum -y install python2-pip ceph-deploy ceph
```

#### 5.安装NTP时间同步工具 

```bash
yum install ntp ntpdate ntp-doc -y
```

 确保时区是正确， 设置开机启动：

```bash 
systemctl enable ntpd
```


并将时间每隔1小时自动校准同步。编辑 vi /etc/rc.d/rc.local 追加：

```bash
/usr/sbin/ntpdate ntp1.aliyun.com > /dev/null 2>&1; /sbin/hwclock -w
```


配置定时任务, 执行crontab -e 加入：

### 免密码SSH登陆
#### 6.开放端口， 非生产环境， 可以直接禁用防火墙： 

```bash
systemctl stop firewalld.service 
systemctl disable firewalld.service
```

#### 7.SELINUX设置

SELinux 设为禁用：

永久生效：
编辑 vi /etc/selinux/config 修改：

```bash
SELINUX=disabled
```

#### 8.生成密钥

```bash
执行 ssh-keygen ，一直按默认提示点击生成 RSA 密钥信息。
```

#### 9.分发密钥至各机器节点

```bash
ssh-copy-id root@master-139
ssh-copy-id root@node1-140
ssh-copy-id root@node2-141
```

**注意：以上1-9步骤在所有节点上执行！！**

## **集群搭建配置**

#### 1.采用root身份进行安装，在管理节点创建集群配置目录

```bash
cd /usr/local/
mkdir ceph-cluster 
cd ceph-cluster
```

注意： 此目录作为 ceph 操作命令的基准目录， 会存储处理配置信息。

#### 2.创建集群， 包含三台机器节点：

```bash
ceph-deploy new master-139 node1-140 node2-141
```

创建成功后， 会生一个配置文件。

```bash
[root@master-139 ceph-cluster]# ll
total 16
-rw-r--r-- 1 root root  242 May 15 03:15 ceph.conf
-rw-r--r-- 1 root root 5559 May 15 03:15 ceph-deploy-ceph.log
-rw------- 1 root root   73 May 15 03:15 ceph.mon.keyring
```

#### 3.如果接下来集群的安装配置出现问题， 可以执行以下命令清除， 再重新安装(可选)

```bash
ceph-deploy purge master-139 node1-140 node2-141
ceph-deploy purgedata master-139 node1-140 node2-141
ceph-deploy forgetkeys
```

#### 4.将三台节点的mon信息也删除（可选）

```bash
rm -rf /var/run/ceph/
```

#### 5.修改配置文件， 有些配置后面需用到

```bash
vi /usr/local/ceph-cluster/ceph.conf
[global] 
# 设置pool池默认分配数量 默认副本数为3 
osd pool default size = 3 
# 容忍更多的时钟误差 
mon clock drift allowed = 2 
mon clock drift warn backoff = 30 
# 允许删除pool 
mon_allow_pool_delete = true
[mgr]
# 开启WEB仪表盘 
mgr modules = dashboard
```

第一项为副本数， 设为 3 份。
第二、三项为允许一定时间的漂移误差。

**设置完成后的配置如下**

```bash
[global]
fsid = 67dcaf50-275b-442f-b69d-677fd8afcc94
mon_initial_members = master-139, node1-140, node2-141
mon_host = 10.4.7.139,10.4.7.140,10.4.7.141
auth_cluster_required = cephx
auth_service_required = cephx
auth_client_required = cephx
# 设置pool池默认分配数量 默认副本数为3
osd pool default size = 3
# 容忍更多的时钟误差
mon clock drift allowed = 2
mon clock drift warn backoff = 30
# 允许删除pool
mon_allow_pool_delete = true
[mgr]
# 开启WEB仪表盘
mgr modules = dashboard
```

#### 6.执行安装

```bash
ceph-deploy install master-139 node1-140 node2-141
```

如果出现错误：

```bash
ceph_deploy][ERROR ] RuntimeError: Failed to execute command: ceph --version
```


可以在各节点上单独进行安装：

```bash
yum -y install ceph 
```

如果没有仓库文件 ceph.repo ， 按上面的步骤手工创建。

#### 7.初始monitor信息

```bash
ceph-deploy mon create-initial ## ceph-deploy --overwrite-conf mon create-initial
```

**初始化后生成以下信息**

```bash
[ceph_deploy.gatherkeys][INFO  ] Storing ceph.client.admin.keyring
[ceph_deploy.gatherkeys][INFO  ] Storing ceph.bootstrap-mds.keyring
[ceph_deploy.gatherkeys][INFO  ] Storing ceph.bootstrap-mgr.keyring
[ceph_deploy.gatherkeys][INFO  ] keyring 'ceph.mon.keyring' already exists
[ceph_deploy.gatherkeys][INFO  ] Storing ceph.bootstrap-osd.keyring
[ceph_deploy.gatherkeys][INFO  ] Storing ceph.bootstrap-rgw.keyring
[ceph_deploy.gatherkeys][INFO  ] Destroy temp directory /tmp/tmpuBxetx
[root@master-139 ceph-cluster]# ll
total 220
-rw------- 1 root root    113 May 15 03:29 ceph.bootstrap-mds.keyring
-rw------- 1 root root    113 May 15 03:29 ceph.bootstrap-mgr.keyring
-rw------- 1 root root    113 May 15 03:29 ceph.bootstrap-osd.keyring
-rw------- 1 root root    113 May 15 03:29 ceph.bootstrap-rgw.keyring
-rw------- 1 root root    151 May 15 03:29 ceph.client.admin.keyring
-rw-r--r-- 1 root root    529 May 15 03:22 ceph.conf
-rw-r--r-- 1 root root 167370 May 15 03:29 ceph-deploy-ceph.log
-rw------- 1 root root     73 May 15 03:22 ceph.mon.keyring
```

#### 8.同步管理信息

下发配置文件和管理信息至各节点：

```bash
ceph-deploy admin master-139 node1-140 node2-141
```

#### 9.安装mgr(管理守护进程)， 大于12.x版本需安装， 我们装的是最新版，需执行： 

```bash
ceph-deploy mgr create master-139 node1-140 node2-141
```

#### 10.查看当前ceph版本

```bash
# ceph -v
ceph version 13.2.10 (564bdc4ae87418a232fc901524470e1a0f76d641) mimic (stable)
```

**以上操作都在管理节点master-139上执行！！**

### 安装OSD(对象存储设备) 

注意： 新版本的 OSD 没有 prepare 与 activate 命令。
这里需要新的硬盘作为 OSD 存储设备， 关闭虚拟机， 增加一块硬盘， 不用格式化。

各节点都添加100G硬盘。

重启， fdisk -l 查看新磁盘名称：

```bash
Disk /dev/sdb: 107.4 GB, 107374182400 bytes, 209715200 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

#### 1.执行创建OSD命令

```bash
ceph-deploy osd create --data /dev/sdb master-139 
ceph-deploy osd create --data /dev/sdb node1-140 
ceph-deploy osd create --data /dev/sdb node2-141
```

三台节点都需分别依次执行。

都切换到/usr/local/ceph-cluster下执行

```bash
ceph-deploy gatherkeys master-139
```

#### 2.验证节点： 

输入 ceph health 或 ceph -s 查看， 出现 HEALTH_OK 代表正常。

```bash
[root@master-139 ceph-cluster]# ceph -s 
  cluster:
    id:     4f74ac9a-b5f5-4bf5-9107-15ab8ea41f76
    health: HEALTH_OK
 
  services:
    mon: 3 daemons, quorum master-139,node1-140,node2-141
    mgr: master-139(active), standbys: node1-140, node2-141
    osd: 3 osds: 3 up, 3 in
 
  data:
    pools:   0 pools, 0 pgs
    objects: 0  objects, 0 B
    usage:   3.0 GiB used, 297 GiB / 300 GiB avail
    pgs:     
```

#### 3.如果出现错误，各节点上执行,确保时间一致。

```bash
 ntpdate ntp1.aliyun.com
```

## **安装管理后台**（可选）

#### 1.开启dashboard模块

```bash
ceph mgr module enable dashboard
```

#### 2.生成签名 

```bash
ceph dashboard create-self-signed-cert
```

#### 3.创建目录 

```bash
mkdir mgr-dashboard
[root@master-139 mgr-dashboard]# pwd 
/usr/local/ceph-cluster/mgr-dashboard
```

#### 4.生成密钥对

```bash
cd /usr/local/ceph-cluster/mgr-dashboard
openssl req -new -nodes -x509 -subj "/O=IT/CN=ceph-mgr-dashboard" -days 3650 -keyout dashboard.key -out dashboard.crt -extensions v3_ca
```

#### 5.启动dashboard 

```bash
ceph mgr module disable dashboard 
ceph mgr module enable dashboard
```

#### 6.设置IP与PORT  

```bash
ceph config set mgr mgr/dashboard/server_addr 10.4.7.139
ceph config set mgr mgr/dashboard/server_port 18843
```

注意：ip为管理节点的主IP

#### 7.关闭HTTPS

```bash
ceph config set mgr mgr/dashboard/ssl false
```

#### 8.查看服务信息 

```bash
[root@master-139 mgr-dashboard]# ceph mgr services
{
    "dashboard": "https://master-139:8443/"
}
```

#### 9.设置管理用户与密码

```bash
ceph dashboard set-login-credentials admin admin
```

#### 10.访问

```bash
http://10.4.7.139:18843
```

## 创建Cephfs

集群创建完后， 默认没有文件系统， 我们创建一个 Cephfs 可以支持对外访问的文件系统。

#### 1.创建两个存储池, 执行两条命令

```bash
ceph osd pool create cephfs_data 128 
ceph osd pool create cephfs_metadata 64
```

少于 5 个 OSD 可把 pg_num 设置为 128
OSD 数量在 5 到 10 ，可以设置 pg_num 为 512
OSD 数量在 10 到 50 ，可以设置 pg_num 为 4096
OSD 数量大于 50 ，需要计算 pg_num 的值

通过下面命令可以列出当前创建的存储池：

```bash
[root@master-139 ceph-cluster]# ceph osd lspools
1 cephfs_data
2 cephfs_metadata
```

#### 2.创建fs, 名称为cephfs

```bash
ceph fs new cephfs cephfs_metadata cephfs_data
```

#### 3.状态查看， 以下信息代表正常

```bash
[root@master-139 ceph-cluster]# ceph fs ls 
name: cephfs, metadata pool: cephfs_metadata, data pools: [cephfs_data ]
[root@haproxy ceph-cluster]# ceph mds stat
cephfs-1/1/1 up  {0=haproxy=up:active}, 1 up:standby
```

这里mds如果只显示cephfs-1/1/1 up 后面没内容说明mds没创建需要手动创建。如果不创建后面ceph挂载会报错。提示mds不存在。



```bash
[root@master-139 ceph-cluster]# ceph mds stat 
cephfs-0/0/1 up
#挂载时报错信息
[root@node1-140 ceph-cluster]# ceph-fuse -k /etc/ceph/ceph.client.admin.keyring -m 10.4.7.139:6789 /usr/local/cephfs_directory 
ceph-fuse[42676]: starting ceph client
2022-05-15 03:43:48.136 7f84eb7cac00 -1 init, newargv = 0x561dca3c0240 newargc=7
ceph-fuse[42676]: probably no MDS server is up?
ceph-fuse[42676]: ceph mount failed with (65536) Unknown error 65536
```

手动创建mds

```bash
ceph-deploy mds create master-139 node1-140 node2-141
```

附： 如果创建错误， 需要删除， 执行：

```bash
ceph fs rm cephfs --yes-i-really-mean-it 
ceph osd pool delete cephfs_data cephfs_data --yes-i-really-really-mean-it
```

确保在ceph.conf中开启以下配置：

```bash
[mon] mon allow pool delete = true
```

### 采用fuse挂载

先确定 ceph-fuse 命令能执行， 如果没有， 则安装：

```bash
yum -y install ceph-fuse
```

#### 1.创建挂载目录

```bash
mkdir -p /usr/local/cephfs_directory
```

#### 2.挂载cephfs

```bash
ceph-fuse -k /etc/ceph/ceph.client.admin.keyring -m 10.4.7.139:6789 /usr/local/cephfs_directory
```

#### 3.查看磁盘挂载信息

```bash
#df -hT
```

/usr/local/cephfs_directory 目录已成功挂载。

```bash
[root@node1-140 ceph-cluster]# df -hT | grep cephfs 
ceph-fuse                  fuse.ceph-fuse   94G     0   94G   0% /usr/local/cephfs_directory
```

注意查看当前ceph 的主节点是谁。三台机器中如果有一台突然宕机，然后又恢复了。此时出问题的那台将成为主节点。

```bash
[root@node1-140 ceph-cluster]# ceph -s 
  cluster:
    id:     4f74ac9a-b5f5-4bf5-9107-15ab8ea41f76
    health: HEALTH_OK
 
  services:
    mon: 3 daemons, quorum master-139,node1-140,node2-141
    mgr: master-139(active), standbys: node1-140, node2-141
    mds: cephfs-1/1/1 up  {0=master-139=up:active}, 2 up:standby
    osd: 3 osds: 3 up, 3 in
 
  data:
    pools:   2 pools, 192 pgs
    objects: 21  objects, 2.2 KiB
    usage:   3.0 GiB used, 297 GiB / 300 GiB avail
    pgs:     192 active+clean
 
  io:
    client:   5.0 KiB/s wr, 0 op/s rd, 9 op/s wr
```

active的是主节点， standbys是备用节点。

## ceph常用命令

#### ceph

```bash
# 查看机器的监控状态
ceph health
# 查看ceph的实时运行状态
ceph -w
# 检查信息状态信息
ceph -s
# 查看ceph存储空间
ceph df
```

#### ceph认证

#### 创建管理用户

为ceph创建一个admin用户并为admin用户创建一个密钥，把密钥保存到/etc/ceph目录下：

```bash
ceph auth get-or-create client.admin mds 'allow' osd 'allow *' mon 'allow *' > /etc/ceph/ceph.client.admin.keyring
或
ceph auth get-or-create client.admin mds 'allow' osd 'allow *' mon 'allow *' -o /etc/ceph/ceph.client.admin.keyring
```

为osd.0创建一个用户并创建一个key

```bash
ceph auth get-or-create osd.0 mon 'allow rwx' osd 'allow *' -o /var/lib/ceph/osd/ceph-0/keyring
```

为mds.node1创建一个用户并创建一个key

```bash
ceph auth get-or-create mds.node1 mon 'allow rwx' osd 'allow *' mds 'allow *' -o /var/lib/ceph/mds/ceph-node1/keyring
```

查看ceph集群中的认证用户及相关的key

```bash
ceph auth list
```

删除集群中的一个认证用户

```bash
ceph auth del osd.0
```

#### 集群相关

```bash
# 查看集群的详细配置
ceph daemon mon.node1 config show | more
# 查看集群健康状态细节
ceph health detail
# 查看ceph log日志所在的目录
ceph-conf --name mon.node1 --show-config-value log_file
```

#### mon命令

```bash
ceph mon stat#查看mon的状态信息

ceph mon dump#查看你ceph映射信息

ceph mon remove node1 #删除一个mon节点  ceph-deploy mon destroy {host-name [host-name]...}

ceph mon add node1 node1_ip #添加一个mon节点  ceph-deploy mon create {host-name [host-name]...} 

mon节点的/var/lib/ceph/mon/ceph-node2/store.db文件内容一致，添加mon注意先改配置目录配置文件，再推送到所有节点

ceph-deploy --overwrite-conf config push  node1 node2 node3
```

#### mds命令

```bash
ceph mds stat #查看msd状态

ceph mds dump #msd的映射信息

ceph mds rm 0 mds.node1#删除一个mds节点

ceph-deploy mds create {host-name}[:{daemon-name}] [{host-name}[:{daemon-name}] ...]
```

#### osd命令

```bash
ceph osd stat #查看osd状态

ceph osd dump #osd的映射信息

ceph osd tree#查看osd目录树

ceph osd down 0   #down掉osd.0节点

ceph osd rm 0#集群删除一个osd硬盘

ceph osd crush remove osd.4#删除标记

ceph osd getmaxosd#查看最大osd个数

ceph osd setmaxosd 10#设置osd的个数

ceph osd out osd.3#把一个osd节点逐出集群

ceph osd in osd.3#把逐出的osd加入集群

ceph osd pause#暂停osd （暂停后整个集群不再接收数据）

ceph osd unpause#再次开启osd （开启后再次接收数据）

ceph osd lspools#查看ceph集群中的pool数量

ceph osd pool create jiayuan 100#创建一个pool  这里的100指的是PG组

ceph osd pool delete jiayuan  jiayuan  --yes-i-really-really-mean-it  #集群名字需要重复两次

rados df#显示集群中pool的详细信息

ceph osd pool get data pg_num  #查看data池的pg数量

ceph osd pool set data target_max_bytes 100000000000000#设置data池的最大存储空间为100T（默认是1T)

ceph osd pool set data size 3  #设置data池的副本数是3

ceph osd pool set data min_size 2 #设置data池能接受写操作的最小副本为2

ceph osd pool set data pg_num 100#设置一个pool的pg数量

ceph osd pool set data pgp_num 100#设置一个pool的pgp数量
```

#### pg命令

```bash
ceph pg stat#查看pg状态

ceph pg dump#查看pg组的映射信息

ceph pg map 0.3f#查看一个pg的map

ceph pg  0.26 query#查看pg详细信息

ceph pg dump --format plain#显示一个集群中的所有的pg统计
```

#### rados和rbd命令

```bash
rados lspools#查看ceph集群中有多少个pool （只是查看pool)

rados df #查看ceph集群中有多少个pool,并且每个pool容量及利用情况

rados mkpool test#创建一个pool

rados create test-object -p test#创建一个对象object 

rados rm test-object-1 -p test#删除一个对象object 

rados -p test ls

rbd ls pool_name#查看ceph中一个pool里的所有镜像

rbd info -p pool_name --image 74cb427c-cee9-47d0-b467-af217a67e60a #查看ceph pool中一个镜像的信息

rbd create -p test --size 10000 zhanguo#在test池中创建一个命名为zhanguo的10000M的镜像

rbd rm  -p test  lizhanguo #删除一个镜像

rbd resize -p test --size 20000 zhanguo  #调整一个镜像的尺寸
```

#### CRUSH映射

```bash
ceph osd getcrushmap -o MAP   #获取一个CRUSH映射

crushtool -d MAP -o MAP.TXT   #反编译一个CRUSH映射

crushtool -c MAP.TXT -o MAP   #编译一个CRUSH映射

ceph osd setcrushmap -i MAP    #设置一个CRUSH映射
```

#### 块设备的一些命令

单位为M，默认在rbd pool中

```bash
创建块设备：rbd create {image-name}  --size {megabytes}  --pool {pool-name}

列出块设备：rbd ls {poolname} -l

检索块信息：rbd --image {image-name} info

更改块大小：rbd resize --image {image-name} --size {megabytes}

删除块设备：rbd rm {image-name}

映射块设备：rbd map {image-name} --pool {pool-name} --id {user-name}

查看已映射块设备：rbd showmapped

取消映射：rbd unmap /dev/rbd/{poolname}/{imagename}
```

#### 快照和克隆相关命令

```bash
创建快照：

rbd --pool {pool-name} snap create --snap {snap-name} {image-name}

rbd snap create {pool-name}/{image-name}@{snap-name}

快照回滚：

rbd --pool {pool-name} snap rollback --snap {snap-name} {image-name}

rbd snap rollback {pool-name}/{image-name}@{snap-name}



清除快照：

rbd --pool {pool-name} snap purge {image-name}

rbd snap purge {pool-name}/{image-name}

删除快照：

rbd --pool {pool-name} snap rm --snap {snap-name} {image-name}

rbd snap rm {pool-name}/{image-name}@{snap-name}

列出快照：

rbd --pool {pool-name} snap ls {image-name}

rbd snap ls {pool-name}/{image-name}

保护快照：

rbd --pool {pool-name} snap protect --image {image-name} --snap {snapshot-name}

rbd snap protect {pool-name}/{image-name}@{snapshot-name}

取消保护快照：

rbd --pool {pool-name} snap unprotect --image {image-name} --snap {snapshot-name}

rbd snap unprotect {pool-name}/{image-name}@{snapshot-name}

快照克隆

rbd clone {pool-name}/{parent-image}@{snap-name} {pool-name}/{child-image-name}

查看快照的克隆

rbd --pool {pool-name} children --image {image-name} --snap {snap-name}

rbd children {pool-name}/{image-name}@{snapshot-name}
```

#### 快照克隆相关例子

```bash
创建快照：rbd  snap create vms/yjk01@yjk01_s1

列出快照：rbd snap list  --pool vms yjk01

快照回滚：rbd snap rollback vms/yjk01@yjk01_s1(先卸载已挂载目录)

删除快照：rbd snap rm vms/yjk01@yjk01_s2(单个)

清除快照：rbd snap purge vms/yjk01(所有)

保护快照：rbd snap protect vms/yjk01@yjk01_s1

取消保护：rbd snap unprotect vms/yjk01@yjk01_s1

快照克隆：rbd clone vms/yjk01@yjk01_s3 vms/yjk01_s3_clone1

查看克隆：rbd children vms/yjk01@yjk01_s3

克隆只能基于快照，并且只能快照处于保护状态，而且ceph仅支持克隆format 2映像。
```

## 日志文件汇总

mon ,mgr , osd 等安装报错信息均在对应日志文件中。

```bash
/usr/local/ceph-cluster/ceph-deploy-ceph.log

# tree /var/log/ceph/
/var/log/ceph/
├── ceph.audit.log
├── ceph-client.admin.log
├── ceph.log
├── ceph-mds.haproxy.log
├── ceph-mgr.haproxy.log
├── ceph-mon.haproxy.log
├── ceph-osd.0.log
└── ceph-volume.log
```

### 参考文档

[1](https://blog.csdn.net/fake_hydra/article/details/109235907)

[2](https://mp.weixin.qq.com/s/nWZWhnaudueuJwmp-Ktuxw)

[3](https://blog.csdn.net/xiaoweite1/article/details/124059228)


