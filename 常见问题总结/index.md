# 常见问题总结


## 描述Linux下软链接和硬链接的区别

实践：当前所在路径`/root`

准备工作

```
echo 123 > /tmpdisk/a.txt
echo 123 > /tmpdisk/b.txt
mkdir -p /tmpdisk/ylink
mkdir -p /tmpdisk/rlink
```

分别创建软链接和硬链接

```
ln -s  /tmpdisk/a.txt ra.txt
ln   /tmpdisk/b.txt yb.txt
ln  /tmpdisk/ylink   ylink 
#创建失败，硬链接不允许文件夹创建。ln: '/tmpdisk/ylink': hard link not allowed for directory
ln -s  /tmpdisk/rlink  rlink
```

查看inode节点

```
[root@43f873a76b81 ~]# ls -li 
total 8
84271555 -rw------- 1 root root 3302 May 31  2018 anaconda-ks.cfg
87108116 lrwxrwxrwx 1 root root   14 Mar 28 06:18 ra.txt -> /tmpdisk/a.txt
87108117 lrwxrwxrwx 1 root root   14 Mar 28 06:18 rlink -> /tmpdisk/rlink
 1588983 -rw-r--r-- 2 root root    4 Mar 28 05:27 yb.txt
[root@43f873a76b81 ~]# ls -li /tmpdisk/
total 8
1588982 -rw-r--r-- 1 root root 4 Mar 28 05:26 a.txt
1588983 -rw-r--r-- 2 root root 4 Mar 28 05:27 b.txt
1588941 drwxr-xr-x 2 root root 6 Mar 28 06:13 rlink
1588940 drwxr-xr-x 2 root root 6 Mar 28 06:13 ylink
```

发现硬链接的原文件和链接文件`inode`一致。都为1588983。

软链接相当于`快捷方式`

```
ln -s 源文件路径 链接文件路径
链接文件路径-------->源文件路径
```

总结：

默认不带参数的情况下，`ln`创建的是硬链接，带`-s`参数的`ln`命令创建的是软链接。

硬链接文件与源文件的`inode`节点号相同，软链接文件的`inode`节点号，与源文件不同。

`ln`命令不能对目录创建硬链接，但可以创建软链接。对目录的软链接会经常使用到

删除软链接文件，对源文件和硬链接文件无 任何影响。

删除文件的硬链接文件，对源文件及软链接文件无任何影响。

删除链接文件的源文件，对硬链接文件无影响，会导致其软链接失效（红底白字闪烁）。

同时删除源文件及其硬链接文件，整个文件才会被真正的删除。

很多硬件设备的快照功能，使用的就是列斯硬链接的原理

软链接可以跨文件系统，硬链接不可以跨文件系统。

## Linux 如何查找具有相同 `inode` 号的文件?

### 通过 `inode`号查找

```
find / -xdev -inum <inode_num> 2>/dev/null
find -L / -xdev -inum <inode_num> 2>/dev/null
```

### 通过文件名查找

```
find / -xdev -samefile <file_name> 2>/dev/null
find -L / -xdev -samefile <file_name> 2>/dev/null
```

其中

```
-xdev 表示不跨越文件系统查找
-L 表示考虑符号链接(软链接)
```

实例

查找源文件为`/tmpdisk/a.txt`的链接文件

```
[root@43f873a76b81 ~]# find / -xdev -samefile /tmpdisk/b.txt 
/root/yb.txt
/tmpdisk/b.txt
[root@43f873a76b81 ~]# ll -i /root/yb.txt /tmpdisk/b.txt 
1588983 -rw-r--r-- 2 root root 4 Mar 28 05:27 /root/yb.txt
1588983 -rw-r--r-- 2 root root 4 Mar 28 05:27 /tmpdisk/b.txt
```

## linux常见的系统日志文件都有哪些，各自的用途？

```
/var/log/messages 内核及公共消息日志
/var/log/cron	计划任务日志
/var/log/dmesg	系统引导日志
/var/log/maillog	邮件系统日志
/var/log/secure	记录与访问限制相关日志
/var/log/yum.log 使用yum安装软件的日志
```

## 常见Linux开机设置文件

```
/etc/fstab 实现开机自动挂载
/etc/initab	定义开机进入默认级别的配置文件
/etc/rc.local 定义开机自定义任务的配置文件
```

## 请描述Linux系统优化的12个步骤

1.不用`root`直接登陆系统改用普通用户登录。

2.禁止ssh远程，可以更改默认远程连接ssh服务及禁止`root`远程连接。

```
vi /etc/ssh/sshd_config
PermitRootLogin no
Port 22 #改为其他端口
重启sshd服务
systemctl restart sshd.service
```

3.时间同步：定时更新服务器时间

每天12点同步一次。

`crontab -e`

```
0 12 * * * * /usr/sbin/ntpdate 192.168.0.1
```

```
设置ntp开机启动并启动ntp
systemctl enable ntpd
systemctl start ntpd
```

定时任务验证

```
https://crontab.guru/
```

4.关闭`selinux`及`iptables`(`iptables`工作场景如有wan ip,一般要打开，高并发除外)

`iptables`是`firewalld`的子模块。

```
systemctl stop firewalld.service 关闭服务
systemctl status firewalld.service 查看服务状态
systemctl disable firewalld.service 设置开机默认关闭防火墙
```

```
永久关闭selinux
/etc/selinux/config
将SELINUX=enforcing 改为SELINUX=disabled
临时关闭
getenforce 获取状态
setenforce 0 关闭 #0关闭，1开启
```

5.配置`yum`更新源，从国内更新下载安装rpm包。

备份

```
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```

下载新的 `CentOS-Base.repo 到 /etc/yum.repos.d/`

```
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
或者
curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
```

运行` yum makecache `生成缓存

```
yum makecache
```

6.调整文件描述符的数量，进程及文件的打开都会消耗文件描述符。

内核（kernel）利用文件描述符（file descriptor）来访问文件。文件描述符是非负整数。打开现存文件或新建文件时，内核会返回一个文件描述符。读写文件也需要使用文件描述符来指定待读写的文件。通俗来讲，因为linux一切皆文件，所以占用文件描述符超过了系统或者用户要求的最大值，就无法再打开了，文件描述符主要是系统级和用户级。查看方式如下

```
sysctl -a|grep fs.file-max 查看系统级文件描述符数量
ulimit -n 查看用户级文件描述符数量
```

用户级限制：`ulimit`命令看到的是用户级的最大文件描述符限制，也就是说每一个用户登录后执行的程序占用文件描述符的总数不能超过这个限制
系统级限制：`sysctl`命令与`proc`文件系统中查看到的数值是一样的，这属于系统级限制，它是限制所有用户打开文件描述符的总数.

修改用户级文件描述符(需要root权限或root用户)

```
vi /etc/security/limits.conf。修改文件描述符，应该注意字段下是否存在nofile字段，如果存在直接修改，不存在在末尾添加保存即可。
* hard nofile 4096
* soft nofile 2048
```

修改系统级文件描述符(需要root权限或root用户)：

```
vi /etc/sysctl.conf
```

找到`fs.file-max`，将`fs.file-max`值修改，保存即可。

```
sysctl -p #加载内核参数使修改生效
```

7.定时自动清理`/var/spool/clientmquene/`目录垃圾文件，防止节点被占满（c6.4默认没有Sendmail，因此可以不配。）

8.精简开机启动服务(crond、sshd、network、rsyslog)

9.Linux内核参数优化`/etc/sysctl.conf`，执行`sysctl -P`生效。

10.更改字符集，支持中文，但是还是建议使用英文，防止乱码问题出现。linux默认编码为`utf-8`

11.锁定关键系统文件

```
chattr +i /etc/passwd /etc/shadow /etc/group /etc/gshadow /etc/inittab
```

处理以上内容后，把`chattr`改名，就更安全了。

12.清空`/etc/issue`去除系统及内核版本登录前的屏幕显示。

## 请给出查看当前哪些在线用户的Linux命令

```
w #显示目前系统登录用户
who #显示目前已登陆用户信息
last #列出当前与过去登录系统的用户相关信息
lastlog #检查某特定用户上次登录时间
whoami #打印与当前生效的用户ID关联的用户名
finger #用户信息查找程序
id #显示指定用户或当前用户的用户信息与组信息
```

## 输出重定向相关

`/dev/null`是特殊的字符设备文件，表示黑洞设备或空设备

```
>或者1> #输出重定向：把前面输出的东西输入到后面的文件中，会删除文件原有内容。
>>或者1>> #追加重定向：把前面输出的东西追加到后边的文件中，不会删除文件原有内容。
<或者<0 #输入重定向：输入重定向用于改变命令的输入，指定输入内容那个，后跟文件名。
<<或者<<0 #输入重定向：后跟字符串，用来表示“输入结束”，也可以用ctrl+d来结束输入。
2> #错误重定向：把错误信息输入到后边的文件中，会删除文件原有内容。
2>> #错误追加重定向：把错误信息追加到后边的文件中，不会删除文件原有内容。
```

```
标准输入（stdin）: 0 使用 <或者<<
标准输出（stdout）: 1 使用 >或者>>。正常的输出
标准错误输出（stderr）:2 使用2> 或者2>>
特殊：
2>&1 就是把标准错误重定向到标准输出（>&）
>/dev/null 2>&1 等价于1>/dev/null 2>/dev/null
```

例如：找到文件，忽略错误输出结果。

```bash
find / -name python3.6 2>/dev/null

/usr/bin/python3.6
/usr/lib/python3.6
/usr/lib64/python3.6
/usr/local/lib/python3.6
/usr/local/lib64/python3.6
```



## `sed`常见用法

删除文件`ra.txt`的第2行内容。

```
sed -i '2d' ra.txt
```

将文件`yb.txt`中的`5555`改为`8080`

```
sed -i 's/5555/8080/g' yb.txt
```

使用`-e`同时更改多个文件。

```
sed -i -e 's/6666/9999/g' a.txt b.txt
```

## `$0`、`dirname`和、`basename`有什么作用？具体举个例子。

## `z.sh`文件

```shell
#!/bin/bash
echo "#############"
echo "# this is '$0' test#"
echo "#############"
echo $0
echo 
echo "###############"
echo "# this is 'dirname' test#"
echo "############@@#"
cd `dirname $0` && pwd
echo 
echo "###############"
echo "# this is 'basename' test#"
echo "####################"
echo `basename $0`
```

执行结果

```
#############
# this is 'z.sh' test#
#############
z.sh

###############
# this is 'dirname' test#
############@@#
/tmpdisk

###############
# this is 'basename' test#
####################
z.sh
```

总结：

`dirname`获取脚本路径名

`basename`获取脚本名

`$0`获取脚本名和脚本路径名

## 用`nohup`把一个脚本放后台运行

```
nohup sh test.sh > filename 2>&1 &
```

## 有`1-9`的数字，`echo`输出`1-9`的数字时，屏蔽`0-5`数字再输出

```
echo '1234567890' | sed 's/[0-5]//g'
```

## 如何用`wget`和`curl`监控web是否正常

`wget` 常用命令参数

```
--spider                             模拟爬虫的行为去访问网站，但不会下载网页
-q --quite                           安静的访问，禁止输出，类似-o /dev/null
-o --output-file=FILE                记录到输出文件
-T --timeout=SECONDS             	 访问网站的超时时间
-t -tries=NUMBER                     重试次数
```

`curl `常用命令参数

```
-I/--header　　　　　　　　　　          显示响应头信息
-m/--max-time<seconds>               访问超时时间
-o/--output<file>                    记录访问信息到文件
-s/--silent                          静默模式访问，不输出信息
-w/--write-output<format>            以固定格式输出，例如%{http_code},输出状态码
```

`wget`

```
[root@43f873a76b81 tmpdisk]# wget -T 5 -q -t 2 --spider www.qq.com 
[root@43f873a76b81 tmpdisk]# echo $?
0
```

`curl`

```
[root@43f873a76b81 tmpdisk]# curl -o /dev/null -s -w %{http_code} https://www.baidu.com
200
```






