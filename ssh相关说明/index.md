# ssh相关说明


## SSH服务

### SSH服务协议说明

SSH 是 **Secure Shell Protocol** 的简写，由 IETF 网络工作小组（Network Working Group )制定；在进行数据传输之前，SSH先对联机数据包通过加密技术进行加密处理，加密后在进行数据传输。确保了传递的数据安全。

SSH是专为远程登录会话和其他网络服务提供的安全性协议。利用 SSH 协议可以有效的防止远程管理过程中的信息泄露问题，在当前的生产环境运维工作中，绝大多数企业普遍采用SSH协议服务来代替传统的不安全的远程联机服务软件，如telnet(23端口，非加密的)等。

在默认状态下，SSH服务主要提供两个服务功能：

一是提供类似telnet远程联机服务器的服务，即上面提到的SSH服务。

另一个是类似FTP服务的sftp-server,借助SSH协议来传输数据的.提供更安全的SFTP服务(vsftp，proftp)。

### SSH加密技术说明

当前，网络上的数据包加密技术一般是通过所谓的一对公钥与私钥（PublickeyandPivatekey)组合成的密钥对进行加密与解密操作。如下图，A-Server要给B_Client传数据，首先会通过本地的公钥加密后再到发到网络上传输。而加密的数据到达B_Client端后，再经由B_Client本地的私钥将加密的数据解密出来。ssh认证连接的过程如下

![ssh认证连接的过程](/postimages/ssh认证连接的过程.webp)

**ssh实现安全链接建立，利用钥匙和锁头**

1. 钥匙=私钥 锁头=公钥，私钥可以解密公钥。

2. 公钥可以再网络中传输，私钥再本地主机保存。

### ssh加密算法

v1 漏洞密钥不更换。

v2 定期更换密钥。

利用Diffie-Hellman机制定期更新密钥。

### ssh知识要点

 ssh是安全的**加密**协议，用于远程链接linux服务器

 ssh 默认端口是22，安全协议版本sshv2，出来2之外还有1（有漏洞）

 ssh服务端主要包括两个服务功能 ssh远程链接和sftp服务

 linux ssh 客户端包括ssh 远程链接命令，以及远程拷贝scp命令等。

## SSH服务软件详细说明

### 什么是ssh服务

SSH服务端是一个守护讲程 (daemon).它在后台运行并响应来自客户端的连接请求。 SSH服务端的进程名为sshd，负责实时监听远程SSH客户端的远程连接请求，并进行处理，一般包括公共密钥认证、密钥交换、对称密钥加密和非安全连接等。这个SSH服务就是我们前面基础系统优化中保留开机自启动的服务之。

ssh客户端包含ssh以及像scp(远程拷贝） slogin(远程登陆) sftp(安全FTP文件传输）等应用程序。

ssh的工作机制大致是本地的ssh客户端先发送一个连接请求到远程的ssh服务端，服务端检查连接的客户端发送的数据包和IP地址，如果确认合法，就会发送密钥给 SSH的客户端，此时，客户端本地再将密钥发回给服务端，自此连接建立。

### ssh软件安装

**客户端**

```sh
rpm -qf 'which ssh'
```

openssh-clients-5.3p1-122.el6.x86_64

**服务端软件**

```sh
rpm -qf 'which sshd'
```

openssh-server-5.3p1-122.el6.x86_64

### openssh-clinets 软件的主要内容

```sh
rpm -ql openssh-clients
```

```sh
/etc/ssh/ssh_config     ---ssh客户端配置文件

/usr/bin/.ssh.hmac

/usr/bin/scp         ---远程复制命令

/usr/bin/sftp         ---远程文件传输服务

/usr/bin/slogin       ---远程登陆命令

/usr/bin/ssh         ---ssh远程登陆管理主机

/usr/bin/ssh-add

/usr/bin/ssh-agent

/usr/bin/ssh-copy-id     ---ssh服务分发公钥命令

/usr/bin/ssh-keyscan
```

### openssh-server 软件的主要内容

```sh
rpm -ql openssh-server
```

/etc/rc.d/init.d/sshd   #ssh服务启动脚本

/etc/ssh/sshd_config    #ssh服务配置文件

/etc/sysconfig/sshd    #ssh创建密钥有关

/usr/sbin/.sshd.hmac    #ssh加密算法有关文件

/usr/sbin/sshd       #ssh服务进程启动命令

**注意：****要使用sshd采用绝对路径进行启动**

```sh
sshd
```

sshd re-exec requires execution with an absolute path

## ssh服务配置文件说明

1. 配置文件中所有注释信息，表示默认参数配置

2. 配置文件中#空格 后面内容表示说明信息

​       \#参数 表示配置参数信息

3. 配置文件参数信息修改后，一旦变为注释，即还原为默认配置

### ssh服务的配置文件路径

```sh
vim /etc/ssh/sshd_config
```

修改SSH服务的运行参数，是通过修改配置文件**/etc/ssh/sshd_config**实现的。

一般来说SSH服务使用默认的配置已经能够很好的工作了，如果对安全要求不高，仅仅提供SSH服务的情况，可以不需要修改任何配置。

### 配置文件中常用配置说明

```shell
vim /etc/ssh/sshd_config

# $OpenBSD: sshd_config,v 1.80 2008/07/02 02:24:18 djm Exp $

# This is the sshd server system-wide configuration file.  See

# sshd_config(5) for more information.

# This sshd was compiled with PATH=/usr/local/bin:/bin:/usr/bin

# The strategy used for options in the default sshd_config shipped with

# OpenSSH is to specify options with their default value where

# possible, but leave them commented.  Uncommented options change a

# default value.

# OpenSSH is to specify options with their default value where

# possible, but leave them commented.  Uncommented options change a

# default value.
Port 25113                   #端口

ListenAddress 10.0.0.41   #监听地址（本地网卡地址），指定本地网卡那个网卡提供服务

PermitRootLogin no         #是否允许root用户登陆

#PermitEmptyPasswords no  #禁止空密码登陆

#UseDNS no                   #不使用DNS

GSSAPIAuthentication no    #API认证

# 连接慢的解决

#AddressFamily any         #指定监听ipv4地址，或是ipv6地址，或者所有都监听
```

**配置文件内容说明**

井号(**#**)注释的参数信息为默认配置

井号(**#**)后面有空格的为描述信息

井号(**#**)后面没有空格的为参数信息

**另外：**配置文件参数信息修改后，一旦变为注释，即还原为默认配置.

**语法格式有错误** **↓**

```sh
sshd -t /etc/ssh/sshd_config
```

/etc/ssh/sshd_config: line 50: Bad configuration option: uthorizedKeysFile

/etc/ssh/sshd_config: terminating, 1 bad configuration options

ssh服务监听参数说明

![ssh服务监听](/postimages/ssh服务监听.webp)

如图所示，sshd_config配置文件中实际监听本地的网卡，并非网络地址

监听地址只能监听本地网卡上配置的地址，**监听的网卡可以对请求做出相应**，为未监听的网卡不响应请求。

## ssh连接慢解决方法

vi /etc/ssh/sshd_config  修改几个参数。

```shell
UseDNS noGSSAPIAuthentication no
```

## ssh 密钥认证方式

![ssh密钥认证方式](/postimages/ssh密钥认证方式.webp)

## ubuntu server 20.04 自动登录。

效果1：

![效果1](/postimages/效果1.webp)

效果2：

![效果2](/postimages/效果2.webp)

效果1实现：

### **首先指定自动登录用户**

```sh
sudo vim /etc/systemd/system/getty.target.wants/getty@tty1.service
```

![自动登录1](/postimages/自动登录1.webp)

### **再设置免密码登录**

```sh
sudo vim /etc/passwd
```

将 root:x:0:0:root:/root:/bin/bash 中的x去掉，即：root::0:0:root:/root:/bin/bash，如果要指定别的用户可以找到相对应的行，用户名位于第一行。

### **允许ssh远程登录**

```shell
sudo vim /etc/ssh/sshd_config，找到PermitRootLogin without-password 修改为PermitRootLogin yes

systemctl restart sshd
systemctl restart ssh
```

效果2：

**每台机器设置hosts**

```sh
vi /etc/hosts
10.4.7.70 master
10.4.7.71 worker1
10.4.7.72 worker2
```

控制端master 上生成公钥和私钥。三次回车。

```shell
cd ~/.ssh
ssh-keygen -t rsa

ls 
authorized_keys  id_rsa  id_rsa.pub  known_hosts 
```

将公钥传给被控端/root/.ssh内

公钥：id_rsa.pub

私钥：id_rsa

将生成的公钥内容写入到authorized_keys（控制端和被控端端都操作）

```sh
cat id_rsa.pub >> authorized_keys
```

都重启ssh

```shell
systemctl restart sshdsystemctl restart ssh
```

测试：

在控制端连被控端看需不需要密码

```shell
ssh worker1
```




