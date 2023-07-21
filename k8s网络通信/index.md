# k8s网路通信


## 基于k8s中cni插件calico网络原理使用总结

### 1.实验环境

|  name   | version |       ip        |  cni   |
| :-----: | :-----: | :-------------: | :----: |
| master  | v1.22.2 | ens33:10.4.7.60 | calico |
| worker1 | v1.22.2 | ens33:10.4.7.61 | calico |
| worker2 | v1.22.2 | ens33:10.4.7.62 | calico |

### 2.实验整体拓扑

![整体拓扑](/postimages/整体拓扑.webp)

### 3.部署一个deployment

![deploy1](/postimages/deploy1.webp)

#### 问题1

pod的ip从哪里来？

由`kube-controller-manager`来分配。

```
root@master:~# cat /etc/kubernetes/manifests/kube-controller-manager.yaml | grep cidr
    - --allocate-node-cidrs=true
    - --cluster-cidr=10.244.0.0/16
```

#### 问题2

如何确定集群上正在使用的cni网络插件?

在master节点上查看

![calico](/postimages/calico.webp)

#### 问题3

在k8s中，docker 使用的是bridge（网桥）模式，并且始终处于Down状态，那么calico使用的也是网桥模式码？

使用`ip -d a`可以查看网卡模式.

calico使用都是vxlan技术。

```shell
root@master:~# ip -d a  
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00 promiscuity 0 minmtu 0 maxmtu 0 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:30:56:5c brd ff:ff:ff:ff:ff:ff promiscuity 0 minmtu 46 maxmtu 16110 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 
    inet 10.4.7.60/24 brd 10.4.7.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fe30:565c/64 scope link 
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:97:3a:db:c3 brd ff:ff:ff:ff:ff:ff promiscuity 0 minmtu 68 maxmtu 65535 
    bridge forward_delay 1500 hello_time 200 max_age 2000 ageing_time 30000 stp_state 0 priority 32768 vlan_filtering 0 vlan_protocol 802.1Q bridge_id 8000.2:42:97:3a:db:c3 designated_root 8000.2:42:97:3a:db:c3 root_port 0 root_path_cost 0 topology_change 0 topology_change_detected 0 hello_timer    0.00 tcn_timer    0.00 topology_change_timer    0.00 gc_timer  102.44 vlan_default_pvid 1 vlan_stats_enabled 0 vlan_stats_per_port 0 group_fwd_mask 0 group_address 01:80:c2:00:00:00 mcast_snooping 1 mcast_router 1 mcast_query_use_ifaddr 0 mcast_querier 0 mcast_hash_elasticity 16 mcast_hash_max 4096 mcast_last_member_count 2 mcast_startup_query_count 2 mcast_last_member_interval 100 mcast_membership_interval 26000 mcast_querier_interval 25500 mcast_query_interval 12500 mcast_query_response_interval 1000 mcast_startup_query_interval 3124 mcast_stats_enabled 0 mcast_igmp_version 2 mcast_mld_version 1 nf_call_iptables 0 nf_call_ip6tables 0 nf_call_arptables 0 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
4: cali64bc2599f1b@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default 
    link/ether ee:ee:ee:ee:ee:ee brd ff:ff:ff:ff:ff:ff link-netnsid 0 promiscuity 0 minmtu 68 maxmtu 65535 
    veth numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 
    inet6 fe80::ecee:eeff:feee:eeee/64 scope link 
       valid_lft forever preferred_lft forever
5: cali186813c9403@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default 
    link/ether ee:ee:ee:ee:ee:ee brd ff:ff:ff:ff:ff:ff link-netnsid 1 promiscuity 0 minmtu 68 maxmtu 65535 
    veth numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 
    inet6 fe80::ecee:eeff:feee:eeee/64 scope link 
       valid_lft forever preferred_lft forever
6: calia0e2fa62436@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default 
    link/ether ee:ee:ee:ee:ee:ee brd ff:ff:ff:ff:ff:ff link-netnsid 2 promiscuity 0 minmtu 68 maxmtu 65535 
    veth numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 
    inet6 fe80::ecee:eeff:feee:eeee/64 scope link 
       valid_lft forever preferred_lft forever
7: calia67de3f32d9@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default 
    link/ether ee:ee:ee:ee:ee:ee brd ff:ff:ff:ff:ff:ff link-netnsid 3 promiscuity 0 minmtu 68 maxmtu 65535 
    veth numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 
    inet6 fe80::ecee:eeff:feee:eeee/64 scope link 
       valid_lft forever preferred_lft forever
10: vxlan.calico: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default 
    link/ether 66:4f:26:ae:af:db brd ff:ff:ff:ff:ff:ff promiscuity 0 minmtu 68 maxmtu 65535 
    vxlan id 4096 local 10.4.7.60 dev ens33 srcport 0 0 dstport 4789 nolearning ttl auto ageing 300 udpcsum noudp6zerocsumtx noudp6zerocsumrx numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 
    inet 10.244.219.64/32 scope global vxlan.calico
       valid_lft forever preferred_lft forever
    inet6 fe80::644f:26ff:feae:afdb/64 scope link 
       valid_lft forever preferred_lft forever
```

#### 问题4

pod通信网口和woker网口之间有什么关系？

![pod网络](/postimages/pod网络.webp)

```
worker1:
my-dep-5b7868d854-4rlld   eth0@if14 10.244.235.135/32  -->对应worker1 上 14: cali6ff8e3ade03@if3  网卡
```

![pod与worker联系1](/postimages/pod与worker联系1.webp)

![pod与worker联系2](/postimages/pod与worker联系2.webp)

![pod与worker联系3](/postimages/pod与worker联系3.webp)

#### 问题5

pod上为什么会有169.254.1.1 的默认路由？地址可达吗？

地址不可达。calico使用了proxy arp 技术，地址不是真实存在的。

![问题5-1](/postimages/问题5-1.webp)

![问题5-2](/postimages/问题5-2.webp)

![问题5-3](/postimages/问题5-3.webp)

#### 问题6

相同worker之间的pod是如何通信的？

|           pod           |       ip       | pod interface |     node interface      |  node   |
| :---------------------: | :------------: | :-----------: | :---------------------: | :-----: |
| my-dep-5b7868d854-4rlld | 10.244.235.135 | 3: eth0@if14  | 14: cali6ff8e3ade03@if3 | worker1 |
| my-dep-5b7868d854-mzp9z | 10.244.235.140 | 3: eth0@if15  | 15: cali1292bd788a2@if3 | worker1 |

从10.244.235.135ping 10.244.235.140。

分别在②、③、④上抓包查看网络接口数据包发送接收状态。

网络路径：

`4rlld(10.244.235.135)->14: cali6ff8e3ade03@if3->15: cali1292bd788a2@if3->mzp9z(10.244.235.140) `

​					①										②										③										④

![相同worker的pod之间通信1](/postimages/相同worker的pod之间通信1.webp)

![相同worker的pod之间通信135](/postimages/相同worker的pod之间通信135.webp)

![相同worker的pod之间通信worker14](/postimages/相同worker的pod之间通信worker14.webp)

![相同worker的pod之间通信worker15](/postimages/相同worker的pod之间通信worker15.webp)

![相同worker的pod之间通信140](/postimages/相同worker的pod之间通信140.webp)

#### 问题7

不同worker之间的pod是如何通信的？

worker1 的pod1和worker2的pod3通信过程。跨子网使用vxlan进行通信。

|           pod           |        ip         | pod interface |     node interface      |  node   |
| :---------------------: | :---------------: | :-----------: | :---------------------: | :-----: |
| my-dep-5b7868d854-4rlld | 10.244.235.135/32 |  3:eth0@if14  | 14: cali6ff8e3ade03@if3 | worker1 |
| my-dep-5b7868d854-4dj4p | 10.244.189.91/32  |  3:eth0@if15  | 15: calic18ddbeaf18@if3 | worker2 |

从10.244.235.135ping 10.244.189.91。

网络路径：

`4rlld(10.244.235.135)->vxlan.calico(10.244.235.128)->14: cali6ff8e3ade03@if3->vxlan.calico(10.244.189.64)->15: calic18ddbeaf18@if3->4dj4p(10.244.189.91)`

`4rlld`上的路由信息

![不同worker的pod之间通信135](/postimages/不同worker的pod之间通信135.webp)

![pod1路由信息](https://images-1316789231.cos.ap-shanghai.myqcloud.com/%E4%BA%91%E8%AE%A1%E7%AE%97/pod1%E8%B7%AF%E7%94%B1%E4%BF%A1%E6%81%AF.webp)

根据路由信息，ping 10.244.189.91，会匹配到第一条。第一条路由的意思是：去往任何网段的数据包都发往网管169.254.1.1，然后从eth0网卡发送出去。

路由表中Flags标志的含义：

U up表示当前为启动状态

H host表示该路由为一个主机，多为达到数据包的路由

G Gateway 表示该路由是一个网关，如果没有说明目的地是直连的

D Dynamicaly 表示该路由是重定向报文修改

M 表示该路由已被重定向报文修改



worker1上路由信息

![worker1路由信息](/postimages/worker1路由信息.webp)

当ping包来到worker1节点上，会匹配到路由eth3。该路由的意思是：去往10.244.189.64/26的网段的数据包都发往网关10.4.7.62。因为pod1在7.61，pod3在7.62。所以数据包就通过设备eth3发往到worker2节点上。 

worker2上路由信息

![worker2路由信息](/postimages/worker2路由信息.webp)

当worker2节点网卡收到数据包之后，发现发往的目的ip为10.244.189.91，于是匹配到红线的路由。该路由的意思是：10.244.189.91是本机直连设备，去往设备的数据包发往calic18ddbeaf18。

绿色框是回程路由。

那么该设备是什么呢？这个设备就是veth pair的一端。在创建pod3时calico会给pod3创建一个veth pair设备。一端是pod3的网卡，另一端就是我们看到的calic18ddbeaf18。下面我们验证一下。在pod3中安装ethtool工具，然后使用ethtool -S eth0,查看veth pair另一端的设备号。

![pod3上对端veth](/postimages/pod3上对端veth.webp)

pod3 网卡另一端的设备好号是15，在worker2上查看编号为15的网络设备，可以发现该网络设备就是calic18ddbeaf18。

![worker2上veth](/postimages/worker2上veth.webp)

所以，worker2上的路由，发送calic18ddbeaf18的数据其实就是发送到pod3的网卡中。ping包的旅行到这里就到了目的地。

查看一下pod3中的路由信息，发现该路由信息和pod1中是一样的。

![pod3路由信息](/postimages/pod3路由信息.webp)

#### 问题8

pod是如何访问互联网的？

演示：worker1中pod1访问baidu.com

![pod1外网测试1](/postimages/pod1外网测试1.webp)

查看coredns

![pod1外网测试2](/postimages/pod1外网测试2.webp)

cluster dns由kubelet分配。

![pod1外网测试3](/postimages/pod1外网测试3.webp)

worker1节点收到pod1的ping请求后数据包从eth3网卡发往baidu.com

![pod1外网测试4](/postimages/pod1外网测试4.webp)

查看iptable转发规则

`iptables -nvL -t nat `

![pod1外网测试5](/postimages/pod1外网测试5.webp)

