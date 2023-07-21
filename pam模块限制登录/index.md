# pam模块限制登录


### 系统环境
```
[root@43f873a76b81 ~]# uname -a 
Linux 43f873a76b81 4.18.0-147.5.1.el8_1.x86_64 #1 SMP Wed Feb 5 02:00:39 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
[root@43f873a76b81 ~]# cat /etc/*release*
CentOS Linux release 7.9.2009 (Core)
Derived from Red Hat Enterprise Linux 7.9 (Source)
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"

CentOS Linux release 7.9.2009 (Core)
CentOS Linux release 7.9.2009 (Core)
cpe:/o:centos:centos:7
```

### 检查是否有ssh服务,没有则需要安装openssh-server服务

```
[root@43f873a76b81 ~]# rpm -qa| grep ssh 
openssh-7.4p1-22.el7_9.x86_64
openssh-server-7.4p1-22.el7_9.x86_64
libssh2-1.8.0-4.el7.x86_64
[root@43f873a76b81 ~]# yum install -y openssh-server 
```

### 编辑vi /etc/ssh/sshd_config 文件，禁用root用户直接登录，1分钟无操作自动退出系统。

### 主要配置文件，其他全注释掉。

```
Port 22
LoginGraceTime 1m
PermitRootLogin no
AuthorizedKeysFile	.ssh/authorized_keys
UsePAM yes
X11Forwarding yes
AcceptEnv LANG LC_CTYPE LC_NUMERIC LC_TIME LC_COLLATE LC_MONETARY LC_MESSAGES
AcceptEnv LC_PAPER LC_NAME LC_ADDRESS LC_TELEPHONE LC_MEASUREMENT
AcceptEnv LC_IDENTIFICATION LC_ALL LANGUAGE
AcceptEnv XMODIFIERS
Subsystem	sftp	/usr/libexec/openssh/sftp-server
Protocol 2
```

### 解释：

```
Port 22 #使用22端口连接
LoginGraceTime 1m #登录系统后1分钟内不操作自动退出。设置空闲会话超时时长。
PermitRootLogin no #禁止root用户直接登录
UsePAM yes #使用pam模块
X11Forwarding yes # 开启GUI界面
Subsystem	sftp	/usr/libexec/openssh/sftp-server 
Protocol 2 #使用版本2更加安全。
```

### 添加一个普通用户admin并设置密码admin

```
useradd admin 
passwd admin
```

### 更改pam模块设置，密码输错后等待120s后重试。

### 一定要加在第一行。

vi /etc/pam.d/login

```
#%PAM-1.0
auth required pam_tally2.so onerr=fail deny=3 unlock_time=120 even_deny_root root_unlock_time=120
```

vi /etc/pam.d/sshd

```
#%PAM-1.0
auth required pam_tally2.so onerr=fail deny=3 unlock_time=120 even_deny_root root_unlock_time=120
```

### 最后重启ssh服务

```
systemctl restart sshd
```

### 测试效果

```
ssh admin@172.16.0.3
Account locked due to 27 failed logins
Password: 
Account locked due to 29 failed logins
Password: 
```

### 查看登陆信息

```
[root@43f873a76b81 ~]# pam_tally2 --user 
Login           Failures Latest failure     From
root                3    03/16/22 06:04:08  gateway
admin              36    03/16/22 06:04:17  gateway
```


