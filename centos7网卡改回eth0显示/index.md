# centos7网卡改回eth0显示


## 将centos7网卡改回eth0显示

```bash
grubby --update-kernel=ALL --args="net.ifnames=0 biosdevname=0"
```

实验环境-->net.ifnames=0

物理环境dell--->biosdevname=0 net.ifnames=0   

云环境------>   默认都是eth0 eth1

#### 修改网卡配置文件的名称

```bash
mv /etc/sysconfig/network-scripts/ifcfg-ens32  /etc/sysconfig/network-scripts/ifcfg-eth0
```

#### 修改网卡配置文件中设备的名字

```bash
sed -i 's#ens32#eth0#g' /etc/sysconfig/network-scripts/ifcfg-eth0
```

#### 重启设备

#### 网卡配置实例

```perl
NAME=eth0
DEVICE=eth0
ONBOOT=yes
BOOTPROTO=static
IPADDR=192.168.1.192
NETMASK=255.255.255.0
GATEWAY=192.168.1.254
DNS1=114.114.114.114
TYPE=Ethernet
```

#### 查看网卡信息

```perl
cat /proc/net/dev
```


