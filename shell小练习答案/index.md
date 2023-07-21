# shell小练习答案

# shell小练习答案

job1:检索本机的IP、NETMASK、MAC地址、广播地址

```python
IP: 172.20.10.3
NetMask: 255.255.255.240
Broadcast: 172.20.10.15
MAC Address: 00:0c:29:8d:49:ea
```

```shell
ifconfig eth0 | grep inet | tr -s " " | cut -d " " -f 3 | xargs echo "IP: "
ifconfig eth0 | grep inet | tr -s " " | cut -d " " -f 5 | xargs echo "NetMask: "
ifconfig eth0 | grep inet | tr -s " " | cut -d " " -f 7 | xargs echo "Broadcast: "
ifconfig eth0 | grep ether | tr -s " " | cut -d " " -f 3 | xargs echo "MAC Address: "
```

job2:将系统中所有普通用户的用户名、密码和默认shell保存到一个文件中，要求用户名密码和默认shell之间用tab分隔

```python
ayitula x /bin/bash
```

```shell
cat 1.txt | grep -i bash | grep -v "root" | cut -d ":" -f 1,2,7 | tr -s ":" "\t" > res      
```

```shell
# 1.txt 文本
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:65534:65534:Kernel Overflow User:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
systemd-coredump:x:999:997:systemd Core Dumper:/:/sbin/nologin
systemd-resolve:x:193:193:systemd Resolver:/:/sbin/nologin
hello:x:1500:1000:this is a test user:/home/hello:/bin/bash
```

job3

`disk_partition.sh`

```shell
#!/bin/bash
fdisk /dev/sdb <<EOF
n
p
3

+543M

n
e
4


n
l

+2G

n
l

+3G

w
EOF

#2、创建逻辑卷
   #2.1 创建物理卷
    pvcreate /dev/sdb5 /dev/sdb6
   #2.2 创建卷组
    vgcreate -s 16M vg100 /dev/sdb{5..6} <<EOF
y
EOF
   #2.3 创建逻辑卷
    lvcreate -L 2.5G -n lv100 vg100 <<EOF
y
EOF

#3、格式化
mkfs.ext4 /dev/sdb3
mkfs.xfs /dev/vg100/lv100

#4、修改/etc/fstab,实现自动挂载
echo  "/dev/sdb3   /data/data1 ext4  defaults   0 0" >> /etc/fstab
echo "/dev/vg100/lv100 /data/data2  xfs   defaults 0 0" >> /etc/fstab

#5、挂载分区
mkdir -p /data/data{1..2}
mount -a

#6、验证并输出挂载结果
mount |grep "/dev/sdb3"
test $? -eq 0&&echo "/dev/sdb3 挂载成功" || echo "/dev/sdb3挂载失败"

##注意检索的时候，mount输出中LV的表示方式，或者直接检索挂载点/data/data2也可以。
mount |grep "vg100-lv100"
test $? -eq 0&&echo "/dev/vg100/lv100 挂载成功" || echo "/dev/vg100/lv100挂载失败"
```

job4

`back_init.sh`

```shell
#!/bin/bash
umount /data/data1
umount /data/data2

vgremove vg100 <<EOF
y

y
EOF
pvremove /dev/sdb5
pvremove /dev/sdb6

fdisk /dev/sdb <<EOF
d

d

d

d

w
EOF
sed -i '/^\/dev\/sdb3/'d /etc/fstab
sed -i '/^\/dev\/vg100/'d /etc/fstab
```

Tips: 

使用EOF ,表示后续的输入作为子命令或子shell的输入，直到遇到EOF为止。再返回到主shell。

job5小练习答案：

```shell
#!/bin/bash
#
#Author: Mr.Lee
#Description: 仿真登陆
IP=`ifconfig eth0 | egrep -w "inet"| awk '{print $2}'`

#清屏
clear
#输出提示信息
echo "Centos Linux 8 (Core)"
echo -e "Kernal `uname -r` on an `uname -m`"
echo -e "Web console: https://localhost:9000/ or https://$IP:9090/ \n"
#交互输入登录名
echo -n "$HOSTNAME login: "
read account
#交互输入密码
read -s -t30 -p "Password: " pw
echo
```

job6:

分析：

1.获取内存总量

2.获取内存使用量

3.运算输出结果

```shell
#!/bin/bash
#Author: Mr.lee
#Release
#Description: 内存使用率计算脚本

#获取内存总量
Mem_total=`free -m | grep Mem | awk '{print $2}'`
#获取内存使用的量
Mem_used=`free -m | grep Mem | awk '{print $3}'`
#计算输出
#浮点运算中：同优先级的情况下，大数除以小数 尽可能保证精确。先乘后除。
echo "内存使用率: `echo "scale=2;$Mem_used*100/$Mem_total"|bc`%"
```

job7 :

```shell
#!/bin/bash
#Author: Mr.lee
#Release
#Description: 四则运算计算器
echo "$1 $2 $3" |bc
```

job8

基本思路：截取uptime命令输出结果中最后3个数字，分别代表1min、5min、15min cpu的负载。

普通方法实现

```sh
#!/bin/bash
#Author: Mr.Lee
#Release:
#Description: 打印cpu 1min 5min 15min的负载值
one_min=`uptime | tr -s " " | cut -d "," -f4 | cut -d ":" -f2`
five_min=`uptime | tr -s " " | cut -d "," -f5`
fiveteen_min=`uptime | tr -s " " | cut -d "," -f6`
echo -ne "CPU 1min平均负载为: "$one_min
echo -ne "\nCPU 5min平均负载为: "$five_min 
echo -ne "\nCPU 15min平均负载为: "$fiveteen_min 
echo
```

使用数组实现

```sh
#!/bin/bash
#Author: Mr.Lee
#Release:
#Description: 打印cpu 1min 5min 15min的负载值
cpu_load=(`uptime | tr -s " "| cut -d " " -f11-13| tr "," " "`)
echo "CPU 1min平均负载: ${cpu_load[0]}"
echo "CPU 5min平均负载: ${cpu_load[1]}"
echo "CPU 15min平均负载: ${cpu_load[2]}"
```


