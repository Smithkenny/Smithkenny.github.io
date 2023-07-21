# centos7.9更换网卡名称

# centos7.9恢复旧网卡名称显示及网口乱序解决方法



#### 换回旧版网卡名称

```shell
grubby --update-kernel=ALL --args="net.ifnames=0 biosdevname=0" 
```

#### 修改`/etc/sysconfig/network-scripts/`中的网卡配置文件

```shell
[root@QZ network-scripts]# cat ifcfg-lan0 
TYPE="Ethernet"
BOOTPROTO="static"
DEFROUTE="yes"
NAME="lan0"
DEVICE="lan0"
ONBOOT="yes"
IPADDR=192.168.242.130
NETWORK=255.255.255.0
GATEWAY=192.168.242.1
[root@QZ network-scripts]# cat ifcfg-lan1
TYPE="Ethernet"
BOOTPROTO="static"
DEFROUTE="yes"
NAME="lan1"
DEVICE="lan1"
ONBOOT="yes"
IPADDR=192.168.35.130
NETWORK=255.255.255.0
GATEWAY=192.168.35.2
DNS1=192.168.35.2
[root@QZ network-scripts]# cat ifcfg-lan2
TYPE="Ethernet"
BOOTPROTO="static"
DEFROUTE="yes"
NAME="lan2"
DEVICE="lan2"
ONBOOT="yes"
IPADDR=192.168.242.131
NETWORK=255.255.255.0
```

#### 重启设备

```shell
reboot
```

#### 带有光口的物理服务器经常会遇到网口乱序现象。解决方法：

```shell
vi /etc/systemd/network/10-lan0.link
[Match]
  MACAddress=00:0c:29:36:a6:c7

[Link]
  Name=lan0

vi /etc/systemd/network/11-lan1.link
[Match]
  MACAddress=00:0c:29:36:a6:b3

[Link]
  Name=lan1

[Match]
  MACAddress=00:0c:29:36:a6:bd
  
vi /etc/systemd/network/12-lan2.link
[Link]
  Name=lan2
```

#### 最后创建对应名称的网卡配置文件，重启设备。


