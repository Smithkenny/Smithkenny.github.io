# 使用docker搭建openvpn


## 需求

通过vpn直接访问内网容器ip可以直接登录，不再需要先登入公网IP再跳转到内网容器内部。

### 访问示意图

```
互联网（openvpn client）-->（公网ip）服务器（私网ip:172.18.0.10）-->服务器端容器（内网172.18.0.8）
```

## openvpn服务端配置

### 拉取镜像

```bash
docker pull kylemanna/openvpn
```

### 创建映射目录

```bash
mkdir -pv /data/openvpn/conf
```

### 生成配置文件

```bash
docker run -v /data/openvpn:/etc/openvpn --rm kylemanna/openvpn ovpn_genconfig -u udp://10.10.10.10
```

### 生成密钥文件

要求输入私钥密码

```bash
docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn ovpn_initpki
	Enter PEM pass phrase: 098poilkj.										# 输入私钥密码
	Verifying - Enter PEM pass phrase: 098poilkj.							# 重新输入一次密码
	Common Name (eg: your user,host,or server name) [Easy-RSA CA]: 		# 输入一个CA名称。可以不用输入，直接回车
	Enter pass phrase for /etc/openvpn/pki/private/ca.key: 123456		# 输入刚才设置的私钥密码，完成后在输入一次

```

### 生成客户端证书

test1改成其他名字

```bash
docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn easyrsa build-client-full test1 nopass
	Enter pass phrase for /etc/openvpn/pki/private/ca.key: 098poilkj.		# 输入刚才设置的密码
```

### 导出客户端配置

```bash
docker run -v /data/openvpn:/etc/openvpn --rm kylemanna/openvpn ovpn_getclient test1 > /data/openvpn/conf/test1.ovpn
```

### 启动openvpn

```bash
docker run --name openvpn -v /data/openvpn:/etc/openvpn -d -p 1194:1194/udp --cap-add=NET_ADMIN kylemanna/openvpn
```

## openvpn用户管理

### 添加用户脚本

`vim add_user.sh`

```bash
#!/bin/bash
read -p "please your username: " NAME
docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn easyrsa build-client-full $NAME nopass
docker run -v /data/openvpn:/etc/openvpn --rm kylemanna/openvpn ovpn_getclient $NAME > /data/openvpn/conf/"$NAME".ovpn
docker restart openvpn
```

### 删除用户脚本

`vim del_user.sh`

```bash
#!/bin/bash
read -p "Delete username: " DNAME
docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn easyrsa revoke $DNAME
docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn easyrsa gen-crl
docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn rm -f /etc/openvpn/pki/reqs/"DNAME".req
docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn rm -f /etc/openvpn/pki/private/"DNAME".key
docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn rm -f /etc/openvpn/pki/issued/"DNAME".crt
docker restart openvpn
```

### 添加用户

```bash
./add_user.sh	# 输入要添加的用户名，回车后输入刚才创建的私钥密码
```

创建的证书在`/data/openvpn/conf`目录下。

## openvpn客户端配置

官网下载[客户端](https://openvpn.net/vpn-client/)

从服务端把证书拷贝到桌面上，并将证书`test1.ovpn`直接导入到客户端内。然后输入容器内部IP就可以直接访问docker内部容器了。

![image-20220417135159910](/postimages/image-20220417135159910.webp)

