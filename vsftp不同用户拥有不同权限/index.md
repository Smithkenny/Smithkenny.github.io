# VSFTP不同用户拥有不同权限


### VSFTP不同用户拥有不同权限

## 安装篇

#### 操作系统环境

```shell
centos 7.9 mini
```

#### 安装vsftp服务

```shell
yum install –y vsftpd ftp –安装
systemctl start vsftpd.service –启动
systemctl enable vsftpd.service –开机自启
vi /etc/selinux/conf
SELINUX=enforce 改成 SELINUX=disabled
getenforce - 查看是否为Disabled
```

## 实现篇

### 1.要实现的功能

```sh
admin_root：/home/download/下的所有文件夹及文件具有上传、下载、删除权限
admin_001：/home/download/admin_001/下的文件具有上传、下载、删除权限

download_root：/home/download/下的所有文件夹及文件具有下载权限
download_001：/home/download/admin_001/下的文件具有下载权限
```

### 2.首先创建一个本地系统用户`abcde`并指定其家目录，并建立相应的子目录

```shell
useradd -d /home/download abcde -s /sbin/nologin
mkdir -p /home/download/admin_001
chown -R abcde: /home/download
chmod 755 /home/download
chmod 755 /home/download/*
```

### 3.创建用户数据文件：（一行账号一行密码，不要有空行，不要有空格）

**所有密码此处示例设为123456。**

```shell
vim /etc/vsftpd/userlist.txt
admin_root
123456
admin_001
123456
download_root
123456
download_001
123456
```

**然后生成数据文件**

```shell
db_load：一般系统都有安装，db4，db4-devel，db4-utils ，如果没有的话可以安装：
yum -y install db4*

db_load -T -t hash -f /etc/vsftpd/userlist.txt /etc/vsftpd/userlist.db
```

#### 4、修改pam配置

```shell
vim /etc/pam.d/vsftpd
```

**将原来的全部注释掉，加入**

```shell
auth sufficient /lib64/security/pam_userdb.so db=/etc/vsftpd/userlist
account sufficient /lib64/security/pam_userdb.so db=/etc/vsftpd/userlist
```

**注意：`db=/etc/vsftpd/userlist` 后面的`.db`必须去掉，默认会自动匹配`*.db`文件，**
**这里只需要给数据文件名，就行了。**
**64位机需要使用`lib64`（这里折腾了好久，有的是`lib`有的是`lib64`）;**

### 5、创建本地系统用户abcde对应的虚拟账户

**虚拟用户需要通过本地系统用户来实现各种操作,因此要放在系统用户的同名文件夹下。**

```shell
mkdir -p /etc/vfstpd/abcde
cd /etc/vfstpd/abcde
```

**每一个虚拟用户都要有一个同名的文件，文件里记载其权限以及可操作的目录；**
**本例如下执行，两个>>和<<之间不要有空格**

**创建有上传下载权限的用户**

```shell
cat >>admin_root<<EOF
anon_world_readable_only=NO
write_enable=YES
anon_mkdir_write_enable=YES
anon_upload_enable=YES
anon_other_write_enable=YES
local_root=/home/download
EOF

cat >>admin_001<<EOF
anon_world_readable_only=NO
write_enable=YES
anon_mkdir_write_enable=YES
anon_upload_enable=YES
anon_other_write_enable=YES
local_root=/home/download/admin_001
EOF
```

**创建只有下载权限的用户**

```shell
cat >>download_root<<EOF
anon_world_readable_only=NO
local_root=/home/download
EOF

cat >>download_001<<EOF
anon_world_readable_only=NO
local_root=/home/download/admin_001
EOF
```

### 6、设置配置文件vsftpd.conf

```shell
vim /etc/vsftpd/vsftpd.conf
```

**修改内容如下：**

```shell
listen=YES
connect_from_port_20=YES
anonymous_enable=NO
local_enable=YES
write_enable=NO
anon_upload_enable=NO
anon_mkdir_write_enable=NO
anon_other_write_enable=NO
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
guest_enable=YES
guest_username=abcde
virtual_use_local_privs=YES
allow_writeable_chroot=YES
pam_service_name=vsftpd
user_config_dir=/etc/vsftpd/abcde
local_umask=022
xferlog_enable=YES
xferlog_file=/var/log/vsftpd.log
pasv_promiscuous=YES
```

**touch  /etc/vsftp/chroot_list**
**里面啥也不写。**

### 7、重新启动vsftpd

```shell
service vsftpd restart
```

### 8.测试

```shell
所有用户只能进入自己家目录不能进入上一级目录；
admin_root对于/home/download下的所有文件夹具有上传、下载、删除权限；
admin_001对于/home/download/下的同名文件夹具有上传、删除权限，无其他权限；
download_root对于/home/download下的所有文件夹具有下载权限，不能上传或删除任何内容，不能对其他任何文件夹操作；
download_001对于/home/download/下的admin_001文件夹分别具有下载权限，不能上传或删除任何内容，不能对其他任何文件夹操作。
```

### 9.日常维护

```shell
（1）更新帐号，先修改userlist.txt，再执行db_load
db_load -T -t hash -f /etc/vsftpd/userlist.txt /etc/vsftpd/userlist.db
（2）更新/etc/vsftpd/abcde/下不同用户对应的权限
（3）重启vsftpd

systemctl start  vsftpd.service
```

### 10.加密连接

**生成证书**

```shell
openssl req -x509 -nodes -days 365 -newkey rsa:1024 \
 -keyout /etc/vsftpd/vsftpd.pem \
 -out /etc/vsftpd/vsftpd.pem
```

**其中"-days 365"声明证书的有效期是一年。**

**接下来的过程需要你输入一些相关的国家，地区，位置，组织名称，common name等信息。**

**回答这些信息以后系统会将生成完的证书vsftpd.pem文件保存在/etc/vsftpd目录下。**

**修改`/etc/vsftpd/vsftpd.conf`文件,增加以下内容**

```shell
ssl_enable=YES
allow_anon_ssl=NO
force_local_data_ssl=NO
force_local_logins_ssl=NO
ssl_tlsv1=YES
ssl_sslv2=NO
ssl_sslv3=NO
rsa_cert_file=/etc/vsftpd/vsftpd.pem
```

**如果设置force_local_logins_ssl=YES，那么会强制用户登录时采用支持TLS/SSL认证的ftp客户端。如果设置为NO的话，则用户可以选择使用ssl加密或者不加密。**

**此时选择不加密登录会有失败提示**

```shell
Non-anonymous sessions must use encryption.
```

**重启服务**

```shell
systemctl restart vsftpd
```

**修改为其他端口**

```shell
listen_port=10021
```

### vsftpd 425 Security: Bad IP connecting解决方法

**问题分析:**
FTP客户端软件连接`vsftpd`服务报此错误，大概原因是在连接中变换了IP地址。

**问题解决**

**服务端添加：**

```shell
vi /etc/vsftpd/vsftpd.conf
pasv_promiscuous=YES
```

**重启服务**

```shell
systemctl restart vsftpd
```

**pasv_promiscuous选项参数说明**
此选项激活时，将关闭PASV模式的安全检查。该检查确保数据连接和控制连接是来自同一个IP地址。小心打开此选项。此选项唯一合理的用法是存在于由安全隧道方案构成的组织中。默认值为NO。
合理的用法是：在一些安全隧道配置环境下，或者更好地支持FXP时(才启用它)。

**FTP模式与数据端口**

FTP 分为两类，PORT FTP和PASV FTP，PORT FTP是一般形式的FTP。这两种FTP在建立控制连接时操作是一样的，都是由客户端首先和FTP服务器的控制端口(默认值为21)建立控制链接，并通过此链接进行传输操作指令。它们的区别在于使用数据传输端口(ftp- data)的方式。PORT FTP由FTP服务器指定数据传输所使用的端口，默认值为20。PASV FTP由FTP客户端决定数据传输的端口。 PASV FTP这种做法，主要是考虑到存在防火墙的环境下，由客户端与服务器进行沟通(客户端向服务器发出数据传输请求中包含了数据传输端口)，决定两者之间的数据传输端口更为方便一些。


