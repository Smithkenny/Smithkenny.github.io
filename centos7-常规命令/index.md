# CentOS7 常规命令

# CentOS 7 常规命令

## 文件类

### 常规

tar 打包解包

```shell
tar cvf FileName.tar DirName or FileName
# 打包
tar xvf FileName.tar -C 指定位置目录
# 解包
```

tar gz 打包压缩

```shell
tar zcvf FileName.tar.gz DirName or FileName
# 打包并压缩
tar zxvf FileName.tar.gz -C 指定位置目录
# 解包
```

zip 压缩解压缩

```shell
zip FileName.zip FileName
# 压缩文件
zip -r DirName.zip DirName
# 压缩目录
unzip xxx.zip
# 解压缩
```

### 文件下载

下载覆盖

```shell
wget https://xxx.com/xxx.zip -O /xxx/xxx.zip
# -O 重命名并直接覆盖
```

下载指定路径比对新旧直接覆盖

```shell
wget -P /xxx/xxx -N https://xxx.com/xxx.zip
# -P 指定下载路径,如不存在则自动创建.
# -N 只下载比本地新的文件并覆盖
```

### 文件传输

文件下载 - 从远程服务器下载文件到本服务器

```shell
scp -P 22 root@8.8.8.8:/root/1.zip /root
```

文件上传 - 上传本地文件到远程服务器

```shell
scp -P 22 /root/st/1.zip root@8.8.8.8:/root
```

目录下载 - 从远程服务器下载文件夹到本服务器

```shell
scp -r -P 22 root@8.8.8.8:/root/xx /root
```

目录上传 - 上传本地文件夹到远程服务器

```shell
scp -r -P 22 /root/xx root@8.8.8.8:/root
```

### 文件查找

查询当前目录下及所有子目录下的某文件,并拷贝到当前文件夹

```shell
find . -name "*.txt" | xargs -I{} cp {} ./
find . -name "*.*" | xargs -I{} cp {} ./
```

查询当前目录下及所有子目录下的某文件,并删除.

```shell
find . -name ".DS_Store" | xargs -I{} rm {}
```

## 运维类

### 基础配置

允许 root 登陆及修改端口

```shell
vi /etc/ssh/sshd_config
Port 22222
# 配置端口
PermitRootLogin yes
PasswordAuthentication yes
ClientAliveInterval 30
# 重启 sshd
systemctl restart sshd
```

yum 源

```shell
yum repolist
yum repolist all
# 查看 yum 源
yum -y install epel-release
# 如无 epel/x86_64 推荐先安装 epel 源
yum clean all
yum makecache
# 清除并重建缓存
yum update -y
# 更新
# 如需使用 epel 源国内阿里云镜像加速,执行以下命令:
mv /etc/yum.repos.d/epel.repo /etc/yum.repos.d/epel.repo.backup
mv /etc/yum.repos.d/epel-testing.repo /etc/yum.repos.d/epel-testing.repo.backup
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
yum clean all
yum makecache
```

修改网络信息

```shell
 cd  /etc/sysconfig/network-scripts
 # ls 查看当前网卡信息,可能为 ifcfg-eth0 ifcfg-ens192 等等.
 vi ifcfg-eth0
 # 编辑修改网络信息
 service network restart
 # 重启网络服务
```

时区及 ntp 时间同步

```shell
tzselect
# 选择时区
mv /etc/localtime /etc/localtime.bak
# 备份 localtime
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
# 创建时区软链接,再次 date 检查时区.
yum -y install ntpdate
# 安装 ntpdate
ntpdate cn.pool.ntp.org
# 同步时间
# 备选 ntp 服务器
cn.pool.ntp.org
ntp.aliyun.com
ntp1.aliyun.com
time1.cloud.tencent.com
```

screen 常用命令

```shell
yum -y install screen
# 安装 screen
screen -S xxx
# 新建名为 xxx 的 session
screen -ls
# 列出当前所有 session
screen -r xxx
# 恢复到 xxx 这个 session
screen -d xxx
# 远程分离 xxx 这个 session
screen -d -r xxx
# 结束当前 session 并恢复到 xxx 这个 session
screen -S xxx -X quit
# 远程删除 session
```

### 常规查询

清除历史登录记录

```shell
echo > /var/log/wtmp
```

清除历史命令记录

```shell
history -c && history -w
```

一键清除并退出

```shell
echo > /var/log/wtmp && history -c && history -w && exit
```

查看目录占用空间

```shell
du -h --max-depth=1 /home
```

端口占用查询

```shell
netstat -ntlp
netstat -ntlp | grep 80
# 查询80端口占用
```

CPU内存占用查询

```shell
ps aux|head -1;ps aux|grep -v PID|sort -rn -k +3|head
# 获取占用CPU资源最多的10个进程
ps aux|head -1;ps aux|grep -v PID|sort -rn -k +4|head
# 获取占用内存资源最多的10个进程
```

重新加载systemctl

```shell
sudo systemctl daemon-reload
```

### iptables

使用 iptables 需禁用默认防火墙 firewalld

```shell
yum -y install iptables-services
# 安装 iptables iptables-services

systemctl stop firewalld
systemctl disable firewalld
# 禁用 firewall

systemctl start iptables
systemctl status iptables
systemctl enable iptables
# 启用 iptables

iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -F 
service iptables save
# 设置默认规则并清空所有规则,保存设置.

# 以下为快速执行
iptables -P INPUT ACCEPT && \
iptables -P OUTPUT ACCEPT && \
iptables -P FORWARD ACCEPT && \
iptables -F && \
service iptables save
```

### 防火墙

#### 常规命令

```shell
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --add-service=https --permanent
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
sudo firewall-cmd --add-port=4000-5000/tcp --permanent
sudo firewall-cmd --remove-port=XXXX/tcp --permanent
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

#### 更多命令

```shell
# 查看是否开启
systemctl status firewalld.service
# 打开防火墙
systemctl start firewalld.service
# 停用防火墙
systemctl disable firewalld
# 禁用防火墙
systemctl stop firewalld.service

# 开机启动
systemctl enable firewalld
# 取消开机启动
systemctl disable firewalld

# 查看运行状态
firewall-cmd --state
# 查看接口信息
firewall-cmd --list-all

# 更新防火墙规则方法1:无需断开连接，动态更改规则
firewall-cmd --reload
# 更新防火墙规则方法2:断开连接，以重启的方式更改规则
firewall-cmd --complete-reload

# 查看帮助
firewall-cmd --help
--zone=NAME # 指定 Zone
--permanent # 为永久生效
--timeout=seconds # 持续一段时间，到期后自动移除，经常用于调试，且不能与 --permanent 同时使用

# 追加一个8181端口，永久有效
firewall-cmd --add-port=8181/tcp --permanent
# 追加一段端口范围
firewall-cmd --add-port=6000-6600/tcp
# 开放 ftp 服务
firewall-cmd --add-service=ftp
# 添加eth0 接口至 public 信任等级，永久有效
firewall-cmd --zone=public --add-interface=eth0 --permanent

# 配置 public zone 的端口转发
firewall-cmd --zone=public --add-masquerade
# 然后转发 tcp 22 端口至 9527
firewall-cmd --zone=public --add-forward-port=port=22:proto=tcp:toport=9527
# 转发 22 端口数据至另一个 ip 的相同端口上
firewall-cmd --zone=public --add-forward-port=port=22:proto=tcp:toaddr=192.168.1.123
# 转发 22 端口数据至另一 ip 的 9527 端口上
firewall-cmd --zone=public --add-forward-port=port=22:proto=tcp:toport=9527:toaddr=192.168.1.100

# IP 封禁
firewall-cmd --permanent --add-rich-rule="rule family='ipv4' source address='192.168.1.123' reject"
# 通过 ipset 来封禁 ip
firewall-cmd --permanent --zone=public --new-ipset=blacklist --type=hash:ip
firewall-cmd --permanent --zone=public --ipset=blacklist --add-entry=192.168.1.123
# 封禁网段
firewall-cmd --permanent --zone=public --new-ipset=blacklist --type=hash:net
firewall-cmd --permanent --zone=public --ipset=blacklist --add-entry=192.168.1.0/24
# 倒入 ipset 规则 blacklist，然后封禁 blacklist
firewall-cmd --permanent --zone=public --new-ipset-from-file=/path/blacklist.xml
firewall-cmd --permanent --zone=public --add-rich-rule='rule source ipset=blacklist drop'
```

## 系统类

### 磁盘挂载

#### 磁盘分区格式化

```shell
fdisk -l
# 查看到新硬盘 /dev/sdb
fdisk /dev/sdb
# 为磁盘分区,选择 n 开始分区,其他直接回车保持默认,结尾 w 保存.
mkfs.ext4 /dev/sdb1
# 格式化分区
```

#### 磁盘挂载

```shell
mkdir /data
# 创建挂载目录
mount /dev/sdb1 /data
# 挂载至 /data
vi /etc/fstab
# 配置开机自动挂载
/dev/sdb1 /data ext4 defaults 0 0
# 添加启动项后保存
umount /data
# 取消挂载,同时需再次编辑 /etc/fstab 删除开机自动挂载命令.
```

### NFS

#### 服务端

##### 安装

```shell
yum -y install nfs-utils
systemctl start rpcbind
systemctl enable rpcbind
systemctl start nfs
systemctl enable nfs
```

##### 配置

```shell
vi /etc/exports
# 配置挂载权限
/share *(rw,sync,no_root_squash,no_all_squash)
# 所有网段
/share 192.168.1.0/24(rw,sync,no_root_squash,no_all_squash)
# 指定网段
/share 192.168.1.9(rw,sync,no_root_squash,no_all_squash)
# 指定 IP
exportfs -r
# 刷新配置
```

#### 客户端挂载

用于挂载局域网内群晖 NFS 共享文件夹,需在群晖 DSM 内开启 NFS 服务,并编辑需挂载的共享文件夹,添加 NFS 权限设置`服务器名称或 IP 地址`为 **`\*`**

```shell
yum -y install nfs-utils
# 安装 NFS 工具
mkdir /data
# 创建本地目录
mount -t nfs 192.168.1.8:/volume1/Downloads /data
# 挂载目录
vi /etc/fstab
# 设置开机自动挂载
192.168.1.8:/volume1/Downloads /data nfs defaults 0 0
# 尾行添加挂载命令
umount /data
# 取消挂载,同时需再次编辑 /etc/fstab 删除开机自动挂载命令.
```

### SMB

#### 服务端

##### 安装启动

```shell
yum -y install samba
# 安装
systemctl start smb
# 启动
systemctl status smb
# 状态
systemctl enable smb
# 开机自启动
systemctl restart smb
# 重启
```

##### 共享文件夹

本文为匿名共享文件夹,无需配置账号密码.

```shell
mkdir /data
# 创建共享文件夹
chown -R nobody:nobody /data
# 赋予文件夹匿名用户权限
vi /etc/sysconfig/selinux
# OR
vi /etc/selinux/config
# 修改 SELINUX=disabled 后重启服务器
```

##### 配置 smb.conf

```shell
vi /etc/samba/smb.conf
# 参考以下编辑 conf 文件:
[global]
    workgroup = WORKGROUP
    security = user
    map to guest = Bad User
[data]
    comment = date
    path = /data
    public = yes
    read only = No
```

##### 配置账号密码

[参考链接](https://cloud.tencent.com/developer/article/1691026)

```shell
useradd stille
smbpasswd -a stille
chown -R nobody:nobody share
chmod -R 777 share

vi /etc/samba/smb.conf

[global]
    security = user
    workgroup = SAMBA
    passdb backend = tdbsam
    
[share]
    comment = share
    path = /share
    writable = no
    create mask = 0644
    directory mask = 02755
    valid users = stille
    write list = stille
```

##### 检查语法

```shell
testparm
# 检查语法
systemctl restart smb
# 重启 smb 服务
```

##### avahi

```shell
yum install avahi-tools
systemctl start avahi-daemon
systemctl enable avahi-daemon
vi /etc/avahi/services/smb.service

<service-group>
<name>CENTOS</name>
<service>
<type>_device-info._tcp</type>
<txt-record>model=Xserve</txt-record>
</service>
<service>
<type>_smb._tcp</type>
<port>445</port>
</service>
</service-group>

systemctl restart avahi-daemon
```

#### 客户端

##### 匿名挂载

```shell
mount -t cifs -o guest //192.168.1.8/share /share
# 配置开机自动挂载
vim /etc/fstab
//192.168.1.8/share  /share          cifs    defaults,guest  0 0
```

##### 用户密码挂载

```shell
mount -t cifs -o username=xxxxx,password='xxxxx',iocharset=utf-8 //192.168.1.8/share /share
# 配置开机自动挂载
vim /etc/fstab
//192.168.1.8/share  /share          cifs    defaults,username=xxxxx,password=xxxxx
```

##### umount

```shell
umount /share
# 遇到无法 umount 时
yum install psmisc
fuser -mv /share
kill -9 xxxx
```

