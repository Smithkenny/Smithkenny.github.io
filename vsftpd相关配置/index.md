# vsftpd相关配置



### ftp基本配置

系统：`centos7`

安装`vsftpd 、ftp客户端`

```shell
yum install vsftpd -y 
yum install ftp -y
[root@localhost vsftpd]# rpm -qa | grep ftp
vsftpd-sysvinit-3.0.2-29.el7_9.x86_64
vsftpd-3.0.2-29.el7_9.x86_64
ftp-0.17-67.el7.x86_64
```

防火墙设置

```shell
firewall-cmd --list-services //查看防火墙允许的服务。

firewall-cmd --add-service=ftp --permanent //永久开放ftp服务

firewall-cmd --add-port=20/tcp --permanent

firewall-cmd --add-port=21/tcp --permanent //允许外网访问

firewall-cmd --reload //重新载入配置

setsebool ftpd_full_access 1 //selinux设置

setsebool tftp_home_dir 1
```

创建用户，登录

```shell
useradd test

passwd test

ftp localhost #ftp本地测试
[root@localhost vsftpd]# ftp localhost
Trying ::1...
ftp: connect to address ::1Connection refused
Trying 127.0.0.1...
Connected to localhost (127.0.0.1).
220 Welcome to blah FTP service.
Name (localhost:root): test
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 
```

局域网测试：

虚拟机`IP:10.4.7.140 ftp：21`被动模式

接下来在`windows平台用FileZilla Client`软件登陆.

![filezila-1](/postimages/filezila-1.webp)

![filezila-2](/postimages/filezila-2.webp)

连接成功：

![filezila-success](/postimages/filezila-success.webp)

系统账号默认登陆是在账号的家目录，可以切换到其它目录。

到这里，基本的`vsftpd`搭建成功。

开机自启`vsftpd`

```
systemctl enable vsftpd
```

### vsftpd 被动模式普通用户登录配置

主程序：`/usr/sbin/vsftpd`

主配置文件：`/etc/vsftpd/vsftpd.conf`

数据根目录：`/var/ftp`

就算是经验丰富的老手也会做好备份工作

`cp /etc/vsftpd/vsftpd.conf /etc/vsftpd/vsftpd.conf.bak`

```shell
anonymous_enable=NO //设定不允许匿名访问

local_enable=YES //设定本地用户可以访问

write_enable=YES //设定可以进行写操作

local_umask=022 //设定上传后文件的权限掩码

anon_upload_enable=NO //禁止匿名用户上传

anon_mkdir_write_enable=NO //禁止匿名用户建立目录

dirmessage_enable=YES //设定开启目录标语功能

xferlog_enable=YES //设定开启日志记录功能

connect_from_port_20=YES //设定端口20进行数据连接(主动模式)

chown_uploads=NO //设定禁止上传文件更改宿主

#chown_username=whoever

xferlog_file=/var/log/xferlog //设定Vsftpd的服务日志保存路径。

xferlog_std_format=YES //设定日志使用标准的记录格式。

#idle_session_timeout=600 //设定空闲连接超时时间，单位为秒，这里默认

#data_connection_timeout=120 //设定空闲连接超时时间，单位为秒，这里默认。

#nopriv_user=ftptest

async_abor_enable=YES //设定支持异步传输功能。

ascii_upload_enable=YES

ascii_download_enable=YES //设定支持ASCII模式的上传和下载功能。

ftpd_banner=Welcome to blah FTP service. //设定Vsftpd的登陆标语。

#deny_email_enable=YES // (default follows)

#banned_email_file=/etc/vsftpd/banned_emails

chroot_local_user=YES

chroot_list_enable=YES //禁止用户登出自己的FTP主目录。

chroot_list_file=/etc/vsftpd/chroot_list //这个文件里的用户不受限制，不限制在本目录。

ls_recurse_enable=NO //禁止用户登陆FTP后使用"ls -R"的命令。

该命令会对服务器性能造成巨大开销。

#listen=NO

#listen_ipv6=YES

userlist_enable=YES //设定userlist_file中的用户将不得使用FTP。

tcp_wrappers=YES //设定支持TCP Wrappers

allow_writeable_chroot=YES //这个可以解决chroot权限问题

pasv_enable=YES

pasv_min_port=40000

pasv_max_port=40080

pasv_promiscuous=YES

```

`systemctl restart vsftpd` //重启`vsftpd服务`

![filezila-3](/postimages/filezila-3.webp)

如果登录失败提示500，需要在服务器上创建`chroot_lis`t把`test`加进列表

```shell
echo test >> /etc/vsftpd/chroot_list
```

![chroot_limit](/postimages/chroot_limit.webp)

创建另一个用户`aaa`。目前`test` 和`aaa`都可以登录`ftp`

由于`test`加入到了`chroot_list`列表中，可以随意切换目录。而`aaa`用户不在了列表中，只能在`aaa用户`家目录中活动，不能自由切换目录。

![test-1](/postimages/test-1.webp)

![aaa-1](/postimages/aaa-1.webp)

————————————————

解释：`chroot_local_user=YES`将所有用户限定在主目录内，`chroot_list_enable=YES`表示要启用`chroot_list_file`, 因为`chroot_local_user=YES`，即全体用户都被“限定在主目录内”,所以总是作为“例外列表”的`chroot_list_file`这时列出的是那些`不会被限制在主目录下`的用户。


### vsftpd 匿名用户登录配置

 建立虚拟FTP用户数据库文件

建立一个虚拟用户名单文件，这个文件就是来记录`vsftpd`虚拟用户的用户名和口令的数据文件，我这里给它命名为`vuser.list`，保存在`/etc/vsftpd/`目录下。一行账号，一行密码

```shell
$ vim vuser.list

vuser
123456
```

安装`Berkeley DB` #有db_load 命令可以忽略。

```shell
yum install libdb-utils-5.3.21-25.el7.x86_64
```

安装后在`/etc/vsftpd/`目录下执行。

```shell
db_load -T -t hash -f vuser.list vuser.db//生成用户加密文件
chmod 600 vuser.db //敏感文件限制只允许属主读写
```

创建虚拟用户及虚拟用户的家目录

```shell
useradd -d /var/vusers -s /sbin/nologin vftp //创建系统用户vftp，并制定其家目录为/var/vusers

chmod -Rf 755 /var/vusers/ //修改目录的权限使得其他用户也可以访问。
```

建立支持虚拟用户的PAM认证文件

`vsftpd`的`pam文件`在`/etc/pam.d/目录`下，先做备份工作。

```shell
cp vsftpd vsftpd.bak
vim vsftpd
重要：先注释掉所有的内容后添加以下内容：
auth required /lib64/security/pam_userdb.so db=/etc/vsftpd/vuser //此句用于检查用户密码，数据库文件不要写后缀.db
account required /lib64/security/pam_userdb.so db=/etc/vsftpd/vuser //此句用于检查用户是否在有效期内，数据库支持虚拟用户的PAM认证文件。
```

在`vsftpd.conf`中添加支持配置

```shell
guest_enable=YES //开启虚拟用户模式

guest_username=vftp //指定虚拟用户账号

pam_service_name=vsftpd //指定pam文件

user_config_dir=/etc/vsftpd/vusers_profile //指定虚拟用户的权限配置目录。

virtual_use_local_privs=NO //虚拟用户和匿名用户有相同的权限
```

为虚拟用户设置不同的权限。

`mkdir -p  /etc/vsftpd/vusers_profile`/新建虚拟用户目录

`vim /etc/vsftpd/vusers_profile/vuser`/新建虚拟用户配置文件，文件名要和上面的虚拟用户名单里的账号名字对等。

`mkdir -p /var/vusers/vuser`/创建虚拟用户家目录

```shell
local_root=/var/vusers/vuser //虚拟账号的家目录

write_enable=YES

anonymous_enable=NO

local_umask=022

anon_upload_enable=YES //上传权限

anon_mkdir_write_enable=YES //创建文件和目录的权限

anon_other_write_enable=YES //删除文件和目录的权限

anon_world_readable_only=YES //当文件的“其他人”有读权限的时候可以下载

download_enable=YES //下载权限
```

保存配置，重启服务。

`systemctl restart vsftpd`

登录查看：

![filezila-niming](/postimages/filezila-niming.webp)

![filezila-niming-1](/postimages/filezila-niming-1.webp)

此处`/`指的是`vuser用户家目录`而不是根目录。

本实验中匿名用户登录后有查看下载权限，没有创建文件权限。并且限制了只能查看匿名用户的家目录，不能切换到其他目录。

`vsftpd`传输模式

主动模式：服务端主动连客户端

![ftp-主动](/postimages/ftp-主动.webp)

在主动模式下，FTP客户端从任意端口5150（端口号>1023）发起一个FTP请求，并携带自己监听的端口号5151（发送的端口号+1=监听端口号）；随后服务器返回确认，然后从服务器本地的20端口主动发起连接请求到客户端的监听端口5151，最后客户端返回确认。

被动模式：客户端主动连服务端

![ftp-被动](/postimages/ftp-被动.webp)

在被动模式中，命令连接和数据连接都由客户端来发起，如上图所示，客户端用随机命令端口5150向服务器的21命令端口发送一个`PASV请求`，然后服务器返回数据端口3267，告诉客户端我在哪个端口监听数据连接。然后客户端向服务器的监听端口3268发起数据连接，最后服务器回复确认。

**vsftpd默认是被动模式。**所以客户端要设置被动模式连接，如果到现在还没连接成功的看客户端有没有设置被动传输模式。

#### FTP工作原理介绍

文件传输协议：`File Transfer Protocol` 早期的三个应用级协议之一，基于`C/S结构`
数据传输格式：二进制（默认）和文本
双通道协议：命令和数据连接

**两种模式**：从服务器角度

- 主动(PORT style)：服务器主动连接
  命令（控制）：客户端：随机port —> 服务器：21/tcp
  数据：客户端：随机port <—服务器：20/tcp
- 被动(PASV style)：客户端主动连接
  命令（控制）：客户端：随机port —> 服务器：21/tcp
  数据：客户端：随机port —> 服务器：随机port /tcp

范例：服务器被动模式数据端口
227 Entering Passive Mode (172,16,0,1,224,59)
服务器数据端口为：224*256+59

**FTP服务状态码：**
`1XX`：信息 125：数据连接打开
`2XX`：成功类状态 200：命令OK 230：登录成功
`3XX`：补充类 331：用户名OK
`4XX`：客户端错误 425：不能打开数据连接
`5XX`：服务器错误 530：不能登录

**用户认证：**

 匿名用户：`ftp,anonymous`,对应`Linux用户ftp`
​ 系统用户：`Linux用户`,用户`/etc/passwd`,密码`/etc/shadow`
​ 虚拟用户：特定服务的专用用户，独立的用户/密码文件

#### vsftpd 软件介绍

由 `vsftpd` 包提供，不再由`xinetd`管理
用户认证配置文件：`/etc/pam.d/vsftpd`

**启动服务相关文件：**

`/usr/lib/systemd/system/vsftpd.service`
`/etc/rc.d/init.d/vsftpd`

**配置文件：**

`/etc/vsftpd/vsftpd.conf`

帮助：`man 5 vsftpd.conf`

配置文件格式：

```
option=value 
```

注意：`=` 前后不要有空格

用户和其共享目录

- 匿名用户（映射为系统用户`ftp` ）共享文件位置：`/var/ftp`
- 系统用户共享文件位置：用户家目录
- 虚拟用户共享文件位置：为其映射的系统用户的家目录

### 提高vsftp的访问速度

vsftp连接时很慢，因为默认开启了reverse_lookup（反向解析）.

在(vsftpd.conf)主文件中添加

```
reverse_lookup_enable=NO
```

重启`vsftpd`服务

```
systemctl restart vsftpd
```

### 需求

1.设立管理员用户拥有所有权限（上传、下载、文件删除）。

2.其他用户只允许下载，不允许上传、文件修改和删除。

3.禁止使用匿名用户登录

|  用户名  |                           权限说明                           |
| :------: | :----------------------------------------------------------: |
|  admin   | 管理员，可以上传、下载、新建文件夹、删除和更改文件和文件夹名。 |
|  upload  | 可以上传、新建文件夹。不可以下载，不能删除文件和文件夹，不能重命名原有文件和文件夹。 |
| download |                 只能下载，不能进行其他操作。                 |

#### 实验环境

`centos7.9 x86_64`

以上三个虚拟用户均不允许登录系统，并且使用ftp时会被锁定在指定目录内不可进入系统其他目录。

#### 安装配置`vsftpd`

```
yum -y install vsftpd
cd /etc/vsftpd/
cp vsftpd.conf vsftpd.conf.bak #先备份
```

#### 添加一个不能登录系统用户，用来做虚拟用户映射。

```
useradd www2013 -s /sbin/nologin -d /home/www2013
passwd www2013
```

#### 创建虚拟用户列表，分别是`upload、download和admin`

```
touch /etc/vsftpd/vu_list.txt

cat >>vu_list<<eof
upload
111111
download
111111
admin
111111
eof
```

查看列表内容

```
#cat /etc/vsftpd/vu_list.txt
upload
111111
download
111111
admin
111111
```

#### 创建`db`文件

保存虚拟帐号和密码的文本文件无法被系统帐号直接调用，需要创建用于系统认证的`db`文件

```
db_load -T -t hash -f /etc/vsftpd/vu_list.txt /etc/vsftpd/vu_list.db
```

```
-T 用于轻松地将文本文件导入数据库。
-t 表示指定底层访问方法。
hash 是我们指定的底层访问方法。
-f 表示从指定文件中读取。
指定的文件是vu_list.txt
我们正在创建或添加的数据库是vu_list.db
```

注意：创建db文件需要db4支持，如果系统没安装请安装

```
yum -y install db4 db4-devel db4-utils
```

#### 修改db文件的权限，以免被非法用户修改

```
chmod 600 /etc/vsftpd/vu_list.db
```

#### 并删除`vu_list.txt `文件

```
rm vu_list.txt
```

添加新用户时，只需使用`vi`新建一个`vu_list.txt `文件，然后重新运行`db_load`命令，即可将新用户添加到数据库中。

#### 配置`PAM`文件

由于服务器通过调用系统PAM模块来对客户端进行身份验证，因此需要修改指定的配置文件来调整认证方式。PAM模块的配置文件路径为：`/etc/pam.d/`,这个目录下存放只许多与用户认证有关的配置文件。

备份原文件

```
cp /etc/pam.d/vsftpd /etc/pam.d/vsftpd.old
```

编辑`vsftpd文件`，除了`#%PAM-1.0`其他都注释掉，然后再添加。

```sh
32位系统添加：
auth       required     /lib/security/pam_userdb.so     db=/etc/vsftpd/vu_list
account    required     /lib/security/pam_userdb.so     db=/etc/vsftpd/vu_list
64位系统添加：
auth       required     /lib64/security/pam_userdb.so   db=/etc/vsftpd/vu_list
account    required     /lib64/security/pam_userdb.so   db=/etc/vsftpd/vu_list
```

#### 创建虚拟用户配置文件

```
#创建conf文件夹
cd /etc/vsftpd
mkdir conf
cd conf
#创建admin用户的配置文件
cat >>admin<< EOF
anon_world_readable_only=NO
write_enable=YES
anon_mkdir_write_enable=YES
anon_other_write_enable=YES
anon_upload_enable=YES
EOF
#创建upload用户的配置文件
cat >>upload<< EOF
write_enable=YES
anon_upload_enable=YES
anon_mkdir_write_enable=YES
anon_world_readable_only=NO
download_enable=NO
EOF
#创建download用户的配置文件
cat >>download<< EOF
anon_world_readable_only=NO
EOF
```

#### 修改`vsftpd.conf文件`

`vi vsftpd.conf`

```
#修改内容如下
anonymous_enable=NO
local_enable=YES
anon_mkdir_write_enable=NO
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
chown_uploads=NO
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
allow_writeable_chroot=YES
xferlog_file=/var/log/vsftpd.log
xferlog_std_format=YES
nopriv_user=www2013
async_abor_enable=YES
ascii_upload_enable=YES
ascii_download_enable=YES
ftpd_banner=Welcome to blah FTP service ^_^
ls_recurse_enable=NO
listen=YES
pam_service_name=vsftpd
userlist_enable=YES
tcp_wrappers=YES
local_root=/home/www2013
guest_enable=YES
guest_username=www2013
virtual_use_local_privs=YES
user_config_dir=/etc/vsftpd/conf
reverse_lookup_enable=NO
```

#### 注意

`guest_username`要和之前建立的不能登陆的系统用户匹配。

`chroot_list_enable和chroot_local_user`取值的不同组合情况

|        参数        |                             取值                             |                             取值                             |              取值              |              取值              |
| :----------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------: | :----------------------------: |
| chroot_list_enable |                             YES                              |                             YES                              |               NO               |               NO               |
| chroot_local_user  |                             YES                              |                              NO                              |              YES               |               NO               |
|        意义        | 文件中列出的用户根目录为系统根目录，其它用户根目录为自己主目录。 | 文件中列出的用户根目录为自己主目录，其它用户根目录为系统根目录。 | 全部用户根目录都是自己主目录。 | 全部用户根目录都是系统根目录。 |

##### 禁锢系统用户

**禁锢所有系统用户在家目录中**

` chroot_local_user=YES` 禁锢系统用户，默认NO，即不禁锢。

**禁锢或不禁锢特定的系统用户在家目录中，与上面设置功能相反**

 `chroot_list_enable=YES` #默认是NO
​ `chroot_list_file=/etc/vsftpd/chroot_list` #默认值

- 当`chroot_local_user=YES`和`chroot_list_enable=YES`时，则`chroot_list`中用户不禁锢，即白名单
- 当`chroot_local_user=NO`和`chroot_list_enable=YES`时，则`chroot_list`中用户禁锢，即黑名单

#### 设置ssl，提高安全性。

`vi /etc/vsftpd/vsftpd.conf`添加到文件末尾。

```
rsa_cert_file=/etc/vsftpd/vsftpd.pem
rsa_private_key_file=/etc/vsftpd/vsftpd.key
ssl_enable=YES
allow_anon_ssl=NO
force_local_data_ssl=YES
force_local_logins_ssl=YES
ssl_tlsv1=YES
ssl_sslv2=NO
ssl_sslv3=NO

pasv_min_port=7000
pasv_max_port=7500

```

#### 设置RSA证书

```
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout /etc/vsftpd/vsftpd.key -out /etc/vsftpd/vsftpd.pem
```

```
第一个字段是国家代码字段，填写您所在国家的两个字母代码：

Country Name (2 letter code) [XX]:

接下来是州或省，通过输入全名而不是缩写来填写：

State or Province Name (full name) []:

接下来是地区名称。这是你的城市：

Locality Name (eg, city) [Default City]:

接下来是公司或组织名称。您可以将此留空或根据需要填写：

Organization Name (eg, company) [Default Company Ltd]:

接下来是组织单位名称。如果服务器用于特定部门，您可以填写此内容，或将其留空：

Organizational Unit Name (eg, section) []:

应该填写下一个字段，但是您可以决定如何填写。这是您的服务器的通用名称。示例webftp.domainname.ext：：

Common Name (eg, your name or your server's hostname) []:

最后是电子邮件字段，您当然可以将其留空而不会出现问题：

Email Address []:

完成表格后，将创建证书。
```



最后重启`vsftpd服务`

```
systemctl restart vsftpd
```

#### 使用`Filezilla`连接测试

![vsftpd测试1](/postimages/vsftpd测试1.webp)

#### 使用`lftp`连接测试

![vsftpd测试2](/postimages/vsftpd测试2.webp)

注意：

关闭`lftp`客户端认证。否则会报错。

报错如下:

```
ls: Fatal error: Certificate verification: Not trusted (DB:40:CE:56:E3:56:6C:75:9C:8D:1F:08:CF:9F:22:9D:8D:9B:E8:D3)
```

`vi /etc/lftp.conf`

```
set ssl:verify-certificate no
```

参考资料：

https://docs.rockylinux.org/guides/file_sharing/secure_ftp_server_vsftpd/

