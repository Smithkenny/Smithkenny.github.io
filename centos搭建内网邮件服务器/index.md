# centos搭建内网邮件服务




### 安装环境

- **centos7.9** 
- **postfix2.10.1**
- **dovecot2.2.36**
- **foxmail(windows版)**

### 功能概括

使用foxmail软件登录邮件服务器（192.168.1.89）能够成功发送和接收邮件。

### 具体实现配置步骤

搭建邮件服务器

配置服务器主机名称，服务器主机名称与发信域名要一致。

```sh
[root@mail ~]# vi /etc/hostname
mail.lee.com
[root@mail ~]# hostname 
mail.lee.com
```

配置postfix服务

```sh
yum install postfix
```

如果系统自带postfix，建议先卸载旧程序，然后再安装新程序。

```sh
rpm -e postfix
```

编辑postfix 程序主配置文件`/etc/postfix/main.cf`

```sh
    75	myhostname = mail.lee.com
    83	mydomain = lee.com
    99 myorigin = $mydomain
   113	inet_interfaces = all
   119	inet_protocols = ipv4
   164	mydestination = $myhostname, $mydomain
   264	mynetworks = 192.168.1.0/24, 127.0.0.0/8
```

启动postfix并加入开机自启

```sh
systemctl restart postfix
systemctl enable postfix
```

配置dovecot服务程序

```sh
yum install dovecot
```

配置dovecot主配置文件`/etc/dovecot/dovecot.conf`

vi /etc/dovecot/dovecot.conf

```sh
     24 protocols = pop3 lmtp imap
     48 login_trusted_networks = 192.168.1.0/24
```

配置邮件格式与存储路径

```sh
vi /etc/dovecot/conf.d/10-mail.conf
mail_location = mbox:~/mail:INBOX=/var/mail/%u
```

重启dovecot服务并开机启动

```sh
 systemctl restart dovecot
 systemctl enable dovecot
```

创建电子邮件系统的登录账户（本地系统的账户和密码，因此在本地系统创建常规账户即可）

```sh
1.发送邮件用户
useradd test
passwd test
2.接收文件用户
useradd recv
passwd test
```

**关键！！！！**分别进入test 用户和 recv用户，默认家目录在/home/test 和/home/recv下。创建用于保存邮件的目录

```sh
[test@mail ~]$ mkdir -p mail/.imap/INBOX
[recv@mail ~]$ mkdir -p mail/.imap/INBOX
```

最后重启postfix和dovacot服务

```sh
systemctl restart postfix
systemctl restart dovacot
```

邮件发送和接收测试

windows 上安装foxmail。

```sh
1、发送用户配置
邮件类型：pop3
账号：test@lee.com
收件服务器： 192.168.1.89		端口110 ssl不勾选
发送服务器： 192.168.1.89		端口25  ssl不勾选
2.接收用户配置
邮件类型：pop3
账号：recv@lee.com
收件服务器： 192.168.1.89		端口110 ssl不勾选
发送服务器： 192.168.1.89		端口25  ssl不勾选
```

最后使用test 用户发送邮件，用recv用户接收邮件。




