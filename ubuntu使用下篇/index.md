# Ubuntu使用下篇


### 一、证书

Ubuntu server 20.04.3  给apache2创建自签名证书

**介绍**

**TLS**（传输层安全）及其前身**SSL**是用于将正常流量包装在受保护的加密包装器中的协议。使用这项技术，服务器可以安全地向其客户端发送信息，而不会被外界拦截或读取其消息。

注意：自签名证书将加密您的服务器和任何客户端之间的通信。但是，由于它不是由 Web 浏览器和操作系统中包含的任何受信任的证书颁发机构签署的，因此用户无法使用该证书来自动验证您的服务器的身份。因此，您的用户在访问您的网站时会看到安全错误。

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

#### 步骤3：配置apache使用ssl

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

注意：如果您的浏览器根本没有连接到服务器，请确保您的连接没有被防火墙阻止。如果您使用的是`ufw`，以下命令将打开端口`80`和`443`：` sudo ufw allow "Apache Full" ` 复制

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

### 二、容器

#### LXD

LXD（发音为 lex-dee）是更轻的管理程序，或轻量级容器管理程序。LXC (lex-see) 是一个在本地系统上创建和管理“容器”的程序。它还提供了一个 API 以允许更高级别的管理器（例如 LXD）管理容器。从某种意义上说，可以将 LXC 与 QEMU 进行比较，同时将 LXD 与 libvirt 进行比较。

LXC API 处理“容器”。LXD API 处理“远程”，它提供图像和容器。这通过网络扩展了 LXC 功能，并允许对容器迁移和容器映像发布等任务进行简洁的管理。

LXD 在幕后使用 LXC 来完成一些容器管理任务。但是，它保留自己的容器配置信息并有自己的约定，因此最好不要将经典的 LXC 命令与 LXD 容器一起使用.

ubuntu server 预装LXD。其他系统可以使用以下命令安装 lxd 包

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

##### 创建容器模板

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

##### 配置共享目录

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

##### 容器克隆

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

##### 可视化管理

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

##### 查看容器日志

要查看有关 LXD 本身的调试信息，请在基于 systemd 的主机上使用

```auto
journalctl -u lxd
```

可以使用以下方式查看容器 c1 的容器日志文件：

```auto
lxc info c1 --show-log
```

##### 创建快照

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

##### lxd 容器端口暴露，使得外面能够访问容器内业务。

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

### 三、数据库

#### PostgreSQL

PostgreSQL 是一个对象关系数据库系统，它具有传统商业数据库系统的特性，并在下一代 DBMS 系统中进行了增强。

安装

```shell
sudo apt install postgresql
```

配置

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

### 四、监控

#### 集群监控软件

- Prometheus
- Prometheus Alertmanager
- Grafana
- Telegraf

#### 所需端口

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

### 五、备份

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

#### 备份脚本编写

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

#### 从备份中恢复

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

### 六、邮件服务

dovecot 

postfix 

### 七、web服务

#### 状态码

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

##### 安装

在终端提示符下输入以下命令：

```
sudo apt install apache2
```

##### 配置

Apache2 是通过在纯文本配置文件中放置*指令*来配置的。这些*指令*在以下文件和目录之间分开：

- apache2.conf：Apache2 的主要配置文件。包含对 Apache2*全局的*设置。
- httpd.conf：历史上主要的 Apache2 配置文件，以 httpd 守护进程命名。在其他发行版（或旧版本的 Ubuntu）中，该文件可能存在。在 Ubuntu 中，所有配置选项已移至apache2.conf及以下引用目录，该文件不再存在。
- conf-available：该目录包含可用的配置文件。以前在的所有文件`/etc/apache2/conf.d`都应移动到`/etc/apache2/conf-available`.
- conf-enabled：*持有*符号链接*到文件`/etc/apache2/conf-available`。当配置文件被符号链接时，它将在下次重新启动 apache2 时启用。
- envvars：设置Apache2*环境*变量的文件。
- mods-available：此目录包含加载模块和配置它们的配置文件。然而，并非所有模块都有特定的配置文件。
- mods-enabled：持有符号链接到文件`/etc/apache2/mods-available`。当模块配置文件被符号链接时，它将在下次重新启动 apache2 时启用。
- ports.conf：包含确定 Apache2 正在侦听的 TCP 端口的指令。
- sites-available: 此目录包含 Apache2*虚拟主机的*配置文件。虚拟主机允许为具有单独配置的多个站点配置 Apache2。
- sites-enabled：与mods-enabled一样，`sites-enabled`包含指向`/etc/apache2/sites-available`目录的符号链接。类似地，当sites-available 中的配置文件被符号链接时，一旦Apache2 重新启动，由它配置的站点将处于活动状态。
- magic：根据文件的前几个字节确定 MIME 类型的说明。

新建站点或虚拟主机

```shell
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/mynewsite.conf
```

**最终要访问的域名或IP和/etc/apache2/sites-available/中.conf文件同名**

更改默认监听端口在：

```shell
/etc/apache2/ports.conf
```

您可能还希望您的站点响应`www.mynewsite.com`，因为许多用户会认为 www 前缀是合适的。为此使用ServerAlias指令。您还可以在 ServerAlias 指令中使用通配符。

例如，以下配置将使您的站点响应任何以`.mynewsite.com`结尾的域请求。

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

### 八、密码管理

#### root密码忘记重置

重启系统 按F1进入。

重启Ubuntu 在启动界面选择 （高级）-》

选择 recovery mode-》

首先重建GRUB引导，否则改密码可能会出现 **Authentication token manipulation error** 错误-》

完成后回车，继续选择root，接下来按照提示修改密码就可以。

```shell
passwd root
```

修改完成后，重启服务器

```shell
reboot
```

### 九、ubuntu20.04 进入命令行模式

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


