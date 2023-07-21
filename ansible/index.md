# ansible基本环境搭建及升级版本

## 应用场景

`Ansible`是一个基于`Python`开发的配置管理和应用部署工具，现在也在自动化管理领域大放异彩。它融合了众多老牌运维工具的优点，`Pubbet和Saltstack`能实现的功能，`Ansible`基本上都可以实现。

`Ansible`能批量配置、部署、管理一大堆的主机。比如以前需要切换到每个主机上执行的一或多个操作，使用`Ansible`只需在固定的一台`Ansible`控制节点上去完成所有主机的操作。

## ansible主控端搭建

### 基于docker构建一个ansible镜像

#### 编写Dockerfile

```bash
FROM debian:10
COPY sources.list /etc/apt/sources.list
RUN apt update;apt install -y ansible sshpass vim net-tools procps
RUN sed -i 's/^#host_key_checking.*/host_key_checking = False/g' /etc/ansible/ansible.cfg
CMD ["/bin/bash"]
```

说明：基于debain10搭建的基础ansible镜像包。

#### 创建sources.list文件

```bash
cat <<EOF > sources.list 
deb http://mirrors.ustc.edu.cn/debian/ buster main
deb-src http://mirrors.ustc.edu.cn/debian/ buster main

deb http://mirrors.ustc.edu.cn/debian-security buster/updates main
deb-src http://mirrors.ustc.edu.cn/debian-security buster/updates main

# buster-updates, previously known as 'volatile'

deb http://mirrors.ustc.edu.cn/debian/ buster-updates main
deb-src http://mirrors.ustc.edu.cn/debian/ buster-updates main

deb http://mirrors.ustc.edu.cn/debian/ buster-backports main non-free contrib
deb-src http://mirrors.ustc.edu.cn/debian/ buster-backports main non-free contrib
EOF
```

注意：`sources.list`是一个源配置文件，和`Dockerfile`文件在同一级。

```bash
FROM 意思就是使用哪个基础镜像
COPY 就是复制本地的哪个文件到容器镜像
RUN 就是执行一个Linux命令
CMD 就是设置容器启动的时候需要运行什么程序
```

#### 开始构建

语法

```bash
 docker build -t ${镜像名称:版本} .
```

例如

```bash
 docker build -t debian10/ansible .
```

当不设置版本的时候，默认会给予latest的一个版本标签。

注意构建过程中报错信息，根据报错信息更改Dockerfile文件内容。

#### 验证镜像

```bash
[root@localhost ~]# docker images | grep ansible
debain10/ansible       latest      03216dab3d76        23 minutes ago      349MB
```

### 主控端容器创建

```bash
docker run -it --name ansible-control --privilaged=true -d debain10/ansible sh
```

#### 查看容器是否正常启动

```bash
docker ps 
ccb07fbb3515  debain10/ansible  "sh"  25 minutes ago  Up 25 minutes  ansible-control
```

#### 进入容器

```bash
docker exec -it ansible-control bash
```

查看ansible版本

```bash
root@ccb07fbb3515:/etc/ansible# ansible --version 
ansible 2.7.7
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/root/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.7.3 (default, Jan 22 2021, 20:04:44) [GCC 8.3.0]
```

## ansible被控节点搭建

### 使用Dockerfile创建被控节点镜像

Dockerfile文件内容如下：

```bash
FROM debian:10
COPY sources.list /etc/apt/sources.list
RUN apt update;apt install -y vim net-tools procps openssh-server python3.7
RUN sed -i 's/^#PermitRootLogin.*/PermitRootLogin yes/g' /etc/ssh/sshd_config;cp /usr/bin/python3.7 /usr/bin/python
CMD ["/bin/bash"]
```

这里有一个坑，被控节点中必须安装python，ansible默认会去`/usr/bin`内找python执行文件。如果没有就会报错。这里安装镜像时就默认安装了`python3.7`。并将执行文件复制为`/usr/bin/python`。

当然`openssh-server`是必备的，`ansible`就是基于ssh服务控制服务器的。

### 创建sources.list文件(同主控端)

### 开始构建

```bash
 docker build -t debian10/ansible-node .
```

### 创建3个被控端容器

```bash
docker run -it --name ansible-node1 --privilaged=true -d debain10/ansible init
docker run -it --name ansible-node2 --privilaged=true -d debain10/ansible init
docker run -it --name ansible-node3 --privilaged=true -d debain10/ansible init
```

### 分别进入容器并修改root密码

```bash
#密码统一修改为123456
echo "123456" | passwd --stdin root #debain10试了下不生效
#使用以下命令更改
passwd root
123456
```

最后可以看到一个主控端和3个被控端容器。

```bash
e8e005edfab9        debain/ansible-node                    "init"                   About an hour ago   Up About an hour                             ansible-node3
cd03436b2424        debain/ansible-node                    "init"                   About an hour ago   Up About an hour                             ansible-node2
88bf589578f5        debain/ansible-node                    "init"                   About an hour ago   Up About an hour                             ansible-node1
ccb07fbb3515        debain10/ansible                       "sh"                     About an hour ago   Up About an hour                             ansible-control
```

## 在控制端添加所有被控端主机信息

有两种方法

### 一种明文方式(不安全)

在/etc/ansible/hosts文件添加下面的内容（覆盖）

```bash
node1 ansible_ssh_host=172.18.0.5 ansible_ssh_port=22 ansible_ssh_pass=1
node2 ansible_ssh_host=172.18.0.6 ansible_ssh_port=22 ansible_ssh_pass=1
node3 ansible_ssh_host=172.18.0.7 ansible_ssh_port=22 ansible_ssh_pass=1
[hello]
node1
node2
node3
```

### 另一种密文方式（推荐）

#### 使用openssl生成随机密码

```bash
openssl rand -out vault_password_file -base64 128 
#cat vault_password_file
eBqrIFYQ7XDJKjTywZwc26KElu1NQwwx5su3pzj21c4H33RQUV4kslL+UHVEJzxN
sYIEbklwL7la+sugcSoK5PutqZ79bXnvT7FkEk2pMFNUO4W13/R3pH1AFAddAvsC
h+YG1mxTCUsNbCwgtngCkAe9cBcLbkbLMVxjQ+dZym4=
#最小化权限并上锁。
chmod a-w vault_password_file
chattr +i vault_password_file
```

删除文件前先解锁

```bash
chattr -i vault_password_file
```

关于openssl补充：

openssl生成随机数

```bash
openssl rand [-out file] [-rand file(s)] [-base64] [-hex] num
常用选项有：

-out file：将生成的随机数保存至指定文件中
-base64：使用base64 编码格式
-hex：使用16进制编码格式
```



#### 使用`ansible-vault`生成加密后的字符串

```
root@ccb07fbb3515:~# ansible-vault encrypt_string "123456" -name ansible_ssh_pass 
The number of --name options do not match the number of args.
The last named variable will be "ame". The rest will not have names.
# The encrypted version of variable ("ame", the string #1 from the command line args).
ame: !vault |
         $ANSIBLE_VAULT;1.1;AES256
         35643865643061373962376261653765333233333839353937643134643437396262646338363965
		 3366666561653365663164386536363963353961313362350a626662366361303434383665306531
		 34386161363965363634626663343832363664623139336665313131623732343437633439386264
		 6534663962663337640a363166383137613835383966396431326165663732646337653235646239
		 3534
# The encrypted version of the string #2 from the command line args.)
!vault |
         $ANSIBLE_VAULT;1.1;AES256
         34306239353764383165316133636366633131366332326362323830613638633263353638656562
	     3736396133633063623863623739326132383062373533300a613036646534353033363134383532          66333366633537643737333065313866396137663965393530653262616132633236663561336164
	     3633393361616162340a343931653762613765366339356632366135326464336532313562643432
	     38386533653231626365303837653831346263646464633430643765396239366334
Encryption successful
```

最后修改主机文件`/etc/ansible/hosts`配置(使用yaml风格)

```yaml
---
all: 
  hosts:
    node1:
      ansible_host: 172.18.0.5
      ansible_ssh_port: 22
      ansible_ssh_pass: !vault |
         $ANSIBLE_VAULT;1.1;AES256
         35643865643061373962376261653765333233333839353937643134643437396262646338363965
         3366666561653365663164386536363963353961313362350a626662366361303434383665306531
         34386161363965363634626663343832363664623139336665313131623732343437633439386264
         6534663962663337640a363166383137613835383966396431326165663732646337653235646239
         3534
    node2:
      ansible_host: 172.18.0.6
      ansible_ssh_port: 22
      ansible_ssh_pass: !vault |
         $ANSIBLE_VAULT;1.1;AES256
         35643865643061373962376261653765333233333839353937643134643437396262646338363965
         3366666561653365663164386536363963353961313362350a626662366361303434383665306531
         34386161363965363634626663343832363664623139336665313131623732343437633439386264
         6534663962663337640a363166383137613835383966396431326165663732646337653235646239
         3534
    node3:
      ansible_host: 172.18.0.7
      ansible_ssh_port: 22
      ansible_ssh_pass: !vault |
         $ANSIBLE_VAULT;1.1;AES256
         35643865643061373962376261653765333233333839353937643134643437396262646338363965
         3366666561653365663164386536363963353961313362350a626662366361303434383665306531
         34386161363965363634626663343832363664623139336665313131623732343437633439386264
         6534663962663337640a363166383137613835383966396431326165663732646337653235646239
         3534

...
```

yaml格式参考[官网](https://docs.ansible.com/ansible-core/devel/reference_appendices/YAMLSyntax.html)

这里的ssh端口还有另一种写法

```bash
ansible_port: 22
```

#### 测试

```bash
root@ccb07fbb3515:/etc/ansible# ansible all -m ping
node1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
node3 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
node2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

重启其中一个主机的服务

```bash
root@ccb07fbb3515:/etc/ansible# ansible node1 -m command -a "systemctl restart cron"
node1 | CHANGED | rc=0 >>
```

查看所有主机的内核版本

```bash
ansible all -m command -a "uname -a"

node1 | CHANGED | rc=0 >>
Linux cd03436b2424 4.18.0-147.5.1.el8_1.x86_64 #1 SMP Wed Feb 5 02:00:39 UTC 2020 x86_64 GNU/Linux

node2 | CHANGED | rc=0 >>
Linux e8e005edfab9 4.18.0-147.5.1.el8_1.x86_64 #1 SMP Wed Feb 5 02:00:39 UTC 2020 x86_64 GNU/Linux

node3 | CHANGED | rc=0 >>
Linux 88bf589578f5 4.18.0-147.5.1.el8_1.x86_64 #1 SMP Wed Feb 5 02:00:39 UTC 2020 x86_64 GNU/Linux
```

### 最后还可以免密登录

主控端使用ssh-keygen生成公钥和私钥，通过sshpass传递到被控端。输入被控端用户名和密码。

```bash
#!/bin/bash
read -p 'input user: ' USER
echo "$USER"
read -s -p 'input pass: ' PASS
rm -f /root/.ssh/id_rsa
(ssh-keygen -P "" -f /root/.ssh/id_rsa) &>/dev/null

for i in {5..7}
do

    IP_host=172.18.0.${i}
    ping  -c 1 -w 1 ${IP_host} &>/dev/null
    if [ $? -eq 0 ];then
        (sshpass -p ${PASS} ssh-copy-id -o StrictHostKeyChecking=no ${IP_host}) &>/dev/null
        echo -e "\e[1;32m ${IP_host}    ok \e[0m"
    fi
done
```

然后ansible的配置文件hosts里可以不写密码了。

```yaml
---
web: #组名1
  hosts:
    node1: #被控主机1
      ansible_host: 172.18.0.5
      ansible_ssh_port: 22
    node2: #被控主机2
      ansible_host: 172.18.0.6
      ansible_ssh_port: 22
apache: #组名2
  hosts:
    node3: #被控主机3
      ansible_host: 172.18.0.7
      ansible_ssh_port: 22
    node4: #被控主机4
      ansible_host: 172.18.0.8
      ansible_ssh_port: 22
...
```

## **Ansible参数补全功能**

从Ansible 2.9版本开始，ansible支持命令的选项补全功能，它依赖于python的argcomplete插件

```bash
aptitude install python-argcomplete -y #安装
activate-global-python-argcomplete   #激活
```

激活后，就可以按两次tab补全命令和选项了。

## ansible升级到特定版本

### 卸载

查看当前版本

```bash
ansible --version
ansible 2.7.7
```

安装debain包管理器`aptitude`,它可以自动解决包依赖问题。

```bash
apt install aptitude -y
```

卸载旧版本的ansible

```bash
aptitude remove ansible*
```

查看ansible依赖包是否没卸载干净

```bash
apt list | grep ansible*
```

### 安装新版本

debain 系统下ansible所有版本

```bash
https://packages.debian.org/buster-backports/all/ansible/download
```

本次下载2.9.16版本的安装包进行升级

```bash
wget http://ftp.cn.debian.org/debian/pool/main/a/ansible/ansible_2.9.16+dfsg-1~bpo10+2_all.deb
```

安装

```bash
aptitude install ansible_2.9.16+dfsg-1~bpo10+2_all.deb
```

最新版支持`python3`，可以下载。

```bash
aptitude install python3
```

### 查询

```bash
ansible --version 
ansible 2.9.16
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/root/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.7.3 (default, Jan 22 2021, 20:04:44) [GCC 8.3.0]
```

## 其他

### 关于`debain10系统`鼠标右键粘连的解决方法

编辑` vim` 的默认配置文件

```bash
vi /usr/share/vim/vim81/defaults.vim
#第79行
if has('mouse')
set mouse=a
endif
#set mouse=a 改为set mouse-=a
保存退出
```

