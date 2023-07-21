# pve优化


### pve优化

### 1.Proxmox VE(PVE)系统移除《无有效订阅》弹窗提示通知

Proxmox VE(PVE)系统安装完成后，输入账号密码登录系统后会有提示：无有效订阅，本文主要介绍如何去除此弹窗提示。

```
提示：无有效订阅

You do not have a valid subscription for this server. Please visit www.proxmox.com to get a list of available options.
```

形成此问题的主要原因是因为我们使用的是免费版Proxmox VE系统，所以每次登录后都会提示这个《无有效订阅》弹窗。

此教程演示系统环境为：Proxmox Virtual Environment 7.2 ，更新日期为：2022年11月10日，其他版本请参考本教程.

需要修改文件路径为：/usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js 修改之前请做好文件备份，防止误操作造成不可预知的错误。

解决方法

```shell
搜索关键词：Ext.Msg.show
将：Ext.Msg.show({修改为：Ext.Msg.noshow({

文件修改后保存上传，执行 systemctl restart pveproxy 命令重启网页服务
```

```
root@pve:~# systemctl restart pveproxy
```

注销后登陆，使用 CTRL+F5 快捷键强制重新加载网页页面，或清理浏览器缓存（很多修改后没有反应的童学记得清缓存）

再次打开登陆 Proxmox VE系统控制台主界面，就没有弹窗提示了。

注意：此方法可能在升级 pve 后失效，失效后再修改即可。

### 2.Proxmox VE更换apt源并更新软件包数据库

1、查看当前镜像源配置

```
cat /etc/apt/sources.list
```

2、查看当前pve版本

```
pveversion -v
```

3、注释默认配置，修改为中科大镜像源

```
nano /etc/apt/sources.list

deb https://mirrors.ustc.edu.cn/debian bullseye main contrib
deb https://mirrors.ustc.edu.cn/debian bullseye-updates main contrib
deb https://mirrors.ustc.edu.cn/debian-security bullseye-security main contrib
```

4、注释proxmox企业版更新源
因为我们安装的是社区版，没有订阅企业版的服务，这个默认启用，所以会报错，使用时我们可以注释掉它。

```
nano /etc/apt/sources.list.d/pve-enterprise.list
```

5、运行 `apt update` 更新索引以生效

6、查看可以更新的包

```
apt list --upgradable
```

7、执行软件包数据库更新

```
apt update && apt upgrade -y
```

8、也可以在页面更新

**pve-更新-刷新**

### 3.PVE更改安装时绑定的网卡

当服务器有多个网卡时，安装pve后，绑定的网卡非管理口网卡导致局域网中机器无法连接pve。此时需要更改pve安装时绑定的网卡。

```sh
root@www:~#  ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eno1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 3c:ec:ef:90:ea:96 brd ff:ff:ff:ff:ff:ff
    altname enp181s0f0
3: enp101s0f0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 58:53:c0:64:19:94 brd ff:ff:ff:ff:ff:ff
4: eno2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq master vmbr0 state UP group default qlen 1000
    link/ether 3c:ec:ef:90:ea:97 brd ff:ff:ff:ff:ff:ff
    altname enp181s0f1
5: enp23s0f0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 68:91:d0:6a:66:a6 brd ff:ff:ff:ff:ff:ff
6: enp101s0f1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 58:53:c0:64:19:95 brd ff:ff:ff:ff:ff:ff
7: enp23s0f1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 68:91:d0:6a:66:a7 brd ff:ff:ff:ff:ff:ff
8: enp182s0f0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 68:91:d0:6a:66:a0 brd ff:ff:ff:ff:ff:ff
9: enp182s0f1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 68:91:d0:6a:66:a1 brd ff:ff:ff:ff:ff:ff
10: vmbr0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 3c:ec:ef:90:ea:97 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.191/24 scope global vmbr0
       valid_lft forever preferred_lft forever
    inet6 fe80::3eec:efff:fe90:ea97/64 scope link 
       valid_lft forever preferred_lft forever
12: tap100i0: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master vmbr0 state UNKNOWN group default qlen 1000
    link/ether 2e:dd:9f:18:5d:41 brd ff:ff:ff:ff:ff:ff
```

由以上信息可知pve的虚拟网卡vmbr0是桥接到mac为3c:ec:ef:90:ea:97的网卡即eno2上，需要将vmbr0更改桥接到eno1上。

```bash
nano /etc/network/interfaces
```

原内容

```bash
auto lo
iface lo inet loopback

iface eno2 inet manual

auto vmbr0
iface vmbr0 inet static
        address 192.168.1.191/24
        gateway 192.168.1.254
        bridge-ports eno2
        bridge-stp off
        bridge-fd 0

iface enp101s0f0 inet manual

iface enp101s0f1 inet manual

iface enp23s0f0 inet manual

iface enp23s0f1 inet manual

iface eno2 inet manual

iface enp182s0f0 inet manual

iface enp182s0f1 inet manual
```

需要更改为

```bash
auto lo
iface lo inet loopback

iface eno1 inet manual

auto vmbr0
iface vmbr0 inet static
        address 192.168.1.191/24
        gateway 192.168.1.254
        bridge-ports eno1
        bridge-stp off
        bridge-fd 0

iface enp101s0f0 inet manual

iface enp101s0f1 inet manual

iface enp23s0f0 inet manual

iface enp23s0f1 inet manual

iface eno2 inet manual

iface enp182s0f0 inet manual

iface enp182s0f1 inet manual
```

重启机器

### 4.pve中虚拟机无法关闭，解决方法。

删除虚拟机ID对应的配置文件

```bash
 rm /var/lock/qemu-server/lock-102.conf    #102是你的虚拟机编号
```

 然后关闭虚拟机

```bash
qm stop 102
```

### 5.centos7.9常用工具一键安装

```bash
yum -y install wget gcc gcc-c++ pcre pcre-devel zlib zlib-devel ruby openssl openssl-devel patch lsof unzip zip lrzsz bash-completion net-tools nmap-ncat
```

### 6.Proxmox VE 7.2 iso 镜像重置 pve root密码

- u盘引导进入pve安装界面，选择Advanced Options。


- 选择 Debug mode
- 进入后发现默认在/路径下，继续按CTRL D，进入root@proxmox:/ 
- 验证LVM并挂载

```bash
#查看VG，输出应该是有VG PVE
vgs
 
#查看LV，可以看到 data root 
lvs
```

- 激活VG

```bash
vgchange -a y 
```

- 挂载lvm

```bash
mount /dev/mapper/pve-root /mnt
```

- chroot

```bash
chroot /mnt
```

- 修改root密码，更改完成密码重启服务器

```bash
passwd root
```

### 7.Proxmox集群部分节点出现问号

决定pve上状态的服务是pvestat daemon，也就是`pvestatd`，因此查看它的运行状况，果然有问题！

```bash
root@www:~# service pvestatd status
● pvestatd.service - PVE Status Daemon
     Loaded: loaded (/lib/systemd/system/pvestatd.service; enabled; vendor preset: enabled)
     Active: failed (Result: signal) since Wed 2022-11-16 09:23:04 CST; 10min ago
    Process: 1146 ExecStart=/usr/bin/pvestatd start (code=killed, signal=KILL)
        CPU: 286ms

Nov 16 09:23:03 www systemd[1]: Starting PVE Status Daemon...
Nov 16 09:23:04 www systemd[1]: pvestatd.service: Control process exited, code=killed, status=9/KILL
Nov 16 09:23:04 www systemd[1]: pvestatd.service: Failed with result 'signal'.
Nov 16 09:23:04 www systemd[1]: Failed to start PVE Status Daemon.
```

重启该服务状态恢复正常

```bash
root@www:~# service pvestatd restart 
root@www:~# service pvestatd status
● pvestatd.service - PVE Status Daemon
     Loaded: loaded (/lib/systemd/system/pvestatd.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2022-11-16 09:34:06 CST; 1s ago
    Process: 1510 ExecStart=/usr/bin/pvestatd start (code=exited, status=0/SUCCESS)
   Main PID: 1511 (pvestatd)
      Tasks: 1 (limit: 18701)
     Memory: 79.2M
        CPU: 851ms
     CGroup: /system.slice/pvestatd.service
             └─1511 pvestatd

Nov 16 09:34:05 www systemd[1]: Starting PVE Status Daemon...
Nov 16 09:34:06 www pvestatd[1511]: starting server
Nov 16 09:34:06 www systemd[1]: Started PVE Status Daemon.
```

### 8.解决pve创建的虚拟机无法复制粘贴

**配置xterm.js**

PVE创建虚拟机后默认只有novnc方法连接虚拟机，无法复制粘贴，很不方便，因此可以打开xterm.js的方式弥补此缺陷。

关闭 VM，并且新增 serial port 给 VM 在PVE Host 中用 qm 命令建立 serial port，假设我的 VM ID 是 100

```
qm set 100 -serial0 socket
```

重新开启 VM，用 dmesg 验证是否有 ttyS 出现。

```
dmesg | grep ttyS
```

如果出现如下代表有了

```
[    3.521757] 00:04: ttyS0 at I/O 0x3f8 (irq = 4, base_baud = 115200) is a 16550A
```

接下来配置VM的grub，进入VM的ssh

```
cd /etc/default/
vi grub
```

修改grub中的GRUB_CMDLINE_LINUX值

```
GRUB_CMDLINE_LINUX="quiet console=tty0 console=ttyS0,115200”
```

更新grub

debian based

```
update-grub
```

redhat based

```sh
grub2-mkconfig --output=/boot/grub2/grub.cfg
```

在虚拟机添加

```
/etc/init/ttyS0.conf
```

并添加如下

```
# ttyS0 - getty
#
# This service maintains a getty on ttyS0 from the point the system is
# started until it is shut down again.
start on stopped rc RUNLEVEL=[12345]
stop on runlevel [!12345]
respawn
exec /sbin/getty -L 115200 ttyS0 vt102
```

设置自启动

```
systemctl enable serial-getty@ttyS0.service
systemctl start serial-getty@ttyS0.service
```

重启VM，测试xterm，已经可以用。

官方文档如下：https://pve.proxmox.com/wiki/Serial_Terminal

