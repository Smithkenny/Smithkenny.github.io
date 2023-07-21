# docker创建专有网卡


## docker创建专有网卡

### 需求：

`tomcat`容器需要通过`jdbc模块`连接数据库`mysql`，配置文件如果指定`IP地址`，`mysql`容器一旦损坏，`IP地址`就会更改，需要将`mysql容器`的`IP`固定。这样就不用每次更改`tomcat`配置文件了。

### 专有网卡创建

**新创建一个名为`myapp`的桥接网卡**

```bash
docker network create myapp
```

**使用`--network-alias`参数给网卡起别名，启动`MySQL`容器。**

```bash
docker run -d \
     --network myapp --network-alias mysql1 \
     --name=mysql57-test \
     -p 33306:3306 \
     -v /tmpdisk/test2:/var/lib/mysql \
     -v /etc/localtime:/etc/localtime:ro \
     -e MYSQL_ROOT_PASSWORD=123456 \
     -e MYSQL_DATABASE=todos \
     mysql:5.7
```

**注意：这里创建的`mysql`容器使用`myapp 网卡`，分配的`IP`自动和`mysql1 网卡别名`绑定，后面创建的容器只要使用同一个网卡创建的，都可以和`mysql`容器互通。**

如何证明呢？

**使用`nicolaka/netshoot`镜像创建一个测试容器**

```bash
docker run -it --name test --network myapp nicolaka/netshoot
```

可以直接和`mysql`容器通信

```bash
62337a7ad7f1# ping mysql1 
PING mysql1 (172.20.0.5) 56(84) bytes of data.
64 bytes from mysql57-test.myapp (172.20.0.5): icmp_seq=1 ttl=64 time=0.042 ms
64 bytes from mysql57-test.myapp (172.20.0.5): icmp_seq=2 ttl=64 time=0.020 ms
```

**查看解析，发现一条A记录。正是这条记录将`myapp`网卡自动分配的`ip`和网卡别名`mysql1`绑定。**

```bash
62337a7ad7f1# dig mysql1 

; <<>> DiG 9.18.11 <<>> mysql1
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 10768
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;mysql1.				IN	A

;; ANSWER SECTION:
mysql1.			600	IN	A	172.20.0.5

;; Query time: 0 msec
;; SERVER: 127.0.0.11#53(127.0.0.11) (UDP)
;; WHEN: Wed Feb 22 01:36:59 UTC 2023
;; MSG SIZE  rcvd: 46
```

**创建`tomcat`容器**

```bash
docker run -itd \
    -p 28080:8080 \
    --restart=always \ 
    --name tomcat-test \ 
    --network myapp \
    -v /tmpdisk/tomcat-test2/webapps:/usr/local/tomcat/webapps \
    -v /etc/localtime:/etc/localtime:ro \
    tomcat:9.0.56
```

**更改`jdbc`连接配置，将`localhost`改成`mysql1`**

```bash
旧：
jdbc:mysql://localhost:3306/jt_ow_dbsync_src?useUnicode=true&amp;characterEncoding=UTF-8&amp;useSSL=false
新：
jdbc:mysql://mysql1:3306/jt_ow_dbsync_src?useUnicode=true&amp;characterEncoding=UTF-8&amp;useSSL=false
```

### 测试

**测试内容：`mysql`容器`IP`更改后，会不会影响业务访问。**

**查看当前`mysql` 容器`IP**`

```sh
[root@HZ ~]$ docker inspect mysql57-test | grep -i ipaddress
            "SecondaryIPAddresses": null,
            "IPAddress": "",
                    "IPAddress": "172.20.0.5",
```

**删除容器**

```sh
docker rm -f mysql57-test
```

**重新创建新容器**

```sh
docker run -itd --name test2 --network myapp busybox
```

**再创建`mysql`容器**

```sh
docker run -d \
     --network myapp --network-alias mysql1 \
     --name=mysql57-test \
     -p 33306:3306 \
     -v /tmpdisk/test2:/var/lib/mysql \
     -v /etc/localtime:/etc/localtime:ro \
     -e MYSQL_ROOT_PASSWORD=123456 \
     -e MYSQL_DATABASE=todos \
     mysql:5.7
```

**再次查看`mysql`容器`IP`**

```sh
[root@HZ ~]$ docker inspect mysql57-test | grep -i ipaddress
            "SecondaryIPAddresses": null,
            "IPAddress": "",
                    "IPAddress": "172.20.0.6",
```

**访问业务，发现业务访问正常。所以完美实现了`mysql`容器`内部IP`变更后不会影响业务。**

