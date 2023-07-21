# linux下混杂模式判断

## 简介

混杂模式就是接收所有经过网卡的数据包，包括不是发给本机的包，即不验证MAC地址。普通模式下网卡只接收发给本机的包（包括广播包）传递给上层程序，其它的包一律丢弃。

一般来说，混杂模式不会影响网卡的正常工作，多在网络监听工具上使用。

## 网卡工作模式

广播模式（Broad Cast Model）:它的物理地址（MAC）地址是 0Xffffff 的帧为广播帧，工作在广播模式的网卡接收广播帧。 

多播传送（MultiCast Model）：多播传送地址作为目的物理地址的帧可以被组内的其它主机同时接收，而组外主机却接收不到。但是，如果将网卡设置为多播传送模式，它可以接收所有的多播传送帧，而不论它是不是组内成员。 

直接模式（Direct Model）:工作在直接模式下的网卡只接收目地址是自己 Mac地址的帧。 

混杂模式（Promiscuous Model）:工作在混杂模式下的网卡接收所有的流过网卡的帧，信包捕获程序就是在这种模式下运行的。

**网卡的缺省工作模式包含广播模式和直接模式，即它只接收广播帧和发给自己的帧。如果采用混杂模式，一个站点的网卡将接受同一网络内所有站点所发送的数据包这样就可以到达对于网络信息监视捕获的目的。**

## 开启混杂模式

Linux下设置把网卡设置成混杂模式的命令也很简单。

```shell
ifconfig ens33 promisc
cat /sys/class/net/ens33/flags
```

![开启混杂模式](/postimages/开启混杂模式.webp)

## 取消混杂模式

```shell
ifconfig ens33 -promisc
cat /sys/class/net/ens33/flags
```

![关闭混杂模式](/postimages/关闭混杂模式.webp)

## 拓展

使用tcpdump工具抓包时，抓包的网口会临时变成混杂模式。

```shell
[root@nginx140 ~]# tcpdump -i ens33 -vv -nn -w ./test.cap 
tcpdump: listening on ens33, link-type EN10MB (Ethernet), capture size 262144 bytes
^C537 packets captured
540 packets received by filter
0 packets dropped by kernel
```

抓包的同时查看对应网卡标志位

```shell
[root@nginx140 ~]# cat /sys/class/net/ens33/flags 
0x1103
```

## 总结

当网卡的标志位为`0x1003`时，没有开启混杂模式。而网卡标志位为`0x1103`时，说明该网卡开启了混杂模式。

