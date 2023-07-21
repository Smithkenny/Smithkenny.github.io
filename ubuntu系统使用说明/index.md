# ubuntu系统使用说明

### 一、Ubuntu20.10 server LTS安装

LTS（长期支持版）

支持以下处理器

```shell
amd64 (Intel/AMD 64-bit)
arm64 (64-bit ARM)
ppc64el (POWER8 and POWER9)
s390x (IBM Z and LinuxONE)
```

最低配置

```shell
CPU: 1 gigahertz or better
RAM: 1 gigabyte or more
Disk: a minimum of 2.5 gigabytes
```

#### **桌面版和服务器版不同**

Ubuntu服务器版和Ubuntu桌面版使用相同的Apt库。

一个主要区别是用于桌面版的图形环境没有为服务器安装。这包括图形服务器本身、图形实用程序和应用程序，以及桌面用户所需的各种用户支持服务。

如果这不是第一次在您的计算机上安装操作系统，您可能需要重新分区您的磁盘以为 Ubuntu 腾出空间。

#### 镜像下载地址
```shell
https://releases.ubuntu.com/20.04/
```

开机引导启动

```shell
ESC、F2、F10、或F12
```

快捷键

```shell
ESC	go back
F1	open help menu
Control-Z, F2	switch to shell
Control-L, F3	redraw screen
Control-T, F4	toggle rich mode (colour, unicode) on and off
```

#### 更换清华源

[参考](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)

1.备份源文件

```shell
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

2.打开sources.list:

```shell
sudo gedit /etc/apt/sources.list
```

加入清华源

```shell
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security multiverse
```

3.刷新软件源信息：

```shell
sudo apt-get update
```

4.更新软件

```shell
sudo apt-get upgrade
```

5.如果更新报以下错误

```
Ign:1 https://mirrors.tuna.tsinghua.edu.cn/ubuntu focal InRelease
Ign:2 https://mirrors.tuna.tsinghua.edu.cn/ubuntu focal-updates InRelease
Ign:3 https://mirrors.tuna.tsinghua.edu.cn/ubuntu focal-backports InRelease
Ign:4 https://mirrors.tuna.tsinghua.edu.cn/ubuntu focal-security InRelease
Err:5 https://mirrors.tuna.tsinghua.edu.cn/ubuntu focal Release
  Certificate verification failed: The certificate is NOT trusted. The certificate chain uses expired certificate.  Could not handshake: Error in the certificate verification. [IP: 101.6.15.130 443]
Err:6 https://mirrors.tuna.tsinghua.edu.cn/ubuntu focal-updates Release
  Certificate verification failed: The certificate is NOT trusted. The certificate chain uses expired certificate.  Could not handshake: Error in the certificate verification. [IP: 101.6.15.130 443]
Err:7 https://mirrors.tuna.tsinghua.edu.cn/ubuntu focal-backports Release
  Certificate verification failed: The certificate is NOT trusted. The certificate chain uses expired certificate.  Could not handshake: Error in the certificate verification. [IP: 101.6.15.130 443]
Err:8 https://mirrors.tuna.tsinghua.edu.cn/ubuntu focal-security Release
  Certificate verification failed: The certificate is NOT trusted. The certificate chain uses expired certificate.  Could not handshake: Error in the certificate verification. [IP: 101.6.15.130 443]
```

将源中`https`改为`http`即可

### 二、包管理

**后缀名为.deb**

#### **安装软件包nmap**

```shell
sudo apt install nmap
```

#### **卸载软件包nmap**

```shell
sudo apt remove nmap
```

卸载多个包用空格分隔。

#### **更新包索引**

APT 包索引本质上是`/etc/apt/sources.list`文件和`/etc/apt/sources.list.d`目录中定义的存储库中可用包的数据库。

```shell
sudo apt update
```

#### **升级包**

随着时间的推移，您计算机上当前安装的包的更新版本可能会从包存储库中获得（例如安全更新）。要升级你的系统，首先，更新你的包索引，如上所述，然后输入：

```shell
sudo apt upgrade
```

**从低版本到高版本的升级过程**

从16.04到20.04升级路径

需要升级两次，不能跨版本升级。

Ubuntu 16.04 LTS ->Ubuntu 18.04 LTS 

Ubuntu 18.04 LTS->Ubuntu 20.04 LTS

相关版本及状态查阅

```shell
https://wiki.ubuntu.com/Releases?_ga=2.249587526.1765783651.1631927770-1233097571.1631927770
```

要升级运行以下命令：

```shell
sudo apt update
sudo apt upgrade
```

建议使用`do-release-upgrade`服务器版和云镜像上的命令升级系统。此命令可以处理在不同版本之间有时需要的系统配置更改。

```shell
sudo do-release-upgrade
```

可以使用该`-d`标志升级到 Ubuntu 的开发版本。但是，不建议在生产环境中使用开发版本或此标志。

从一个 LTS 升级到下一个 LTS 版本仅在第一个点发布后可用。例如，Ubuntu 18.04 LTS 只会在 20.04.1 点发布后升级到 Ubuntu 20.04 LTS。如果用户希望在点发布之前更新（例如在评估 LTS 升级的机器子集上），用户可以通过`-d`标志强制升级。

```shell
sudo do-release-upgrade -d 
```

**升级文章相关参考**

```shell
https://soulteary.com/2020/04/25/server-upgrade-ubuntu-20-04-lts.html
```

**操作日志**（包括安装、删除软件包）

```shell
/var/log/dpkg.log
```

**帮助**

```shell
apt help
```

### aptitude-包管理工具

aptitude 在删除一个包时，会同时删除本身所依赖的包。这样，系统中不会残留无用的包，整个系统更为干净。

#### **常用包管理命令**

```shell
aptitude update                   更新可用的包列表
　　aptitude upgrade               升级可用的包
　　aptitude dist-upgrade        将系统升级到新的发行版
　　aptitude install pkgname     安装包
　　aptitude remove pkgname  删除包
　　aptitude purge pkgname    删除包及其配置文件
　　aptitude search string        搜索包
　　aptitude show pkgname     显示包的详细信息
　　aptitude clean                   删除下载的包文件
　　aptitude autoclean            仅删除过期的包文件
```

#### **查看程序所需依赖包**

```shell
root@liujie-desktop:~# apt-cache depends freeradius
freeradius
  依赖: lsb-base
  依赖: libc6
  依赖: libfreeradius2
  依赖: libgdbm3
  依赖: libltdl7
  依赖: libpam0g
  依赖: libperl5.10
  依赖: libpython2.6
  依赖: libssl0.9.8
  依赖: zlib1g
  依赖: freeradius-common
  依赖: ssl-cert
  依赖: adduser
  建议: freeradius-ldap
  建议: freeradius-postgresql
  建议: freeradius-mysql
  建议: freeradius-krb5
```

aptitude 安装nmap

```shell
sudo aptitude install nmap
```

aptitude 卸载nmap

```shell
sudo aptitude remove nmap
```

### dpkg-包管理器

**它可以安装、删除和构建包，但与其他包管理系统不同，它不能自动下载和安装包或其依赖项。Apt 和 Aptitude 是较新的，并且在 dpkg 之上添加了附加功能。**

列出系统包数据库中的所有包，包括所有已安装和已卸载的包。

```shell
dpkg -l
```

通过 grep 管道输出以查看是否安装了特定的包：

```
dpkg -l | grep apache2
```

要列出包安装的文件，在本例中为 ufw 包，请输入：

```
dpkg -L ufw
```

如果你不确定安装了哪个包的文件，`dpkg -S`也许可以告诉你。例如：

```
dpkg -S /etc/host.conf base-files: /etc/host.conf
```

输出显示`/etc/host.conf`属于 base-files 包。

您可以`.deb`通过输入以下内容来安装本地文件：

```shell
sudo dpkg -i zip_3.0-4_amd64.deb
```

更改`zip_3.0-4_amd64.deb`为要安装的本地 .deb 文件的实际文件名。

卸载软件包可以通过以下方式完成：

```
sudo dpkg -r zip
```

注意：在大多数情况下，不推荐使用 dpkg 卸载软件包。最好使用处理依赖关系的包管理器，以确保系统处于一致状态。它不会卸载相关依赖包。

### 自动更新

unattended-upgrades 软件包可用于自动安装更新的软件包，并可配置为更新所有软件包或仅安装安全更新。首先，通过在终端中输入以下内容来安装包

```shell
sudo apt install unattended-upgrades
```

要配置无人值守升级，请编辑`/etc/apt/apt.conf.d/50unattended-upgrades`和调整以下内容以满足您的需要：

```
Unattended-Upgrade::Allowed-Origins {
        "${distro_id}:${distro_codename}";
        "${distro_id}:${distro_codename}-security";
//      "${distro_id}:${distro_codename}-updates";
//      "${distro_id}:${distro_codename}-proposed";
//      "${distro_id}:${distro_codename}-backports";
};
```

某些软件包也可能被*列入黑名单*，因此不会自动更新。要将软件包列入黑名单，请将其添加到列表中：

```
Unattended-Upgrade::Package-Blacklist {
//      "vim";
//      "libc6";
//      "libc6-dev";
//      "libc6-i686";
};
```

// 用作注释

要启用自动更新，请编辑`/etc/apt/apt.conf.d/20auto-upgrades`并设置适当的 apt 配置选项：

```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::AutocleanInterval "7";
APT::Periodic::Unattended-Upgrade "1";
```

以上配置每天更新包列表、下载和安装可用升级。本地下载存档每周清理一次。在升级到较新版本 Ubuntu 的服务器上，根据您的回答，上面列出的文件可能不存在。在这种情况下，创建此名称的新文件也应该有效。

**帮助文档**

您可以在`apt.conf(5)`联机帮助页和`/usr/lib/apt/apt.systemd.daily`脚本标题中阅读有关 apt Periodic 配置选项的更多信息。

无人值守升级的结果将被记录到`/var/log/unattended-upgrades`.

### apticron 

定时向系统管理员发邮件，类似于计划任务。

apticron 将配置一个 cron 作业，以通过电子邮件向管理员发送有关系统上具有可用更新的任何软件包的信息，以及每个软件包中更改的摘要。

安装 apticron 软件包

```shell
sudo apt install apticron
```

安装包后 edit `/etc/apticron/apticron.conf`，设置电子邮件地址和其他选项：

```
EMAIL="root@example.com"
```



### 三、无人值守批量安装Ubuntu server 20.04.3

使用PXE自动从网络加载安装程序，发起安装。

使用Ubuntu的自动安装功能，自动加载cloud-init安装配置，完成操作系统的自动选择。

需要装有pxe的服务端，服务器需要安装的软件有tftp客户端，dnsmasq 服务端，tftp服务端可以在dnsmasq.conf中配置。

```shell
apt install tftp
apt install dnsmasq
```

需要安装系统的客户端（此时用虚拟机创建一个空的虚拟机。需要第一启动项为网卡启动，并且默认uefi启动）

#### PXE 安装流程

PXE（Pre-boot Execution Environment/预启动执行环境）的实现依赖于网卡，只有支持PXE Client的网卡才能实现网络自动安装。这种网卡实现DHCP Client和 TFTP Client，在BIOS的引导下通过DHCP协议自动分配IP地址，通过TFTP获取最小内核，然后在最小内核环境下通过HTTP协议或NFS协议获取Ubuntu安装版本。之后最小内核引导进行Ubuntu 20.04的安装。下图是详细的安装流程。

![无人值守1](/postimages/无人值守1.webp)

上图有几个前提：

1. 网卡支持PXE，今年新出的网卡基本都支持，同时BIOS的启动项也要配置，请大家自行研究。
2. UEFI启动才会请求bootx64.efi，如果是传统启动模式（Legacy），那么PXE Client会请求pxelinux.0。
3. 可以采用nfsboot方式，这个流程采用的是iso镜像下载再安装的方式。

以下引导方式为uefi，虚拟机客户端需要将bios模式改为uefi模式，同时，将开机启动项将从网卡启动设置为第一启动项。

![无人值守2](/postimages/无人值守2.webp)

![noskip](/postimages/noskip.png)

#### 安装必须的服务端软件

1. 安装DHCP、TFTP服务器
   dnsmasq同时实现了DHCP、TFTP、DNS三种服务器
   `sudo apt-get install dnsmasq`
2. 安装HTTP服务器
   HTTP Server有很多，大家可以使用自己的熟悉的服务器如nginx，这里使用的是apache2
   `sudo apt-get install apache2`

#### 准备启动文件

1. 下载uefi 引导文件，shim.signed、grub-efi-amd64-signed 安全启动链加载的引导程序

   ```arduino
   apt-get download shim.signed
   apt-get download grub-efi-amd64-signed
   ```

2. 下载ubuntu 20.04 server iso
   这个直接去官网下载，我下载的是 ubuntu-20.04.3-live-server-amd64.iso，需要注意的是只有live版本才能支持subiquity
   `https://ubuntu.com/download/server`

#### 创建TFTP 文件夹

TFTP 文件夹是TFTP服务的根目录，PXE启动过程中下载的文件都存在在该目录中

```mipsasm
tftp
├── boot
│   └── live-server
│       ├── initrd
│       └── vmlinuz
├── grub
│   ├── bootx64.efi
│   ├── font.pf2
│   └── grub.cfg
└── grubx64.efi
```

#### 说明

1. bootx64.efi、grubx64.efi 引导程序，来自shim.signed 安装包
2. grub.cfg 自建
3. 其他文件来自 ubuntu安装包
4. 五个文件是需要的，但是目录结构是我自建的，大家可以根据自己的喜好修改

#### 创建目录

在 /tftp (可根据实际情况修改)目录下创建 tftp目录

```awk
mkdir /tftp
mkdir /tftp/grub
mkdir /tftp/boot
mkdir /tftp/boot/live-server
```

#### 获取引导文件

在安装包下载目录创建一个shim文件夹

解压shim安装包到shim文件夹
`dpkg -x <%刚才下载的shim.signed 安装包包名%> shim`

解压grub安装包到grub文件夹

拷贝引导文件到tftp目录

```awk
cp ./root/grub/usr/lib/grub/x86_64-efi-signed/grubnetx64.efi.signed  /tftp/grubx64.efi
cp ./root/shim/usr/lib/shim/shimx64.efi.signed  /tftp/grub/bootx64.efi
```

#### 获取内核镜像文件

在下载目录mount iso文件

```nginx
sudo mount ubuntu-20.04.3-live-server-amd64.iso /media
```

系统会提示只读，不影响使用

```pgsql
mount: /media: WARNING: device write-protected, mounted read-only.
```

拷贝内核镜像文件

```awk
cp /media/casper/initrd       /tftp/boot/live-server
cp /media/casper/vmlinuz      /tftp/boot/live-server
```

拷贝grub文件
grub.cfg拷贝过来做个参考，内容会被全部修改掉

```awk
cp /media/grub/font.pf2       /tftp/grub
cp /media/grub/grub.cfg       /tftp/grub
```

### 配置dnsmasq（默认端口53udp）

tftp默认端口69

关键配置有以下几个

1. 配置DHCP 地址段
2. 配置引导文件目录
3. 配置tftp 根目录
4. 配置日志路径
5. 配置服务网卡，多网卡机器需关注

```routeros
# 配置外网DNS服务器地址
server=10.4.7.254
# 指定服务的网卡
interface=ens33,lo
# 绑定端口
bind-interfaces
# 设置DHCP分发IP端范围、地址掩码、IP地址有效时间
dhcp-range=10.4.7.240,10.4.7.241,255.255.255.0,12h
# 指定网关地址   和安装无关，应该可以不配置
dhcp-option=3,10.4.7.254
# 指定DNS服务器地址  和安装无关，应该可以不配置
dhcp-option=6,10.4.7.254
# 设置引导程序相对tftp根目录的路径
dhcp-match=set:efi-x86_64,option:client-arch,7
dhcp-boot=tag:efi-x86_64,grub/bootx64.efi
# 打开tftp服务
enable-tftp
# 设置tftp根路径
tftp-root=/tftp
# 设置日志路径
log-facility=/var/log/dnsmasq.log
```

修改配置后，重启dnsmasq服务才能生效。

### 创建HTTP文件夹

apache2的默认服务根目录是 /var/www/html，不修改他，在他下面创建目录

```haskell
html/
├── autoinstall
│   ├── meta-data
│   └── user-data
├── index.html
└── iso
    └── ubuntu-20.04.2-live-server-amd64.iso
```

#### 说明

autoinstall 目录存放参数自动配置文件，user-data、meta-data是cloud-init要求的文件名

iso 目录存放操作系统镜像文件

#### 创建目录

```awk
mkdir /var/www/html/autoinstall
mkdir /var/www/html/iso
```

#### 拷贝iso文件

到下载目录拷贝iso文件
`cp ubuntu-20.04.3-live-server-amd64.iso /var/www/html/iso`

#### 创建参数自动配置文件

先创建空文件，meta-data无需修改，user-data后续章节会详细描述配置

```awk
touch /var/www/html/autoinstall/user-data
touch /var/www/html/autoinstall/meta-data
```

### 配置grub.cfg

```routeros
if loadfont /grub/font.pf2 ; then
        set gfxmode=auto
        insmod efi_gop
        insmod efi_uga
        insmod gfxterm
        terminal_output gfxterm
fi

set menu_color_normal=white/black
set menu_color_highlight=black/light-gray
set timeout=5

menuentry "Ubuntu server 20.04.3 autoinstall" {
        set gfxpayload=keep
        linux  /boot/live-server/vmlinuz root=/dev/ram0 ramdisk_size=1500000 ip=dhcp url='http://10.4.7.150/iso/ubuntu-20.04.3-live-server-amd64.iso' autoinstall ds=nocloud-net\;s=http://10.4.7.150/autoinstall/ ---
        initrd /boot/live-server/initrd
}
```

menuentry 之前是配置样式，也可以删除，重点关注`menuentry "Ubuntu server 20.04 autoinstall"`
内的配置。

1. 指定镜像文件相对于tftp根目录的路径 /boot/live-server/initrd
2. root=/dev/ram0 ramdisk_size=1500000 为了指定内核镜像挂载空间，是否可删除待确认
3. ip=dhcp 指定内核镜像挂载后使用DHCP 获取IP地址
4. url= 指定ISO文件的网络存放路径
5. `autoinstall ds=nocloud-net\;s=http://10.4.7.150/autoinstall/ ---` 该配置指明参数自动填写，并指明配置文件所在路径

#### 坑

网上很多文章配置是这么写的`ds=nocloud-net;s=http://10.4.7.150/autoinstall/`，我试了很多次，都没有自动安装。
在网上查到，由于UEFI启动使用了grub，grub将;识别为了特殊字符，所以要在;前加\转义。

### 配置user-data

```yaml
#cloud-config
autoinstall:
  version: 1
  # 修改apt 服务地址
  apt:
    primary:
    - arches: [default]
      uri: https://mirrors.tuna.tsinghua.edu.cn/ubuntu
  user-data:
    # 配置时区
    timezone: Asia/Shanghai
    # 去使能 root账号
    disable_root: true
  # 配置用户  
  identity:
    hostname: ubuntu-server
    password: "yours"
    username: ubuntu
  # 配置键盘  
  keyboard: {layout: us, variant: 'us'}
  locale: en_US.UTF-8
  # 默认安装ssh server
  ssh:
    install-server: true
  # 指定安装的包
  packages:
    - net-tools
    - python3-pip
  # 配置磁盘分区
  storage:
    grub:
      reorder_uefi: False
    config:
    - {ptable: gpt, path: /dev/sda, wipe: superblock-recursive, preserve: false, name: '',
      grub_device: false, type: disk, id: disk-sda}
    - {device: disk-sda, size: 536870912, wipe: superblock, flag: boot, number: 1,
      preserve: false, grub_device: true, type: partition, id: partition-0}
    - {fstype: fat32, volume: partition-0, preserve: false, type: format, id: format-0}
    - {device: disk-sda, size: -1, wipe: superblock, flag: '', number: 2,
      preserve: false, type: partition, id: partition-1}
    - {fstype: ext4, volume: partition-1, preserve: false, type: format, id: format-1}
    - {device: format-1, path: /, type: mount, id: mount-1}
    - {device: format-0, path: /boot/efi, type: mount, id: mount-0}
```

#### 说明

1. 密码需要加密，可以先用工具对自己的密码进行加密后填入
2. 代理不是必须的配置，与网络拓扑有关
3. 磁盘分区配置要注意，配置不对会导致自动安装走不下去，提示crash；这个配置的整体思路是先格式化disk-sda，然后在disk-sda下划分/dev/sda1 、/dev/sda2 ，然后分别mount /,/boot/efi目录
4. 安装过程日志在 /var/log/installer/，如果安装失败可以通过nc 等工具实时发出去

### 网络拓扑

1. 我在电脑上搭建了DHCP、TFTP、HTTP三种服务
2. 目标机器不能上网，三台机器在同一个局域网

 dnsmasq.log 

![无人值守3](/postimages/无人值守3.webp)

```shell
Sep 21 19:26:56 dnsmasq-dhcp[1910]: DHCP, IP range 10.4.7.240 -- 10.4.7.241, lease time 12h
Sep 21 19:26:56 dnsmasq-dhcp[1910]: DHCP, sockets bound exclusively to interface ens33
Sep 21 19:26:56 dnsmasq-tftp[1910]: TFTP root is /tftp  
Sep 21 19:29:33 dnsmasq-tftp[1910]: failed sending /tftp/grubx64.efi to 10.4.7.140
Sep 21 19:35:37 dnsmasq-dhcp[1910]: DHCPDISCOVER(ens33) 00:0c:29:68:21:9a 
Sep 21 19:35:37 dnsmasq-dhcp[1910]: DHCPOFFER(ens33) 10.4.7.240 00:0c:29:68:21:9a 
Sep 21 19:35:38 dnsmasq-dhcp[1910]: DHCPREQUEST(ens33) 10.4.7.240 00:0c:29:68:21:9a 
Sep 21 19:35:38 dnsmasq-dhcp[1910]: DHCPACK(ens33) 10.4.7.240 00:0c:29:68:21:9a 
Sep 21 19:35:38 dnsmasq-tftp[1910]: error 8 User aborted the transfer received from 10.4.7.240
Sep 21 19:35:38 dnsmasq-tftp[1910]: sent /tftp/grub/bootx64.efi to 10.4.7.240
Sep 21 19:35:38 dnsmasq-tftp[1910]: sent /tftp/grub/bootx64.efi to 10.4.7.240
Sep 21 19:35:40 dnsmasq-tftp[1910]: sent /tftp/grubx64.efi to 10.4.7.240
Sep 21 19:35:40 dnsmasq-tftp[1910]: file /tftp/grub/x86_64-efi/command.lst not found
Sep 21 19:35:40 dnsmasq-tftp[1910]: file /tftp/grub/x86_64-efi/fs.lst not found
Sep 21 19:35:40 dnsmasq-tftp[1910]: file /tftp/grub/x86_64-efi/crypto.lst not found
Sep 21 19:35:40 dnsmasq-tftp[1910]: file /tftp/grub/x86_64-efi/terminal.lst not found
Sep 21 19:35:40 dnsmasq-tftp[1910]: sent /tftp/grub/grub.cfg to 10.4.7.240
Sep 21 19:35:40 dnsmasq-tftp[1910]: sent /tftp/grub/font.pf2 to 10.4.7.240
Sep 21 19:35:51 dnsmasq-tftp[1910]: sent /tftp/boot/live-server/vmlinuz to 10.4.7.240
```

### 四、Ubuntu 网络配置

要快速识别所有可用的以太网接口，您可以使用如下所示的 ip 命令。

```shell
root@lhp-server:/etc/netplan# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:da:c6:09 brd ff:ff:ff:ff:ff:ff
    inet 10.4.7.142/24 brd 10.4.7.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:feda:c609/64 scope link 
       valid_lft forever preferred_lft forever
```

另一个可以帮助识别系统可用的所有网络接口的应用程序是 lshw 命令。此命令提供有关特定适配器的硬件功能的更多详细信息。在下面的示例中，lshw 显示了一个具有*eth1*逻辑名称的以太网接口以及总线信息、驱动程序详细信息和所有支持的功能。

```shell
root@lhp-server:/etc/netplan# lshw -class network
  *-network                 
       description: Ethernet interface
       product: 82545EM Gigabit Ethernet Controller (Copper)
       vendor: Intel Corporation
       physical id: 1
       bus info: pci@0000:02:01.0
       logical name: ens33
       version: 01
       serial: 00:0c:29:da:c6:09
       size: 1Gbit/s
       capacity: 1Gbit/s
       width: 64 bits
       clock: 66MHz
       capabilities: pm pcix bus_master cap_list rom ethernet physical logical tp 10bt 10bt-fd 100bt 100bt-fd 1000bt-fd autonegotiation
       configuration: autonegotiation=on broadcast=yes driver=e1000 driverversion=7.3.21-k8-NAPI duplex=full ip=10.4.7.142 latency=0 link=yes mingnt=255 multicast=yes port=twisted pair speed=1Gbit/s
       resources: irq:19 memory:fd5c0000-fd5dffff memory:fdff0000-fdffffff ioport:2000(size=64) memory:fd500000-fd50ffff
```

ethtool 是一个程序，用于显示和更改以太网卡设置，例如自动协商、端口速度、双工模式和 LAN 唤醒。以下是如何查看以太网接口支持的功能和配置设置的示例。

```shell
root@lhp-server:/etc/netplan# ethtool ens33 
Settings for ens33:
	Supported ports: [ TP ]
	Supported link modes:   10baseT/Half 10baseT/Full 
	                        100baseT/Half 100baseT/Full 
	                        1000baseT/Full 
	Supported pause frame use: No
	Supports auto-negotiation: Yes
	Supported FEC modes: Not reported
	Advertised link modes:  10baseT/Half 10baseT/Full 
	                        100baseT/Half 100baseT/Full 
	                        1000baseT/Full 
	Advertised pause frame use: No
	Advertised auto-negotiation: Yes
	Advertised FEC modes: Not reported
	Speed: 1000Mb/s
	Duplex: Full
	Port: Twisted Pair
	PHYAD: 0
	Transceiver: internal
	Auto-negotiation: on
	MDI-X: off (auto)
	Supports Wake-on: d
	Wake-on: d
	Current message level: 0x00000007 (7)
			       drv probe link
	Link detected: yes
```

要临时配置 IP 地址，可以通过以下方式使用 ip 命令。修改 IP 地址和子网掩码以符合您的网络要求。ip 命令允许您配置立即生效的设置，但是它们不是持久的并且会在重新启动后丢失。

```shell
sudo ip addr add 10.102.66.200/24 dev ens33
```

然后可以使用 ip 来设置链接的开启或关闭。

```
ip link set dev ens33 up
ip link set dev ens33 down
```

验证enp0s25的IP地址配置，可以通过以下方式使用ip命令。

```
ip address show dev enp0s25
10: enp0s25: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 00:16:3e:e2:52:42 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.102.66.200/24 brd 10.102.66.255 scope global dynamic eth0
       valid_lft 2857sec preferred_lft 2857sec
    inet6 fe80::216:3eff:fee2:5242/64 scope link
       valid_lft forever preferred_lft forever6
```

要配置默认网关，您可以按以下方式使用 ip 命令。修改默认网关地址以符合您的网络要求。

```
sudo ip route add default via 10.102.66.1
```

要验证您的默认网关配置，您可以按以下方式使用 ip 命令。

```
ip route show
default via 10.102.66.1 dev eth0 proto dhcp src 10.102.66.200 metric 100
10.102.66.0/24 dev eth0 proto kernel scope link src 10.102.66.200
10.102.66.1 dev eth0 proto dhcp scope link src 10.102.66.200 metric 100 
```

如果您的临时网络配置需要 DNS，您可以在文件中添加 DNS 服务器 IP 地址`/etc/resolv.conf`。一般情况下，`/etc/resolv.conf`不建议直接编辑，但这是一种临时且非持久的配置。下面的示例显示了如何将两个 DNS 服务器输入到 中`/etc/resolv.conf`，应将其更改为适合您网络的服务器。以下部分详细介绍了进行 DNS 客户端配置的正确持久方式。

```
nameserver 8.8.8.8
nameserver 8.8.4.4
```

如果您不再需要此配置并希望从接口中清除所有 IP 配置，您可以使用带有flush 选项的ip 命令，如下所示。

```
ip addr flush eth0
```

> **笔记**
>
> 使用 ip 命令刷新 IP 配置不会清除`/etc/resolv.conf`. 您必须手动删除或修改这些条目，或者重新启动，这也应该导致`/etc/resolv.conf`，它是到 的符号链接`/run/systemd/resolve/stub-resolv.conf`，被重写。

### 动态 IP 地址分配（DHCP 客户端）

要将您的服务器配置为使用 DHCP 进行动态地址分配，请在文件`/etc/netplan/99_config.yaml`. 下面的示例假设您正在配置标识为*enp3s0 的*第一个以太网接口。

```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp3s0:
      dhcp4: true
```

然后可以使用 netplan 命令应用配置。

```
sudo netplan apply
```

### 静态 IP 地址分配

要将系统配置为使用静态地址分配，请在文件`/etc/netplan/99_config.yaml`. 下面的示例假设您正在配置标识为*eth0 的*第一个以太网接口。更改*address*、*gateway4*和*nameservers*值以满足您的网络要求。

```
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      addresses:
        - 10.10.10.2/24
      gateway4: 10.10.10.1
      nameservers:
          search: [mydomain, otherdomain]
          addresses: [10.10.10.1, 1.1.1.1]
```

然后可以使用 netplan 命令应用配置。

```
sudo netplan apply
```

**注：早期版本如：Ubuntu 18.04等等，它们的配置文件在/etc/network/interface文件中。**

网卡重启：

```shell
ip link set ens33 down ip link set ens33 up 
```

### 环回接口

环回接口由系统标识为*lo*，默认 IP 地址为 127.0.0.1。可以使用ip命令查看。

```
ip address show lo
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
```

### 名称解析

与 IP 网络相关的名称解析是将 IP 地址映射到主机名的过程，从而更容易识别网络上的资源。以下部分将解释如何正确配置系统以使用 DNS 和静态主机名记录进行名称解析。

### DNS 客户端配置

传统上，该文件`/etc/resolv.conf`是一个静态配置文件，很少需要更改或通过 DCHP 客户端挂钩自动更改。Systemd-resolved 处理名称服务器配置，它应该通过`systemd-resolve`命令进行交互。Netplan 配置 systemd-resolved 以生成要放入的名称服务器和域列表`/etc/resolv.conf`，这是一个符号链接：

```
/etc/resolv.conf -> ../run/systemd/resolve/stub-resolv.conf
```

要配置解析器，请将适合您网络的名称服务器的 IP 地址添加到 netplan 配置文件中。您还可以添加可选的 DNS 后缀搜索列表以匹配您的网络域名。生成的文件可能如下所示：

```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s25:
      addresses:
        - 192.168.0.100/24
      gateway4: 192.168.0.1
      nameservers:
          search: [mydomain, otherdomain]
          addresses: [1.1.1.1, 8.8.8.8, 4.4.4.4]
```

该搜索选项也可以用多个域名使用，使得DNS查询将按照它们的输入顺序追加。例如，您的网络可能有多个要搜索的子域的父域*`example.com`*，和两个子域，*`sales.example.com`*以及*`dev.example.com`*.

如果您有多个要搜索的域，您的配置可能如下所示：

```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s25:
      addresses:
        - 192.168.0.100/24
      gateway4: 192.168.0.1
      nameservers:
          search: [example.com, sales.example.com, dev.example.com]
          addresses: [1.1.1.1, 8.8.8.8, 4.4.4.4]
```

如果您尝试 ping 名为*server1*的主机，您的系统将按以下顺序自动查询 DNS 以获取其完全限定域名 (FQDN)：

1. `server1.example.com`
2. `server1.sales.example.com`
3. `server1.dev.example.com`

如果未找到匹配项，DNS 服务器将提供*notfound*结果并且 DNS 查询将失败。

### 静态主机名

静态主机名是位于文件中的本地定义的主机名到 IP 的映射`/etc/hosts`。`hosts`默认情况下，文件中的条目优先于 DNS。这意味着如果您的系统尝试解析主机名并且它与 /etc/hosts 中的条目匹配，则它不会尝试在 DNS 中查找记录。在某些配置中，尤其是当不需要访问 Internet 时，可以方便地将与有限数量资源通信的服务器设置为使用静态主机名而不是 DNS。

以下是一个`hosts`文件示例，其中通过简单的主机名、别名及其等效的完全限定域名 (FQDN) 标识了多个本地服务器。

```
127.0.0.1   localhost
127.0.1.1   ubuntu-server
10.0.0.11   server1 server1.example.com vpn
10.0.0.12   server2 server2.example.com mail
10.0.0.13   server3 server3.example.com www
10.0.0.14   server4 server4.example.com file
```

> **笔记**
>
> 在上面的示例中，请注意，除了专有名称和 FQDN 之外，每个服务器还被赋予了别名。*Server1*已映射到名称*vpn*，*server2*称为*mail*，*server3*称为*www*，*server4*称为*file*。

### 名称服务交换机配置

系统选择将主机名解析为 IP 地址的方法的顺序由名称服务交换机 (NSS) 配置文件控制`/etc/nsswitch.conf`。如上一节所述，通常在系统`/etc/hosts`文件中定义的静态主机名优先于从 DNS 解析的名称。以下是负责文件中主机名查找顺序的行的示例`/etc/nsswitch.conf`。

```
hosts:          files mdns4_minimal [NOTFOUND=return] dns mdns4
```

- **文件**首先尝试解析位于`/etc/hosts`.
- **mdns4_minimal**尝试使用多播 DNS 解析名称。
- **[NOTFOUND =返回]**意味着任何响应*NOTFOUND*由前述*mdns4_minimal*过程应为权威并且该系统不应该试图继续狩猎的答案进行处理。
- **dns**代表传统的单播 DNS 查询。
- **mdns4**表示多播 DNS 查询。

要修改上述名称解析方法的顺序，您可以简单地将*hosts:*字符串更改为您选择的值。例如，如果您更喜欢使用传统的单播 DNS 而不是多播 DNS，您可以更改字符串，`/etc/nsswitch.conf`如下所示。

```
hosts: files dns [NOTFOUND=return] mdns4_minimal mdns4
```

### 桥接

桥接多个接口是一种更高级的配置，但在多个场景中非常有用。一种方案是建立具有多个网络接口的网桥，然后使用防火墙过滤两个网段之间的流量。另一种情况是在具有一个接口的系统上使用桥接器，以允许虚拟机直接访问外部网络。以下示例涵盖了后一种情况。

通过编辑您在`/etc/netplan/`以下位置找到的 netplan 配置来配置网桥：

```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp3s0:
      dhcp4: no
  bridges:
    br0:
      dhcp4: yes
      interfaces:
        - enp3s0
```

> **笔记**
>
> 为您的物理接口和网络输入适当的值。

现在应用配置以启用网桥：

```
sudo netplan apply
```

新的桥接接口现在应该已经启动并运行了。brctl 提供有关网桥状态的有用信息，控制哪些接口是网桥的一部分等。有关`man brctl`更多信息，请参见。

### DHCP

DHCP 服务器可以使用以下方法提供配置设置：

- 手动分配（MAC 地址）
  这种方法需要使用 DHCP 来识别连接到网络的每个网卡的唯一硬件地址，然后在每次 DHCP 客户端使用该网络设备向 DHCP 服务器发出请求时不断提供恒定配置。这可确保根据其 MAC 地址自动将特定地址分配给该网卡。
- 动态分配（地址池）
  在这种方法中，DHCP 服务器将从地址池（有时也称为范围或范围）中分配一个 IP 地址，用于一段时间或租用，即在服务器上配置或直到客户端通知服务器它不再需要该地址。这样，客户端将在“先到先得”的基础上动态接收其配置属性。当 DHCP 客户端在指定时间段内不再位于网络上时，配置将过期并释放回地址池以供其他 DHCP 客户端使用。这样，一个地址可以被租用或使用一段时间。在此期间之后，客户端必须与服务器重新协商租用以维持地址的使用。
- 自动分配
  使用这种方法，DHCP 会自动为设备永久分配一个 IP 地址，从可用地址池中选择它。通常 DHCP 用于为客户端分配临时地址，但 DHCP 服务器可以允许无限的租用时间。

后两种方法可以被认为是“自动的”，因为在每种情况下，DHCP 服务器都会分配一个地址，而无需额外干预。它们之间的唯一区别在于 IP 地址的租用时间长短，换句话说，客户端的地址是否随时间变化。Ubuntu 提供的 DHCP 服务器是 dhcpd（动态主机配置协议守护进程），它易于安装和配置，并且会在系统启动时自动启动。

安装

在终端提示符下，输入以下命令来安装 dhcpd：

```
sudo apt install isc-dhcp-server
```

注意：dhcpd 的消息正在发送到 syslog。在那里查找诊断消息。

配置

您可能需要通过编辑来更改默认配置`/etc/dhcp/dhcpd.conf`以满足您的需要和特定配置。

最常见的是，您想要做的是随机分配一个 IP 地址。这可以通过如下设置来完成：

```
# minimal sample /etc/dhcp/dhcpd.conf
default-lease-time 600;
max-lease-time 7200;
ddns-update-style none;

subnet 10.4.7.0 netmask 255.255.255.0 {
  range 10.4.7.220 10.4.7.230;
  option routers 10.4.7.254;
  option subnet-mask 255.255.255.0;
  option broadcast-address 10.4.7.255;
  option domain-name-servers 10.4.7.254;
  option ntp-servers 10.4.7.254;
  option netbios-name-servers 10.4.7.254;
  option netbios-node-type 8;
}
```

这将导致 DHCP 服务器为客户端提供一个范围为 10.4.7.220-10.4.7.230 的 IP 地址。如果客户端不要求特定的时间范围，它将租用一个 IP 地址 600 秒。否则最大（允许）租期将为 7200 秒。服务器还将“建议”客户端使用 10.4.7.254 作为默认网关，使用 10.4.7.254 作为其 DNS 服务器。使用10.4.7.254 作为ntp服务器。使用10.4.7.254作为局域网内部主机名对应的IP。

您可能还需要编辑`/etc/default/isc-dhcp-server`以指定 dhcpd 应侦听的接口。

```
INTERFACESv4="eth4"
```

更改配置文件后，您必须重新启动 dhcpd 服务：

```
sudo systemctl restart isc-dhcp-server.service
```

### NTP

NTP 是一种 TCP/IP 协议，用于通过网络同步时间。基本上，客户端从服务器请求当前时间，并使用它来设置自己的时钟。

在这个简单的描述背后，有很多复杂性——NTP 服务器有很多层，第一层 NTP 服务器连接到原子钟，而第二层和第三层服务器则通过 Internet 分散实际处理请求的负载。Ubuntu 默认使用*timedatectl / timesyncd*来同步时间，用户可以选择使用 chrony 来为网络时间协议提供服务。

同步时间

Ubuntu 16.04 *timedatectl / timesyncd*（它们是 systemd 的一部分）替换了大部分*ntpdate / ntp*。

默认情况下，timesyncd 是可用的，它不仅替换 ntpdate，而且替换 chrony（或以前的 ntpd）的客户端部分。因此，除了 ntpdate 在启动和网络激活时提供的一次性操作之外，现在默认情况下 timesyncd 会定期检查并保持本地时间同步。它还在本地存储时间更新，以便在重新启动后单调前进（如果适用）。

如果安装了 chrony，timedatectl 会退回让 chrony 进行计时。这将确保没有两个时间同步服务发生冲突。虽然不再推荐使用，但这仍然适用于安装 ntpd 以保留您通过升级获得的任何类型的旧行为/配置。但这也意味着在从以前的版本升级时可能仍会安装 ntp/ntpdate，因此会禁用新的基于 systemd 的服务。

ntpdate 被认为已弃用，取而代之的是 timedatectl（或 chrony），因此默认情况下不再安装。timesyncd 通常会做正确的事情，使您的时间保持同步，而 chrony 将帮助处理更复杂的情况。

需要一次性同步：

```shell
chronyd -q
```

需要一次性检查时间，而不设置时间:

```shell
chronyd -Q
```

timesyncd 和chronyd 同时存在时，只有一个生效，如果chronyd正在运行，那么chronyd生效。

通过 timedatectl 和 timesyncd 可以检查时间和时间配置的当前状态`timedatectl status`。

```auto
$ timedatectl status
                       Local time: Fr 2018-02-23 08:47:13 UTC
                   Universal time: Fr 2018-02-23 08:47:13 UTC
                         RTC time: Fr 2018-02-23 08:47:13
                        Time zone: Etc/UTC (UTC, +0000)
        System clock synchronized: yes
 systemd-timesyncd.service active: yes
                  RTC in local TZ: no
```

如果 chrony 正在运行，它将自动切换到：

```auto
[...]
 systemd-timesyncd.service active: no 
```

通过 timedatectl 管理员可以控制时区、系统时钟应该如何与 hwclock 相关以及是否应该启用永久同步。有关`man timedatectl`更多详细信息，请参阅。

timesyncd 本身仍然是一个正常的服务，所以你也可以通过它更详细地检查它的状态。

```shell
root@lhp-server:~# systemctl status systemd-timesyncd 
● systemd-timesyncd.service
     Loaded: masked (Reason: Unit systemd-timesyncd.service is masked.)
     Active: inactive (dead) since Thu 2021-09-23 02:41:56 UTC; 35min ago
   Main PID: 736 (code=exited, status=0/SUCCESS)
     Status: "Shutting down..."

Sep 23 01:53:57 lhp-server systemd[1]: Starting Network Time Synchronization...
Sep 23 01:53:57 lhp-server systemd[1]: Started Network Time Synchronization.
Sep 23 01:54:05 lhp-server systemd-timesyncd[736]: Network configuration changed, trying to establish connection.
Sep 23 01:54:06 lhp-server systemd-timesyncd[736]: Network configuration changed, trying to establish connection.
Sep 23 01:54:39 lhp-server systemd-timesyncd[736]: Initial synchronization to time server 91.189.89.199:123 (ntp.ubuntu.com).
Sep 23 02:41:56 lhp-server systemd[1]: Stopping Network Time Synchronization...
Sep 23 02:41:56 lhp-server systemd[1]: systemd-timesyncd.service: Succeeded.
Sep 23 02:41:56 lhp-server systemd[1]: Stopped Network Time Synchronization.
```

可以从这两个配置文件中获取到根域名服务器的时间：/etc/systemd/timesyncd.conf和/etc/systemd/timesyncd.conf.d/，更多信息可以查看man timesyncd.conf。

### Chrony

安装

```shell
sudo apt install chrony
```

这将提供两个二进制文件：

- chronyd - 通过 NTP 协议同步和服务的实际守护进程
- chronyc - chrony 守护进程的命令行界面

配置：

编辑`/etc/chrony/chrony.conf`以添加/删除服务器行。默认情况下，这些服务器配置为：

```
# Use servers from the NTP Pool Project. Approved by Ubuntu Technical Board
# on 2011-02-08 (LP: #104525). See http://www.pool.ntp.org/join.html for
# more information.
pool 0.ubuntu.pool.ntp.org iburst
pool 1.ubuntu.pool.ntp.org iburst
pool 2.ubuntu.pool.ntp.org iburst
pool 3.ubuntu.pool.ntp.org iburst
```

有关`man chrony.conf`配置选项的更多详细信息，请参阅。更改任何配置文件后，您必须重新启动chrony：

```
sudo systemctl restart chrony.service
```

如果需要，`pool 2.ubuntu.pool.ntp.org`以及`ntp.ubuntu.com`还支持 ipv6。如果需要强制使用 ipv6 `ipv6.ntp.ubuntu.com`，默认情况下也没有配置。

查看状态

使用 chronyc 查看查询 chrony 守护进程的状态。例如，获取当前可用和选定时间源的概览.

```shell
root@lhp-server:~# chronyc sources 210 Number of sources = 8MS Name/IP address         Stratum Poll Reach LastRx Last sample               ===============================================================================^- chilipepper.canonical.com     2   6   377    43  +8909us[+8909us] +/-  192ms^- alphyn.canonical.com          2   7   175   108    +15ms[  +15ms] +/-  167ms^- golem.canonical.com           2   6   377    49    +17ms[  +17ms] +/-  188ms^- pugot.canonical.com           2   6   377    51    +16ms[  +16ms] +/-  178ms^- 139.199.215.251               2   6   377    53  -1308us[-1350us] +/-   32ms^* 202.118.1.130                 1   6   377    53   -113us[ -155us] +/- 8565us^- tock.ntp.infomaniak.ch        1   6   377    49    +36ms[  +36ms] +/-  118ms^- 111.230.189.174               2   7   237    54  -2129us[-2171us] +/-   27ms
```

某些 chronyc 命令是特权命令，未经明确允许无法通过网络运行。见*指挥和监控访问*中`man chrony.conf`了解更多详情。本地管理员通常可以使用 sudo ，因为这将授予他访问本地管理员套接字的权限`/var/run/chrony/chronyd.sock`。

### 五、安全

用户管理

root用户默认禁止访问。

鼓励用户使用名为“sudo”的工具来执行系统管理职责。Sudo 允许授权用户使用他们自己的密码临时提升他们的权限，而不必知道属于 root 帐户的密码。这种简单而有效的方法为所有用户操作提供了问责制，并使管理员可以精细控制用户可以使用所述权限执行哪些操作。

启用root用户并设置新密码：

```shell
sudo passwd
```

Sudo 将提示您输入密码，然后要求您为 root 提供一个新密码，如下所示：

```
[sudo] password for username: (enter your own password)
Enter new UNIX password: (enter a new password for root)
Retype new UNIX password: (repeat new password for root)
passwd: password updated successfully
```

禁用 root 帐户密码

```shell
sudo passwd -l root
```

更多sudo

```shell
man sudo 
```

添加和删除用户

```shell
sudo adduser username
```

删除用户帐户及其主要组，请使用以下语法：

```
sudo deluser username
```

- 删除帐户不会删除其各自的主文件夹。您是否希望手动删除文件夹或根据所需的保留策略保留它取决于您。

  请记住，如果您没有采取必要的预防措施，任何后来添加的与前一个所有者具有相同 UID/GID 的用户现在都可以访问此文件夹。

  您可能希望将这些 UID/GID 值更改为更合适的值，例如 root 帐户，甚至可能重新定位文件夹以避免将来发生冲突：

  ```
  sudo chown -R root:root /home/username/
  sudo mkdir /home/archived_users/
  sudo mv /home/username /home/archived_users/
  ```

- 要临时锁定或解锁用户密码，请分别使用以下语法：

  ```
  sudo passwd -l username
  sudo passwd -u username
  ```

- 要添加或删除个性化组，请分别使用以下语法：

  ```
  sudo addgroup groupname
  sudo delgroup groupname
  ```

- 要将用户添加到组，请使用以下语法：

  ```
  sudo adduser username groupname
  ```

用户配置文件安全

创建新用户时， adduser 实用程序会创建一个名为 的全新主目录`/home/username`。默认配置文件以 目录中的内容为模型`/etc/skel`，其中包括所有配置文件基础。

如果您的服务器将有多个用户使用，则应密切注意用户主目录的权限以确保机密性。默认情况下，Ubuntu 中的用户主目录是使用全局读取/执行权限创建的。这意味着所有用户都可以浏览和访问其他用户主目录的内容。这可能不适合您的环境。

- 要验证您当前的用户主目录权限，请使用以下语法：

  ```
  ls -ld /home/username
  ```

  以下输出显示该目录`/home/username`具有全球可读的权限：

  ```
  drwxr-xr-x  2 username username    4096 2007-10-02 20:03 username
  ```

- 您可以使用以下语法删除世界可读权限：

  ```
  sudo chmod 0750 /home/username
  ```

  > **笔记**
  >
  > 有些人倾向于不加选择地使用递归选项 (-R) 来修改所有子文件夹和文件，但这不是必需的，并且可能会产生其他不良结果。仅父目录就足以防止未经授权访问父目录下的任何内容。

  一个更有效的方法是在创建用户主文件夹时修改 adduser 全局默认权限。只需编辑文件`/etc/adduser.conf`并将`DIR_MODE`变量修改为适当的内容，以便所有新的主目录都将获得正确的权限。

  ```
  DIR_MODE=0750
  ```

- 使用前面提到的任何技术更正目录权限后，使用以下语法验证结果：

  ```
  ls -ld /home/username
  ```

  下面的结果表明，世界可读的权限已被删除：

  ```
  drwxr-x---   2 username username    4096 2007-10-02 20:03 username
  ```

密码策略

最小长度密码

默认情况下，Ubuntu 需要最少 6 个字符的密码长度，以及一些基本的熵检查。这些值在文件 中控制，`/etc/pam.d/common-password`概述如下。

```
password        [success=1 default=ignore]      pam_unix.so obscure sha512
```

如果要将最小长度调整为 8 个字符，请将适当的变量更改为 min=8。修改概述如下。

```
password        [success=1 default=ignore]      pam_unix.so obscure sha512 minlen=8
```

> **笔记**
>
> 基本密码熵检查和最小长度规则不适用于使用 sudo 级别命令设置新用户的管理员。

密码过期

创建用户帐户时，您应该制定一项策略，设置最短和最长密码期限，迫使用户在密码到期时更改密码。

- 要轻松查看用户帐户的当前状态，请使用以下语法：

  ```
  sudo chage -l username
  ```

  下面的输出显示了有关用户帐户的有趣事实，即没有应用任何策略：

  ```
  Last password change                                    : Jan 20, 2015
  Password expires                                        : never
  Password inactive                                       : never
  Account expires                                         : never
  Minimum number of days between password change          : 0
  Maximum number of days between password change          : 99999
  Number of days of warning before password expires       : 7
  ```

- 要设置这些值中的任何一个，只需使用以下语法，并按照交互式提示进行操作：

  ```
  sudo chage username
  ```

  下面也是一个示例，说明如何手动将显式到期日期 (-E) 更改为 01/31/2015、最短密码期限 (-m) 为 5 天、最长密码期限 (-M) 为 90 天、不活动密码到期后 30 天的时间段 (-I)，以及密码到期前 14 天的警告时间段 (-W)：

  ```
  sudo chage -E 01/31/2015 -m 5 -M 90 -I 30 -W 14 username
  ```

- 要验证更改，请使用与前面提到的相同的语法：

  ```
  sudo chage -l username
  ```

  下面的输出显示了为账户建立的新策略：

  ```
  Last password change                                    : Jan 20, 2015
  Password expires                                        : Apr 19, 2015
  Password inactive                                       : May 19, 2015
  Account expires                                         : Jan 31, 2015
  Minimum number of days between password change          : 5
  Maximum number of days between password change          : 90
  Number of days of warning before password expires       : 14
  ```

对于被禁用的用户ssh的访问

如果用户之前已经设置了 SSH 公钥身份验证，则简单地禁用/锁定用户密码不会阻止用户远程登录您的服务器。他们仍然能够获得对服务器的 shell 访问，而无需任何密码。请记住检查用户主目录中是否有允许此类经过身份验证的 SSH 访问的文件，例如`/home/username/.ssh/authorized_keys`.

删除或重命名`.ssh/`用户主文件夹中的目录，以防止进一步的 SSH 身份验证功能。

请务必检查禁用用户建立的任何 SSH 连接，因为他们可能已经存在入站或出站连接。杀死任何找到的人。

```
who | grep username  (to get the pts/# terminal)
sudo pkill -f pts/#
```

将 SSH 访问限制为只有应该拥有它的用户帐户。例如，您可以创建一个名为“sshlogin”的组，并将组名添加为与`AllowGroups`文件中的变量关联的值`/etc/ssh/sshd_config`。

```
AllowGroups sshlogin
```

然后将您允许的 SSH 用户添加到“sshlogin”组中，并重新启动 SSH 服务。

```
sudo adduser username sshlogin
sudo systemctl restart sshd.service
```

查看登录日志

```shell
tail -f /var/log/auth.log

Sep 23 07:32:58 lhp-server sshd[5801]: User test from 10.4.7.1 not allowed because none of user's groups are listed in AllowGroups
Sep 23 07:33:01 lhp-server sshd[5801]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=10.4.7.1  user=test
Sep 23 07:33:03 lhp-server sshd[5801]: Failed password for invalid user test from 10.4.7.1 port 1047 ssh2
Sep 23 07:33:07 lhp-server sshd[5801]: error: Received disconnect from 10.4.7.1 port 1047:0:  [preauth]
Sep 23 07:33:07 lhp-server sshd[5801]: Disconnected from invalid user test 10.4.7.1 port 1047 [preauth]
```

**apparmor**

AppArmor 是默认安装和加载的。它使用应用程序的*配置文件*来确定应用程序需要哪些文件和权限。一些包会安装它们自己的配置文件，额外的配置文件可以在 apparmor-profiles 包中找到。

从终端提示安装 apparmor-profiles 包：

```
sudo apt install apparmor-profiles
```

**AppArmor 配置文件有两种执行模式**

Enforcement – 在这种模式下，配置文件里列出的限制条件都会得到执行，并且对于违反这些限制条件的程序会进行日志记录。

Complain – 在这种模式下，配置文件里的限制条件不会得到执行，Apparmor只是对程序的行为进行记录。例如程序可以写一个在配置文件里注明只读的文件，但Apparmor不会对程序的行为进行限制，只是进行记录。

那既然complain不能限制程序，为什么还需要这种模式呢，因为——如果某个程序的行为不符合其配置文件的限制，可以将其行为记录到系统日志，并且可以根据程序的行为，将日志转换成配置文件。

当然我们可以随时对配置文件进行修改，选择自己需要的模式。

可选的 apparmor-utils 包包含可用于更改 AppArmor 执行模式、查找配置文件状态、创建新配置文件等的命令行实用程序。

- apparmor_status 用于查看 AppArmor 配置文件的当前状态。

  ```
  sudo apparmor_status
  ```

- aa-complain 将配置文件置于complain模式。

  ```
  sudo aa-complain /path/to/bin
  ```

- aa-enforce 将配置文件置于enforcement模式。

  ```
  sudo aa-enforce /path/to/bin
  ```

- 该`/etc/apparmor.d`目录是 AppArmor 配置文件所在的位置。它可用于操纵所有配置文件的*模式*。

  输入以下内容将所有配置文件置于complain模式：

  ```
  sudo aa-complain /etc/apparmor.d/*
  ```

  将所有配置文件置于enforcement模式：

  ```
  sudo aa-enforce /etc/apparmor.d/*
  ```



- apparmor_parser 用于将配置文件加载到内核中。它还可用于在修改当前加载的配置文件后使用*-r*选项重新加载它以使更改生效。
  要重新加载配置文件：

  ```
  sudo apparmor_parser -r /etc/apparmor.d/profile.name
  ```

- `systemctl`可用于*重新加载*所有配置文件：

  ```
  sudo systemctl reload apparmor.service
  ```

- 该`/etc/apparmor.d/disable`目录可以与 apparmor_parser -R 选项一起使用以*禁用*配置文件。

  ```
  sudo ln -s /etc/apparmor.d/profile.name /etc/apparmor.d/disable/
  sudo apparmor_parser -R /etc/apparmor.d/profile.name
  ```

  要*重新启用*禁用的配置文件，请删除`/etc/apparmor.d/disable/`. 然后使用*-a*选项加载配置文件。

  ```
  sudo rm /etc/apparmor.d/disable/profile.name
  cat /etc/apparmor.d/profile.name | sudo apparmor_parser -a
  ```

- 可以禁用 AppArmor，并通过输入以下内容卸载内核模块：

  ```
  sudo systemctl stop apparmor.service
  sudo systemctl disable apparmor.service
  ```

- 要重新启用 AppArmor，请输入：

  ```
  sudo systemctl enable apparmor.service
  sudo systemctl start apparmor.service
  ```

> **笔记**
>
> 将*profile.name*替换为您要操作的配置文件的名称。另外，替换`/path/to/bin/`为实际的可执行文件路径。例如对于 ping 命令使用`/bin/ping`

简介

AppArmor 配置文件是位于`/etc/apparmor.d/`. 这些文件以它们分析的可执行文件的完整路径命名，将“/”替换为“.”。例如`/etc/apparmor.d/bin.ping`，该`/bin/ping`命令的 AppArmor 配置文件。

配置文件中使用了两种主要类型的规则：

- *路径条目：*详细说明应用程序可以访问文件系统中的哪些文件。
- *能力条目：*确定允许受限进程使用哪些权限。

举个例子，看看`/etc/apparmor.d/bin.ping`：

```
#include <tunables/global>
/bin/ping flags=(complain) {
  #include <abstractions/base>
  #include <abstractions/consoles>
  #include <abstractions/nameservice>

  capability net_raw,
  capability setuid,
  network inet raw,
  
  /bin/ping mixr,
  /etc/modules.conf r,
}
```

- *#include <tunables/global>：*包含来自其他文件的语句。这允许将与多个应用程序有关的语句放在一个公共文件中。
- */bin/ping flags=(complain):*分析程序的路径，还将模式设置为*抱怨*。
- *能力 net_raw，：*允许应用程序访问 CAP_NET_RAW Posix.1e 能力。
- */bin/ping mixr,：*允许应用程序对文件进行读取和执行访问。

> **笔记**
>
> 编辑配置文件后，必须重新加载配置文件。有关详细信息，请参阅上面的[使用 AppArmor](https://ubuntu.com/server/docs/security-apparmor#loadrules)。

### 创建配置文件

- 设计一个测试计划：尝试考虑应该如何运行应用程序。测试计划应该分成小的测试用例。每个测试用例都应该有一个简短的描述并列出要遵循的步骤。

  一些标准测试用例是：

  - 启动程序。
  - 停止程序。
  - 重新加载程序。
  - 测试 init 脚本支持的所有命令。

- 生成新配置文件：使用 aa-genprof 生成新配置文件。

  ```
  sudo aa-genprof executable
  ```

  例如：

  ```
  sudo aa-genprof slapd
  ```

- 要将您的新配置文件包含在 apparmor-profiles 包中，请在Launchpad 中针对[AppArmor](https://bugs.launchpad.net/ubuntu/+source/apparmor/+filebug)包提交错误：

  - 包括您的测试计划和测试用例。
  - 将您的新配置文件附加到错误。

### 更新配置文件

当程序出现异常时，审核消息会发送到日志文件。程序 aa-logprof 可用于扫描 AppArmor 审核消息的日志文件，查看它们并更新配置文件。从终端：

```
sudo aa-logprof
```

### 其他预先存在的配置文件

为 AppArmor 安全策略打包`apport-profiles`并提供`apparmor-profiles-extra`一些实验性配置文件。
不要指望这些配置文件开箱即用，但是当您尝试通过从现有的基础开始创建新配置文件时，它们可以为您提供一个良好的开端。

默认情况下，这些配置文件被认为不够成熟，无法以强制模式发送。因此，它们以抱怨模式发送，以便用户可以测试它们，选择所需的，并在需要时帮助上游改进它们。

包装中携带的一些更多的实验配置文件被放置在` /usr/share/doc/apparmor-profiles/extras/`

### 检查和调试拒绝

如果您遇到拒绝，您将在“dmesg”和任何收集内核消息的日志中看到。
立即值得知道的是，这将涵盖任何被拒绝的访问`because it was not allowed`，但`explicit denies`根本不会在您的日志中放置任何消息。

示例可能如下所示：

```
[1521056.552037] audit: type=1400 audit(1571868402.378:24425): apparmor="DENIED" operation="open" profile="/usr/sbin/cups-browsed" name="/var/lib/libvirt/dnsmasq/" pid=1128 comm="cups-browsed" requested_mask="r" denied_mask="r" fsuid=0 ouid=0
[1482106.651527] audit: type=1400 audit(1571829452.330:24323): apparmor="DENIED" operation="sendmsg" profile="snap.lxd.lxc" pid=24115 comm="lxc" laddr=10.7.0.69 lport=48796 faddr=10.7.0.231 fport=445 family="inet" sock_type="stream" protocol=6 requested_mask="send" denied_mask="send"
```

这遵循以时间戳、审计标记和类别开头的通用结构`apparmor="DENIED"`。
从以下字段中，您可以得出正在发生的事情以及失败的原因。

在上面的例子中，这将是

第一个例子：

- 操作：（`open`程序试图打开一个文件）
- 个人资料：（`/usr/sbin/cups-browsed`你会发现`/etc/apparmor.d/usr.bin.cups-browsed`）
- 名称：（`/var/lib/libvirt/dnsmasq`它想访问什么）
- pid/comm：触发访问的程序
- requested_mask/denied_mask/fsuid/ouid：该公开调用的参数

第二个例子：

- 操作：（`sendmsg`程序尝试通过网络发送）
- 简介：（`snap.lxd.lxc`快照很特别，你会发现`/var/lib/snapd/apparmor/profiles/snap.lxd.lxc`）
- pid/comm：触发访问的程序
- laddr/lport/faddr/fport/family/sock_type/protocol：sendmsg 调用的参数

这样，如果您考虑调试或调整配置文件，您就可以知道在哪个配置文件中以及在什么操作中必须开始。

### 个人资料定制

配置文件旨在提供安全性，因此不能太开放。但通常一个非常特殊的设置可以与配置文件一起使用，如果它只*允许这个额外的访问*。可以通过三种方式来处理。

- 修改配置文件本身
  - 总是有效，但有一个缺点，即配置文件在 /etc 中并被视为 conffiles。因此，在对相关软件包更新进行修改后，您可能会收到一个 conffile 提示。最坏的情况取决于配置自动更新甚至可能覆盖它并且您的自定义规则消失了。
- 使用可调参数
  - 那些提供了可在模板中使用的变量，例如，如果您希望将自定义目录视为主目录，您可以修改`/etc/apparmor.d/tunables/home`它定义用于主目录的基本路径规则
  - 通过设计，这些变量只会影响使用它们的配置文件
- 修改本地覆盖
  - 为了减轻上述方法的缺点，*本地包括*引入了添加编写将要使用的任意规则的能力，并且不会在修改打包规则的升级中出现问题。
  - 这些文件可以在`/etc/apparmor.d/local/`已知有时需要为特殊设置稍作调整的软件包中找到并存在

### 六、防火墙

**uwf**

Ubuntu 的默认防火墙配置工具是 ufw。为简化 iptables 防火墙配置而开发，ufw 提供了一种用户友好的方式来创建基于 IPv4 或 IPv6 的基于主机的防火墙。

#### **启用**

```shell
sudo ufw enable
```

#### 添加端口（本例中为 SSH）

```shell
sudo ufw allow 22
```

#### 使用编号格式添加

```shell
sudo ufw insert 1 allow 80
```

#### 关闭要打开的端口

```shell
sudo ufw deny 22
```

#### 要删除规则，请使用 delete 后跟规则

```
sudo ufw delete deny 22
```

还可以允许从特定主机或网络访问某个端口。以下示例允许从主机 192.168.0.2 SSH 访问此主机上的任何 IP 地址：

```
sudo ufw allow proto tcp from 192.168.0.2 to any port 22
```

将 192.168.0.2 替换为 192.168.0.0/24 以允许从整个子网进行 SSH 访问。

- 向*ufw*命令添加*–dry-run*选项将输出结果规则，但不会应用它们。例如，如果打开 HTTP 端口，将应用以下内容：

  ```auto
   sudo ufw --dry-run allow http
  ```

  ```
  *filter
  :ufw-user-input - [0:0]
  :ufw-user-output - [0:0]
  :ufw-user-forward - [0:0]
  :ufw-user-limit - [0:0]
  :ufw-user-limit-accept - [0:0]
  ### RULES ###
  
  ### tuple ### allow tcp 80 0.0.0.0/0 any 0.0.0.0/0
  -A ufw-user-input -p tcp --dport 80 -j ACCEPT
  
  ### END RULES ###
  -A ufw-user-input -j RETURN
  -A ufw-user-output -j RETURN
  -A ufw-user-forward -j RETURN
  -A ufw-user-limit -m limit --limit 3/minute -j LOG --log-prefix "[UFW LIMIT]: "
  -A ufw-user-limit -j REJECT
  -A ufw-user-limit-accept -j ACCEPT
  COMMIT
  Rules updated
  ```

- 可以通过以下方式禁用 ufw：

  ```
  sudo ufw disable
  ```

- 要查看防火墙状态，请输入：

  ```
  sudo ufw status
  ```

- 对于更详细的状态信息，请使用：

  ```
  sudo ufw status verbose
  ```

- 查看编号格式：

  ```
  sudo ufw status numbered
  ```

> **笔记**
>
> 如果要打开或关闭`/etc/services`的端口在 中定义，则可以使用端口名称而不是编号。在上面的示例中，将*22*替换为*ssh*。

更多详细参数见：man ufw手册

**ufw 应用集成**

打开端口的应用程序可以包含一个 ufw 配置文件，其中详细说明了应用程序正常运行所需的端口。配置文件保存在 中`/etc/ufw/applications.d`，如果默认端口已更改，则可以对其进行编辑。

- 要查看哪些应用程序安装了配置文件，请在终端中输入以下内容：

  ```
  sudo ufw app list
  ```

- 类似于允许流量到端口，使用应用程序配置文件是通过输入：

  ```
  sudo ufw allow Samba
  ```

- 扩展语法也可用：

  ```
  ufw allow from 192.168.0.0/24 to any app Samba
  ```

  将*Samba*和*192.168.0.0/24*替换为您正在使用的应用程序配置文件以及您网络的 IP 范围。

  > **笔记**
  >
  > 无需为应用程序指定*协议*，因为该信息在配置文件中有详细说明。另请注意，*应用程序*名称替换了*端口*号。

- 要查看有关为应用程序定义的端口、协议等的详细信息，请输入：

  ```
  sudo ufw app info Samba
  ```

并非所有需要打开网络端口的应用程序都带有 ufw 配置文件，但是如果您对应用程序进行了配置文件并希望将文件包含在包中，请在 Launchpad 中针对该包提交错误。

```
ubuntu-bug nameofpackage
```

#### IP伪装

IP 伪装的目的是允许网络上具有私有、不可路由 IP 地址的机器通过执行伪装的机器访问 Internet。必须对来自您的专用网络的发往 Internet 的流量进行处理，以便回复可路由回发出请求的机器。为此，内核必须修改每个数据包的*源*IP 地址，以便将回复路由回它，而不是发送到发出请求的私有 IP 地址，这在 Internet 上是不可能的。Linux 使用*连接跟踪*(conntrack) 跟踪哪些连接属于哪些机器并相应地重新路由每个返回数据包。因此，离开您的专用网络的流量被“伪装”为源自您的 Ubuntu 网关机器。此过程在 Microsoft 文档中称为 Internet 连接共享。

#### ufw 伪装

可以使用自定义 ufw 规则来实现 IP 伪装。这是可能的，因为 ufw 的当前后端是 iptables-restore，规则文件位于`/etc/ufw/*.rules`. 这些文件是添加不使用 ufw 使用的遗留 iptables 规则的好地方，以及与网关或网桥相关的规则。

规则分为两个不同的文件，应该在ufw命令行规则之前执行的规则，以及在ufw命令行规则之后执行的规则。

- 首先需要在ufw中开启数据包转发。需要调整两个配置文件，将*DEFAULT_FORWARD_POLICY*`/etc/default/ufw`更改为“ACCEPT”：

  ```
  DEFAULT_FORWARD_POLICY="ACCEPT"
  ```

  然后编辑`/etc/ufw/sysctl.conf`并取消注释：

  ```
  net/ipv4/ip_forward=1
  ```

  同样，对于 IPv6 转发取消注释：

  ```
  net/ipv6/conf/default/forwarding=1
  ```

- 现在将规则添加到`/etc/ufw/before.rules`文件中。默认规则只配置*过滤*表，并且启用伪装*nat*表需要配置。将以下内容添加到文件顶部的标题注释之后：

  ```
  # nat Table rules
  *nat
  :POSTROUTING ACCEPT [0:0]
  
  # Forward traffic from eth1 through eth0.
  -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE
  
  # don't delete the 'COMMIT' line or these nat table rules won't be processed
  COMMIT
  ```

  注释不是绝对必要的，但记录您的配置被认为是一种很好的做法。此外，在修改 中的任何*规则*文件时`/etc/ufw`，请确保这些行是每个修改表的最后一行：

  ```
  # don't delete the 'COMMIT' line or these rules won't be processed
  COMMIT
  ```

  对于每个*表，*都需要一个相应的*COMMIT*语句。在这些示例中，只显示了*nat*和*filter*表，但您也可以为*raw*和*mangle*表添加规则。

  > **笔记**
  >
  > 在上面的示例中，将*eth0*、*eth1*和*192.168.0.0/24*替换为适合您网络的接口和 IP 范围。

- 最后，禁用并重新启用 ufw 以应用更改：

  ```
  sudo ufw disable && sudo ufw enable
  ```

现在应该启用 IP 伪装。您还可以将任何其他 FORWARD 规则添加到`/etc/ufw/before.rules`. 建议将这些附加规则添加到*ufw-before-forward*链中。

#### iptables 伪装

iptables 也可用于启用伪装。

- 与 ufw 类似，第一步是通过编辑`/etc/sysctl.conf`并取消注释以下行来启用 IPv4 数据包转发：

  ```
  net.ipv4.ip_forward=1
  ```

  如果您希望启用 IPv6 转发也取消注释：

  ```
  net.ipv6.conf.default.forwarding=1
  ```

- 接下来，执行 sysctl 命令以启用配置文件中的新设置：

  ```
  sudo sysctl -p
  ```

- 现在可以使用单个 iptables 规则完成 IP 伪装，根据您的网络配置，该规则可能略有不同：

  ```
  sudo iptables -t nat -A POSTROUTING -s 192.168.0.0/16 -o ppp0 -j MASQUERADE
  ```

  上述命令假设您的私有地址空间是 192.168.0.0/16，并且您的面向 Internet 的设备是 ppp0。语法分解如下：

  - -t nat – 规则是进入 nat 表
  - -A POSTROUTING – 规则将被附加 (-A) 到 POSTROUTING 链
  - -s 192.168.0.0/16 – 该规则适用于源自指定地址空间的流量
  - -o ppp0 – 该规则适用于计划通过指定网络设备路由的流量
  - -j MASQUERADE - 与此规则匹配的流量将“跳转”（-j）到要操作的 MASQUERADE 目标，如上所述

- 此外，过滤器表中的每个链（默认表，以及大多数或所有数据包过滤发生的地方）都有一个默认*策略*ACCEPT，但如果除了网关设备之外，您还创建了防火墙，您可能已将策略设置为DROP 或 REJECT，在这种情况下，您的伪装流量需要通过 FORWARD 链允许才能使上述规则起作用：

  ```
  sudo iptables -A FORWARD -s 192.168.0.0/16 -o ppp0 -j ACCEPT
  sudo iptables -A FORWARD -d 192.168.0.0/16 -m state \
  --state ESTABLISHED,RELATED -i ppp0 -j ACCEPT
  ```

  上述命令将允许从本地网络到 Internet 的所有连接以及与这些连接相关的所有流量返回到启动它们的机器。

- 如果您希望在重新启动时启用伪装（您可能会这样做），请编辑`/etc/rc.local`并添加上面使用的任何命令。例如添加第一个没有过滤的命令：

  ```
  iptables -t nat -A POSTROUTING -s 192.168.0.0/16 -o ppp0 -j MASQUERADE
  ```

### 七、日志

防火墙日志对于识别攻击、排除防火墙规则故障以及注意网络上的异常活动至关重要。但是，您必须在防火墙中包含日志规则才能生成它们，并且日志规则必须出现在任何适用的终止规则（具有决定数据包命运的目标的规则，例如 ACCEPT、DROP 或 REJECT）之前。

如果您使用的是 ufw，则可以通过在终端中输入以下内容来打开日志记录：

```
sudo ufw logging on
```

要在 ufw 中关闭注销，只需在上述命令中将*on*替换为*off*。

如果使用 iptables 而不是 ufw，请输入：

```
sudo iptables -A INPUT -m state --state NEW -p tcp --dport 80 \
-j LOG --log-prefix "NEW_HTTP_CONN: "
```

然后，来自本地计算机的端口 80 上的请求将在 dmesg 中生成如下所示的日志（将单行拆分为 3 行以适合此文档）：

```
[4304885.870000] NEW_HTTP_CONN: IN=lo OUT= MAC=00:00:00:00:00:00:00:00:00:00:00:00:08:00
SRC=127.0.0.1 DST=127.0.0.1 LEN=60 TOS=0x00 PREC=0x00 TTL=64 ID=58288 DF PROTO=TCP
SPT=53981 DPT=80 WINDOW=32767 RES=0x00 SYN URGP=0
```

上面的日志也会出现在`/var/log/messages`、`/var/log/syslog`、 和 中`/var/log/kern.log`。可以通过`/etc/syslog.conf`适当编辑或安装和配置 ulogd 并使用 ULOG 目标而不是 LOG来修改此行为。ulogd 守护进程是一个用户空间服务器，它专门为防火墙侦听来自内核的日志记录指令，并且可以记录到您喜欢的任何文件，甚至是 PostgreSQL 或 MySQL 数据库。使用日志分析工具（例如 logwatch、fwanalog、fwlogwatch 或 lire）可以简化理解防火墙日志的过程。

### 八、证书

Ubuntu server 20.04.3  给apache2创建自签名证书

**介绍**

**TLS**或“传输层安全”——及其前身**SSL——**是用于将正常流量包装在受保护的加密包装器中的协议。使用这项技术，服务器可以安全地向其客户端发送信息，而不会被外界拦截或读取其消息。

**注意：**自签名证书将加密您的服务器和任何客户端之间的通信。但是，由于它不是由 Web 浏览器和操作系统中包含的任何受信任的证书颁发机构签署的，因此用户无法使用该证书来自动验证您的服务器的身份。因此，您的用户在访问您的网站时会看到安全错误。

由于此限制，自签名证书不适用于为公众服务的生产环境。它们通常用于测试或保护单个用户或一小群用户使用的非关键服务，这些用户可以通过备用通信渠道建立对证书有效性的信任。如需更适合生产的证书解决方案，请查看[Let's Encrypt](https://letsencrypt.org/)，一个免费的证书颁发机构。

先决条件

- 使用非**root 用户**、启用 sudo 的用户访问 Ubuntu 20.04 服务器。我们的[Ubuntu 20.04 初始服务器设置](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04)指南可以向您展示如何创建此帐户。
- 您还需要安装 Apache。

```shell
sudo apt update
```

然后，安装`apache2`软件包：

```bash
sudo apt install apache2
```

最后，如果您`ufw`设置了防火墙，请打开`http`和`https`端口：

```bash
sudo ufw allow "Apache Full"
```

#### 步骤1：启用mod_ssl

在我们可以使用*任何*SSL 证书之前，我们首先必须启用`mod_ssl`Apache 模块，它提供对 SSL 加密的支持。

`mod_ssl`使用以下`a2enmod`命令启用：

```bash
sudo a2enmod ssl
```

重启Apache激活模块：

```bash
sudo systemctl restart apache2
```

该`mod_ssl`模块现已启用并可以使用。

#### 步骤2：创建ssl证书

现在 Apache 已准备好使用加密，我们可以继续生成新的 SSL 证书。该证书将存储有关您站点的一些基本信息，并附带一个允许服务器安全处理加密数据的密钥文件。

我们可以使用以下`openssl`命令创建 SSL 密钥和证书文件：

```shell
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt
```

参数详解：

- `openssl`：这是用于创建和管理 OpenSSL 证书、密钥和其他文件的命令行工具。
- `req -x509`：这指定我们要使用 X.509 证书签名请求 (CSR) 管理。X.509 是 SSL 和 TLS 遵守的用于密钥和证书管理的公钥基础结构标准。
- `-nodes`：这告诉 OpenSSL 跳过使用密码保护我们的证书的选项。当服务器启动时，我们需要 Apache 能够在没有用户干预的情况下读取文件。密码可以防止这种情况发生，因为我们必须在每次重新启动后输入它。
- `-days 365`：此选项设置证书被视为有效的时间长度。我们在这里设置了一年。许多现代浏览器会拒绝任何有效期超过一年的证书。
- `-newkey rsa:2048`：这指定我们要同时生成新证书和新密钥。我们没有在上一步中创建签署证书所需的密钥，因此我们需要将其与证书一起创建。该`rsa:2048`部分告诉它制作一个 2048 位长的 RSA 密钥。
- `-keyout`：这一行告诉 OpenSSL 在哪里放置我们正在创建的生成的私钥文件。
- `-out`：这告诉 OpenSSL 在哪里放置我们正在创建的证书。

**最重要的一行是请求`Common Name`. 您需要输入用于访问服务器的主机名或服务器的公共 IP**。重要的是，此字段与您将放入浏览器地址栏中以访问该站点的任何内容相匹配，因为不匹配会导致更多的安全错误。

完整的提示列表如下所示：

```
Country Name (2 letter code) [XX]:US
State or Province Name (full name) []:Example
Locality Name (eg, city) [Default City]:Example 
Organization Name (eg, company) [Default Company Ltd]:Example Inc
Organizational Unit Name (eg, section) []:Example Dept
Common Name (eg, your name or your server's hostname) []:your_domain_or_ip
Email Address []:webmaster@example.com
```

您创建的两个文件都将放置在`/etc/ssl`.

接下来，我们将更新我们的 Apache 配置以使用新的证书和密钥。

#### 步骤3：配置apache以使用ssl

现在我们有了自签名证书和密钥，我们需要更新我们的 Apache 配置以使用它们。在 Ubuntu 上，您可以将新的 Apache 配置文件（它们必须以 结尾`.conf`）放入`/etc/apache2/sites-available/`，它们将在下次重新加载或重新启动 Apache 进程时加载。

在本教程中，我们将创建一个新的最小配置文件。（如果您已经`<Virtualhost>`设置了Apache并且只需要向其中添加 SSL，您可能需要复制以 开头的配置行`SSL`，并将`VirtualHost`端口从切换`80`到`443`。我们将`80`在下一步中处理端口。 )

在 /etc/apache2/sites-available 目录中打开一个新文件：

```bash
sudo nano /etc/apache2/sites-available/your_domain_or_ip.conf
```

粘贴以下最小的 VirtualHost 配置：

/etc/apache2/sites-available/your_domain_or_ip.conf

```bash
<VirtualHost *:443>
   ServerName your_domain_or_ip
   DocumentRoot /var/www/your_domain_or_ip

   SSLEngine on
   SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
   SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
</VirtualHost>
```

请务必将`ServerName`行更新为您打算寻址服务器的任何方式。这可以是主机名、完整域名或 IP 地址。确保您选择的任何内容与`Common Name`您在制作证书时选择的内容相匹配。

剩下的几行指定一个`DocumentRoot`目录来提供文件，以及将 Apache 指向我们新创建的证书和密钥所需的 SSL 选项。

现在让我们创建我们`DocumentRoot`的 HTML 文件并将其放入其中，仅用于测试目的：

```bash
sudo mkdir /var/www/your_domain_or_ip
```

`index.html`使用文本编辑器打开一个新文件：

```bash
sudo nano /var/www/your_domain_or_ip/index.html
```

将以下内容粘贴到空白文件中：

/var/www/your_domain_or_ip/index.html

```bash
<h1>it worked!</h1>
```

当然，这不是一个完整的 HTML 文件，但浏览器很宽松，足以验证我们的配置。

保存并关闭文件
接下来，我们需要使用`a2ensite`工具启用配置文件：

```bash
sudo a2ensite your_domain_or_ip.conf
```

接下来，让我们测试一下配置错误：

```bash
sudo apache2ctl configtest
```

如果一切顺利，您将得到如下所示的结果：

```
OutputAH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this messageSyntax OK
```

第一行是一条消息，告诉您该`ServerName`指令未全局设置。如果你想摆脱那条消息，你可以`ServerName`在`/etc/apache2/apache2.conf`. 这是可选的，因为该消息不会造成伤害。

如果您的输出包含`Syntax OK`在其中，则您的配置文件没有语法错误。我们可以安全地重新加载 Apache 以实现我们的更改：

```bash
sudo systemctl reload apache2
```

现在在浏览器中加载您的站点，确保`https://`在开始时使用。

您应该会看到一个错误。这对于自签名证书来说是正常的！浏览器警告您它无法验证服务器的身份，因为我们的证书没有由任何已知的证书颁发机构签署。对于测试目的和个人使用，这可以很好。您应该能够点击进入高级信息或更多信息，然后选择继续。

执行此操作后，您的浏览器将加载该`it worked!`消息。

**注意：**如果您的浏览器根本没有连接到服务器，请确保您的连接没有被防火墙阻止。如果您使用的是`ufw`，以下命令将打开端口`80`和`443`：` sudo ufw allow "Apache Full" ` 复制

接下来，我们将`VirtualHost`在我们的配置中添加另一个部分，以提供纯 HTTP 请求并将它们重定向到 HTTPS。

#### 步骤4：将http重定向到https

目前，我们的配置只会响应端口上的 HTTPS 请求`443`。`80`即使您想强制加密所有流量，也最好在 port 上做出响应。让我们设置一个`VirtualHost`来响应这些未加密的请求并将它们重定向到 HTTPS。

打开我们在前面的步骤中启动的同一个 Apache 配置文件：

```bash
sudo nano /etc/apache2/sites-available/your_domain_or_ip.conf
```

在底部，创建另一个`VirtualHost`块以匹配端口上的请求`80`。使用该`ServerName`指令再次匹配您的域名或 IP 地址。然后，用于`Redirect`匹配任何请求并将它们发送到 SSL `VirtualHost`。确保包含尾部斜杠：

/etc/apache2/sites-available/your_domain_or_ip.conf

```bash
<VirtualHost *:80>
    ServerName your_domain_or_ip
    Redirect / https://your_domain_or_ip/
</VirtualHost>
```

完成后保存并关闭此文件，然后再次测试您的配置语法，并重新加载 Apache：

```bash
sudo apachectl configtest
sudo systemctl reload apache2
```

您可以通过`http://`在地址前面使用纯文本访问您的站点来测试新的重定向功能。您应该会被`https://`自动重定向到。

结果：

未重定向前访问http://10.4.7.142

![未重定向前](/postimages/未重定向前.webp)

访问：https://10.4.7.142

将请求到http上的流量重定向到https上后访问：http://10.4.7.142 和 https://10.4.7.142

![重定向后](/postimages/重定向后.webp)

谷歌浏览器f12 security 选项可以查看自建的证书详细信息。

![证书详细信息](/postimages/证书详细信息.webp)

#### 控制台安全

禁用ctrl+alt+delete 

任何对键盘有物理访问权限的人都可以简单地使用Ctrl+Alt+Delete组合键来重新启动服务器，而无需登录。虽然有人可以简单地拔掉电源，但您仍应防止在生产服务器上使用此组合键。这迫使攻击者采取更严厉的措施重启服务器，同时防止意外重启。

要禁用通过按Ctrl+Alt+Delete组合键执行的重启操作，请运行以下两个命令：

```
sudo systemctl mask ctrl-alt-del.target
sudo systemctl daemon-reload
```

https://multipass.run/?_ga=2.172781731.1854281748.1632272954-1233097571.1631927770

#### multipass 

Ubuntu虚拟机

[Multipass](https://multipass.run/?_ga=2.172781731.1854281748.1632272954-1233097571.1631927770)是在 Ubuntu 上创建 Ubuntu VM 的推荐方法。它专为希望通过单个命令获得全新 Ubuntu 环境并可在 Linux、Windows 和 macOS 上运行的开发人员而设计。

在 Linux 上，它可以快速使用：

```
sudo snap install multipass --beta --classic
```

用法：

**查找镜像**

```shell
$ multipass find
Image                   Aliases           Version          Description
core                    core16            20190424         Ubuntu Core 16
core18                                    20190213         Ubuntu Core 18
16.04                   xenial            20190628         Ubuntu 16.04 LTS
18.04                   bionic,lts        20190627.1       Ubuntu 18.04 LTS
18.10                   cosmic            20190628         Ubuntu 18.10
19.04                   disco             20190628         Ubuntu 19.04
daily:19.10             devel,eoan        20190623         Ubuntu 19.10
```

**在当前Ubuntu系统中创建新实例**

```shell
$ multipass launch ubuntu
Launching dancing-chipmunk...
Downloading Ubuntu 18.04 LTS..........
Launched: dancing chipmunk
```

**检查正在运行中的实例**

```shell
$ multipass info dancing-chipmunk
Name:           dancing-chipmunk
State:          RUNNING
IPv4:           10.125.174.247
Release:        Ubuntu 18.04.1 LTS
Image hash:     19e9853d8267 (Ubuntu 18.04 LTS)
Load:           0.97 0.30 0.10
Disk usage:     1.1G out of 4.7G
Memory usage:   85.1M out of 985.4M
```

**显示刚才创建实例的详细信息**

```shell
$ multipass info dancing-chipmunkName:           dancing-chipmunkState:          RUNNINGIPv4:           10.125.174.247Release:        Ubuntu 18.04.1 LTSImage hash:     19e9853d8267 (Ubuntu 18.04 LTS)Load:           0.97 0.30 0.10Disk usage:     1.1G out of 4.7GMemory usage:   85.1M out of 985.4M
```

**进入到刚刚创建实例的系统中**

```shell
$ multipass shell dancing-chipmunk
Welcome to Ubuntu 18.04.1 LTS (GNU/Linux 4.15.0-42-generic x86_64)

...
```

**在刚刚创建的虚拟机外部查看创建的虚拟机的相关信息**

```shell
$ multipass exec dancing-chipmunk -- lsb_release -a
No LSB modules are available.
Distributor ID:  Ubuntu
Description:     Ubuntu 18.04.1 LTS
Release:         18.04
Codename:        bionic
```

**停止刚才创建的实例**

```shell
multipass stop dancing-chipmunk
```

**删除实例**

```shell
 multipass delete dancing-chipmunk
```

它会显示删除了

```shell
Name                    State             IPv4             Release
snapcraft-asciinema     STOPPED           --               Ubuntu Snapcraft builder for Core 18
dancing-chipmunk        DELETED           --               Not Available
```

**彻底删除**

```shell
multipass purge
```

### 九、容器

LXD

LXD（发音为 lex-dee）是更轻的管理程序，或轻量级容器管理程序。LXC (lex-see) 是一个在本地系统上创建和管理“容器”的程序。它还提供了一个 API 以允许更高级别的管理器（例如 LXD）管理容器。从某种意义上说，可以将 LXC 与 QEMU 进行比较，同时将 LXD 与 libvirt 进行比较。

LXC API 处理“容器”。LXD API 处理“远程”，它提供图像和容器。这通过网络扩展了 LXC 功能，并允许对容器迁移和容器映像发布等任务进行简洁的管理。

LXD 在幕后使用 LXC 来完成一些容器管理任务。但是，它保留自己的容器配置信息并有自己的约定，因此最好不要将经典的 LXC 命令与 LXD 容器一起使用.

#### ubuntu server 预装LXD。其他系统可以使用以下命令安装 lxd 包

```shell
sudo snap install lxd
```

配置

lxd 初始化（必须以root身份运行）

```shell
sudo lxd init
```

详细参数：

LXD Clustering：不需要
New storage pool：需要创建一个存储池
Name of storage pool：给存储池命名
storage backend：存储后端，使用 ZFS
Create a new ZFS pool：需要创建一个 ZFS 池
Use an existing block device：Yes
Path to block device：使用已有的磁盘分区用于 ZFS 的存储后端
MAAS server?：不知道是啥，不需要
New local network bridge?：需要，我只需要使用 LXD 默认的网桥即可
New bridge be called：给网桥命名
IPv4：默认 auto
IPv6：默认 auto
LXD available over the network？：默认 no
Stale cached？：默认 yes
YAML printed？：打印信息，yes/no 都行

如果不能用root用户，普通用户如lhp 需要加入lxd用户组。

```shell
adduser lhp lxd 
```

查看lxd配置信息

```shell
sudo lxc info
```

查看默认容器配置

```shell
sudo lxc profile show default
```

查看容器列表

```shell
sudo lxc list
```

#### 创建容器模板

- 配置清华镜像源

https://mirrors.tuna.tsinghua.edu.cn/help/lxc-images/

**LXD/LXC 2.0及以上版本使用镜像加速的方法**:

创建一个remote链接，指向镜像站即可，或替换掉默认的images链接。

```
# lxc remote add tuna-images https://mirrors.tuna.tsinghua.edu.cn/lxc-images/ --protocol=simplestreams --public
# lxc image list tuna-images:
```

- 查看镜像列表，寻找合适的镜像的FINGERPRINT，用于下载(注意选择x86_64架构)

```shell
sudo lxc image list tuna-images:
```

```shell
root@lhp-server:~/snap/lxd/common/config# lxc image list tuna-images:ubuntu | grep x86_64 
| ubuntu/16.04 (7 more)               | 4f34b9f8a490 | yes    | Ubuntu xenial amd64 (20210924_07:42)    | x86_64       | VIRTUAL-MACHINE | 204.25MB  | Sep 24, 2021 at 12:00am (UTC) |
| ubuntu/16.04 (7 more)               | 10c1476cf890 | yes    | Ubuntu xenial amd64 (20210924_07:42)    | x86_64       | CONTAINER       | 84.59MB   | Sep 24, 2021 at 12:00am (UTC) |
| ubuntu/16.04/cloud (3 more)         | 31a69e3218ef | yes    | Ubuntu xenial amd64 (20210924_07:42)    | x86_64       | CONTAINER       | 103.50MB  | Sep 24, 2021 at 12:00am (UTC) |
| ubuntu/16.04/cloud (3 more)         | b3637c07e450 | yes    | Ubuntu xenial amd64 (20210924_07:42)    | x86_64       | VIRTUAL-MACHINE | 233.88MB  | Sep 24, 2021 at 12:00am (UTC) |
| ubuntu/18.04 (7 more)               | 89e249e9c85d | yes    | Ubuntu bionic amd64 (20210924_07:42)    | x86_64       | CONTAINER       | 100.45MB  | Sep 24, 2021 at 12:00am (UTC) |
```



- FINGERPRINT是镜像的指纹，在上条命令下查找，ContainerTemplateName为容器模板名称，自己定义

```shell
sudo lxc launch tuna-images:<FINGERPRINT> <ContainerTemplateName>
```

- 举例，创建一个名为ubuntu的容器。在lxc清华源中10c1476cf890是Ubuntu16.04的fingerprint

```shell
sudo lxc launch tuna-images:10c1476cf890 ubuntu
```

- 进行容器列表查看

```
sudo lxc list
+--------+---------+----------------------+-----------------------------------------------+-----------+-----------+
|  NAME  |  STATE  |         IPV4         |                     IPV6                      |   TYPE    | SNAPSHOTS |
+--------+---------+----------------------+-----------------------------------------------+-----------+-----------+
| ubuntu | RUNNING | 10.235.54.209 (eth0) | fd42:1082:67cd:27dd:216:3eff:fec0:6ed1 (eth0) | CONTAINER | 0         |
+--------+---------+----------------------+-----------------------------------------------+-----------+-----------+

```

- 可进入容器的 root 用户下 bash

```
sudo lxc exec <ContainerTemplateName> bash
```

- 用户切换

```
sudo susu ubuntu
```

#### 配置共享目录

- 设置共享目录来实现宿主机与容器之间的文件传输
- 设置键值

```shell
sudo lxc config set <ContainerTemplateName> security.privileged true
```

- 设置共享目录，其中shareName为虚拟的设备名称，lxd会虚拟出该设备并导通接通两者共享目录，path1为宿主机下共享目录路径，path2为容器下共享目录路径

```shell
sudo lxc config device add <ContainerTemplateName> <shareName> disk source=<path1> path=<path2>
```

实例：

```shell
oot@lhp-server:~# lxc config set ubuntu security.privileged true
root@lhp-server:~# lxc config device add ubuntu share disk source=/share path=/media
Device share added to ubuntu
```

可以在宿主机/media中创建文件测试，容器/share中文件是否存在。

设置用户名密码：

```shell
sudo lxc exec <ContainerTemplateName> bashpasswd
```

#### 容器克隆

- 克隆容器 参数一为模板容器名称，参数二为目标容器名称

```shell
sudo lxc copy <ContainerTemplateName> <newContainerName>
```

- 运行新容器

```shell
sudo lxc start <newContainerName>
```

- 进入新容器bash

```shell
sudo lxc exec <newContainerName> bash
```

注意，此步骤后需要修改Frp的端口，重新建立内外网穿透，建议修改后使用snapshot快照备份`sudo lxc snapshot <ContainerName>`。

实例：

```shell
root@lhp-server:~# lxc list 
+---------+---------+----------------------+-----------------------------------------------+-----------+-----------+
|  NAME   |  STATE  |         IPV4         |                     IPV6                      |   TYPE    | SNAPSHOTS |
+---------+---------+----------------------+-----------------------------------------------+-----------+-----------+
| ubuntu  | RUNNING | 10.235.54.122 (eth0) | fd42:1082:67cd:27dd:216:3eff:fefa:fea3 (eth0) | CONTAINER | 0         |
+---------+---------+----------------------+-----------------------------------------------+-----------+-----------+
| ubuntu2 | STOPPED |                      |                                               | CONTAINER | 0         |
+---------+---------+----------------------+-----------------------------------------------+-----------+-----------+
```

```shell
root@lhp-server:~# lxc start ubuntu2 
root@lhp-server:~# lxc list 
+---------+---------+----------------------+-----------------------------------------------+-----------+-----------+
|  NAME   |  STATE  |         IPV4         |                     IPV6                      |   TYPE    | SNAPSHOTS |
+---------+---------+----------------------+-----------------------------------------------+-----------+-----------+
| ubuntu  | RUNNING | 10.235.54.122 (eth0) | fd42:1082:67cd:27dd:216:3eff:fefa:fea3 (eth0) | CONTAINER | 0         |
+---------+---------+----------------------+-----------------------------------------------+-----------+-----------+
| ubuntu2 | RUNNING | 10.235.54.50 (eth0)  |                                               | CONTAINER | 0         |
+---------+---------+----------------------+-----------------------------------------------+-----------+-----------+
```

#### 可视化管理

- 下载源码

```shell
git clone https://github.com/AdaptiveScale/lxdui.git
```

- 安装前环境检查

```shell
https://github.com/AdaptiveScale/lxdui/wiki/Installing-the-Prerequisites
```

```shell
git status
which python 
python --version 
pip --version
apt search python3-virtualenv
apt install pip #安装pip
apt install python3-virtualenv #安装python虚拟环境

root@lhp-server:~# pip list | grep  virtualenv
virtualenv             20.0.17 
```

- 创建虚拟环境并检查环境

```shell
cd /lxdui
python3 -m venv test
source test/bin/activate

(test) root@lhp-server:~/lxdui# which pip 
/root/lxdui/test/bin/pip
(test) root@lhp-server:~/lxdui# which python 
/root/lxdui/test/bin/python

(test) root@lhp-server:~/lxdui# pip list 
Package       Version
------------- -------
pip           20.0.2 
pkg-resources 0.0.0  
setuptools    44.0.0 
```

- 安装LXDUI面板并再次检查环境

```shell
python3 setup.py install
(test) root@lhp-server:~/lxdui# pip list 
Package             Version  
------------------- ---------
certifi             2021.5.30
cffi                1.15.0rc1
chardet             3.0.4    
click               6.7      
cryptography        3.4.8    
Flask               1.0      
Flask-JWT           0.3.2    
Flask-Login         0.4.1    
idna                2.7      
itsdangerous        2.0.1    
Jinja2              3.0.1    
jsonschema          2.6.0    
LXDUI               2.1.2    
MarkupSafe          2.0.1    
netaddr             0.7.19   
pbr                 5.6.0    
pip                 20.0.2   
pkg-resources       0.0.0    
psutil              5.6.6    
ptyprocess          0.7.0    
pycparser           2.20     
PyJWT               1.4.2    
pylxd               2.2.7    
pyOpenSSL           17.5.0   
python-dateutil     2.8.2    
requests            2.20.0   
requests-toolbelt   0.9.1    
requests-unixsocket 0.2.0    
setuptools          44.0.0   
six                 1.16.0   
terminado           0.8.1    
tornado             5.0.2    
tornado-xstatic     0.2      
urllib3             1.24.3   
Werkzeug            2.0.1    
ws4py               0.5.1    
XStatic             1.0.1    
XStatic-term.js     0.0.7.0 
```

- 启动面板

```shell
python3 run.py start
```

- 登录面板[http://127.0.0.1:15151](http://127.0.0.1:15151/)，默认账户密码均为admin

![lxdui](/postimages/lxdui.webp)

参考：https://blog.csdn.net/CrazyBusby/article/details/114886340 

https://www.youtube.com/watch?v=Fzb6jN6GYL8&ab_channel=JustmeandOpensource

#### 查看容器日志

要查看有关 LXD 本身的调试信息，请在基于 systemd 的主机上使用

```auto
journalctl -u lxd
```

可以使用以下方式查看容器 c1 的容器日志文件：

```auto
lxc info c1 --show-log
```

#### 创建快照

可以使用以下`lxc move`命令重命名和实时迁移容器：

```auto
lxc move c1 final-beta
```

它们也可以被快照：

```auto
lxc snapshot c1 YYYY-MM-DD
```

然后可以通过恢复快照来恢复对 c1 的后续更改：

```auto
lxc restore u1 YYYY-MM-DD
```

也可以通过复制容器或快照来创建新容器：

```auto
lxc copy u1/YYYY-MM-DD testcontainer
```

限制：

LXD 支持灵活限制容器可以消耗的资源。限制分为以下几类：

- CPU：以多种方式限制容器可用的 CPU。
- 磁盘：配置负载下I/O请求的优先级
- RAM：配置内存和交换可用性
- 网络：配置负载下的网络优先级
- 进程数：限制容器中的并发进程数。

#### lxd 容器端口暴露，使得外面能够访问容器内业务。

前提条件：

lxd容器:Ubuntu

宿主机:Ubuntu

lxd容器内安装apache2 ,并将访问端口更改为8080,重启apache2

```shell
apt install apache2
vi /etc/apache2/ports.conf 
Listen 8080
systemctl restart apache2
```

将宿主机的80端口和容器内8080做映射。外部通过访问宿主机的ip+80来访问容器内的业务。

![lxc-expose](/postimages/lxc-expose.webp)

```shell
lxc config device add ubuntu myport80 proxy listen=tcp:0.0.0.0:80 connect=tcp:127.0.0.1:8080
```

删除映射

```shell
lxc config device remove ubuntu myport80
```

### 十、数据库

#### PostgreSQL

PostgreSQL 是一个对象关系数据库系统，它具有传统商业数据库系统的特性，并在下一代 DBMS 系统中进行了增强。

#### 安装

```shell
sudo apt install postgresql
```

#### 配置

要使其他计算机能够连接到您的 PostgreSQL 服务器，请编辑文件 `/etc/postgresql/12/main/postgresql.conf`

找到*#listen_addresses = 'localhost' 行*并将其更改为：

```shell
listen_addresses = '*'
```

**笔记**

要同时允许 IPv4 和 IPv6 连接，请将 'localhost' 替换为 '::'

现在我们可以连接到我们的 PostgreSQL 服务器，下一步是为*postgres*用户设置密码。在终端提示符下运行以下命令以连接到默认的 PostgreSQL 模板数据库：

```
sudo -u postgres psql template1
```

上面的命令以用户*postgres*连接到 PostgreSQL 数据库*template1*。连接到 PostgreSQL 服务器后，您将看到 SQL 提示符。您可以在 psql 提示符下运行以下 SQL 命令来配置用户*postgres*的密码。

```
ALTER USER postgres with encrypted password 'your_password';
```

配置密码后，编辑文件`/etc/postgresql/12/main/pg_hba.conf`以对*postgres*用户使用*MD5*身份验证：

```
local   all         postgres                          md5
```

最后，您应该重新启动 PostgreSQL 服务以初始化新配置。从终端提示输入以下内容以重新启动 PostgreSQL：

```
sudo systemctl restart postgresql.service
```

> **警告**
>
> 以上配置无论如何都不完整。更多参数请参考《[PostgreSQL 管理员指南》](http://www.postgresql.org/docs/current/static/admin.html)。

您可以使用 PostgreSQL 客户端测试来自其他机器的服务器连接。

```
sudo apt install postgresql-client
psql -h postgres.example.com -U postgres -W 
```

> **笔记**
>
> 将域名替换为您的实际服务器域名。

参考：https://ubuntu.com/server/docs/databases-postgresql

### 十一、监控

集群监控软件：

- Prometheus
- Prometheus Alertmanager
- Grafana
- Telegraf

所需端口：

| Prometheus   | monitor:9090  |
| ------------ | ------------- |
| Alertmanager | monitor:9093  |
| Grafana      | monitor:3000  |
| Telegraf     | workload:9273 |

nagios 安装

```sh
https://www.osradar.com/how-to-install-nagios-on-ubuntu-20-04/#:~:text=How%20To%20Install%20Nagios%20on%20Ubuntu%2020.04%201,pre-checks%20are%20okay.%20...%209%20Access%20Web%20Dashboard
```

![nagios](/postimages/nagios.webp)

### 十二、备份

备份软件：

方案1：bacula 

方案2：NFS+shell script + crontab 

场景：nfs server ：10.4.7.141  共享目录： /mnt/backup

​           nfs client : 10.4.7.142 新建目录/opt/example 需要与 10.4.7.141的/mnt/backup 目录做绑定。

server：

```shell
sudo apt install nfs-kernel-server
sudo systemctl start nfs-kernel-server.service
```

配置：

```shell
vim /etc/exports
/mnt/backup *(ro,sync,subtree_check)
```

如果/mnt/backup 不存在先创建该目录。

```shell
mkdir -p /mnt/backup
```

配置生效

```shell
sudo exportfs -a
```

client:

安装nfs客户端

```shell
sudo apt install nfs-common
```

创建目录

```shell
sudo mkdir /opt/example
sudo mount 10.4.7.141:/mnt/backup /opt/example
```

最后需要配置fstab

```shell
vim /etc/fstab
10.4.7.141:/mnt/backup /opt/example nfs rsize=8192,wsize=8192,timeo=14,intr
```

备份脚本编写：

场景：定时将client中 /home /var/spool/mail /etc /opt 中文件备份到server 的/mnt/backup 中。每天凌晨12：00 备份一次。

backup.sh

```shell
#!/bin/bash
####################################
#
# Backup to NFS mount script.
#
####################################

# What to backup. 
backup_files="/home /var/spool/mail /etc /opt"

# Where to backup to.
dest="/mnt/backup"

# Create archive filename.
day=$(date +%A)
hostname=$(hostname -s)
archive_file="$hostname-$day.tgz"

# Print start status message.
echo "Backing up $backup_files to $dest/$archive_file"
date
echo

# Backup the files using tar.
tar czf $dest/$archive_file $backup_files

# Print end status message.
echo
echo "Backup finished"
date

# Long listing of files in $dest to check file sizes.
ls -lh $dest
```

crontab 

```shell
0 0 * * * bash  /usr/local/bin/backup.sh
```

从备份中恢复

- 查看存档内容的列表。从终端提示输入：

  ```
  tar -tzvf /mnt/backup/host-Monday.tgz
  ```

- 要将文件从存档恢复到不同的目录，请输入：

  ```
  tar -xzvf /mnt/backup/host-Monday.tgz -C /tmp etc/hosts
  ```

  tar的*-C*选项将提取的文件重定向到指定的目录。上面的例子将`/etc/hosts`文件解压到`/tmp/etc/hosts`. tar 重新创建它包含的目录结构。

  另外，请注意前导*“/”*不在要恢复的文件路径之外。

- 要恢复存档中的所有文件，请输入以下内容：

  ```
  cd /sudo tar -xzvf /mnt/backup/host-Monday.tgz
  ```

> **笔记**
>
> 这将覆盖当前文件系统上的文件。

### 十三、邮件服务

dovecot 

postfix 

### 十四、web服务

状态码：

- **1xx** : *Informational* - 收到请求，继续处理
- **2xx** : *成功*- 动作被成功接收、理解和接受
- **3xx** : *重定向*- 必须采取进一步行动才能完成请求
- **4xx** : *客户端错误*- 请求包含错误的语法或无法完成
- **5xx**： *服务器错误*- 服务器未能满足明显有效的请求

- **静态 Web 服务器**：服务器响应的内容将是托管文件“原样”。
- **动态 Web 服务器**：由一个 Web 服务器和一个额外的软件组成，通常是一个应用服务器和一个数据库。例如，要生成您在 Web 浏览器中看到的 Web 页面，应用程序服务器可能会用数据库中的内容填充 HTML 模板。因此，我们说服务器响应的内容是动态生成的。

#### apache2

Apache 是 Linux 系统上最常用的 Web 服务器。Web 服务器用于为客户端计算机请求的网页提供服务。客户端通常使用 Firefox、Opera、Chromium 或 Internet Explorer 等 Web 浏览器应用程序请求和查看网页.

用于传输网页的最常用协议是超文本传输协议 (HTTP)。还支持诸如基于安全套接字层的超文本传输协议 (HTTPS) 和文件传输协议 (FTP)（用于上传和下载文件的协议）等协议。

Apache Web 服务器通常与 MySQL 数据库引擎、超文本预处理器 (PHP) 脚本语言和其他流行的脚本语言（如 Python 和 Perl）结合使用。这种配置被称为 LAMP（Linux、Apache、MySQL 和 Perl/Python/PHP），它为基于 Web 的应用程序的开发和部署形成了一个强大而健壮的平台。

#### 安装

在终端提示符下输入以下命令：

```
sudo apt install apache2
```

#### 配置

Apache2 是通过在纯文本配置文件中放置*指令*来配置的。这些*指令*在以下文件和目录之间分开：

- *apache2.conf：* Apache2 的主要配置文件。包含对 Apache2*全局的*设置。
- *httpd.conf：*历史上主要的 Apache2 配置文件，以 httpd 守护进程命名。在其他发行版（或旧版本的 Ubuntu）中，该文件可能存在。在 Ubuntu 中，所有配置选项已移至*apache2.conf*及以下引用目录，该文件不再存在。
- *conf-available：*该目录包含可用的配置文件。以前在的所有文件`/etc/apache2/conf.d`都应移动到`/etc/apache2/conf-available`.
- *conf-enabled*：*持有*符号链接*到文件`/etc/apache2/conf-available`。当配置文件被符号链接时，它将在下次重新启动 apache2 时启用。
- *envvars：*设置Apache2*环境*变量的文件。
- *mods-available：*此目录包含加载*模块*和配置它们的配置文件。然而，并非所有模块都有特定的配置文件。
- *mods-enabled*：*持有*符号链接*到文件`/etc/apache2/mods-available`。当模块配置文件被符号链接时，它将在下次重新启动 apache2 时启用。
- *ports.conf：*包含确定 Apache2 正在侦听的 TCP 端口的指令。
- *sites-available:*：*此目录包含 Apache2*虚拟主机的*配置文件。虚拟主机允许为具有单独配置的多个站点配置 Apache2。
- *sites-enabled*：*与*mods-enabled一样，`sites-enabled`包含指向`/etc/apache2/sites-available`目录的符号链接。类似地，当sites-available 中的配置文件被符号链接时，一旦Apache2 重新启动，由它配置的站点将处于活动状态。
- *magic：*根据文件的前几个字节确定 MIME 类型的说明。

新建站点或虚拟主机

```shell
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/mynewsite.conf
```

**最终要访问的域名或IP和/etc/apache2/sites-available/中.conf文件同名**

更改默认监听端口在：

```shell
/etc/apache2/ports.conf
```

您可能还希望您的站点响应`www.mynewsite.com`，因为许多用户会认为 www 前缀是合适的。为此使用*ServerAlias*指令。您还可以在 ServerAlias 指令中使用通配符。

例如，以下配置将使您的站点响应任何以*.mynewsite.com*结尾的域请求。

```
ServerAlias *.mynewsite.com
```

默认站点位置：

```shell
/var/www/html
```

示例：将80端口重定向到443端口

/etc/apache2/sites-available/10.4.7.142.conf

```shell
<VirtualHost *:443>
   ServerName 10.4.7.142
   DocumentRoot /var/www/10.4.7.142

   SSLEngine on
   SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
   SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
</VirtualHost>
<VirtualHost *:80>
    ServerName 10.4.7.142
    Redirect / https://10.4.7.142/
</VirtualHost>
```

站点主页：

/var/www/10.4.7.142/index.html

```shell
<h1>it worked!</h1>
```

自签名证书需要自行创建：

```shell
/etc/ssl/certs/apache-selfsigned.crt 和/etc/ssl/private/apache-selfsigned.key
```

开启应用配置：

```shell
sudo a2ensite l0.4.7.142
sudo systemctl restart apache2.service
```

禁用站点配置：

```shell
sudo a2dissite 10.4.7.142
sudo systemctl restart apache2.service
```

启动ssl模块

```shell
sudo a2enmod ssl
```

您可以通过`https://your_hostname/url/`在浏览器地址栏中键入来访问安全服务器页面。

### 十五、密码管理

#### root密码忘记重置

重启系统 按F1进入。

重启Ubuntu 在启动界面选择 （高级）

-》

选择 recovery mode

-》

首先重建GRUB引导，否则改密码可能会出现 **Authentication token manipulation error** 错误

-》

完成后回车，继续选择root

接下来输入，按照提示修改密码就可以

```shell
passwd root
```

修改完成后，重启服务器

```shell
reboot
```

### 十六、ubuntu20.04 进入命令行模式

启动时禁用GUI

```shell
sudo systemctl set-default multi-user
```

重新启动或退出当前会话以退出GUI:

```shell
gnome-session-quit
```

**恢复图形模式**

```shell
sudo systemctl set-default graphical
```


