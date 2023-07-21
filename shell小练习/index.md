# shell小练习

# shell小练习

job1:检索本机的IP、NETMASK、MAC地址、广播地址

```python
IP: 172.20.10.3
NetMask: 255.255.255.240
Broadcast: 172.20.10.15
MAC Address: 00:0c:29:8d:49:ea
```

job2:将系统中所有普通用户的用户名、密码和默认shell保存到一个文件中，要求用户名密码和默认shell之间用tab分隔

```python
ayitula x /bin/bash
```

job3: 虚拟机中添加一块硬盘，盘号为sdb。主分区sdb3，543M，ext4格式。其他空间都分给拓展分区sdb4。逻辑分区sdb5，2G，逻辑分区sdb6，3G。 创建一个卷组，PE为16M，创建逻辑卷大小为2.5G，格式为xfs。

主分区sdb3自动挂载到/data/data1上，逻辑卷挂载到/data/data2上。

最后分别验证并输出挂载结果。



job4:编写脚本，清空job3内的所有配置，包括还原/etc/fstab文件。



job5:模拟登录系统。输入用户名、密码登录系统。

终端提示信息：

```shell
Centos Linux 8 (Core)
Kernal 5.4.0-113-generic on an x86_64

588c34fafcd5 login: smith
Password: 
```

job6：

内存使用率统计，要求打印内存使用率。

内存使用率公式： 使用量/总量*100%

job7:

实现一个四则运算计算器。要求用户传输三个参数，num1 算数运算符 num2 。运算输出结果。

job8:

写一个监控CPU 平均负载值的脚本。收集cpu load 平均负载值到数组，打印输出对应的负载值。

输出样式

```sh
CPU 1min平均负载为:  0.10
CPU 5min平均负载为:  0.16
CPU 15min平均负载为:  0.08
```


