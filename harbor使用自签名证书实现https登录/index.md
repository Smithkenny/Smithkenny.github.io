# harbor使用自签名证书实现https登录

## 前言

上次安装了Harbor，并没有开启https，不太安全，这次记录下自签名证书来实现https登录。

## 自签名证书创建过程

创建证书目录

```bash
mkdir /root/ca -p
cd /root/ca
#以下命令创建CA证书
openssl req  -newkey rsa:4096 -nodes -sha256 -keyout ca.key -x509 -days 365 -out ca.crt
```

其中：（req：申请证书签署请求；-newkey 新密钥 ；-x509：可以用来显示证书的内容，转换其格式，给CSR签名等X.509证书的管理工作，这里用来自签名。）
一路回车出现Common Name 输入IP或域名:

```bash
Common Name (eg, your name or your server's hostname) []:harbor.lhp.com
```

### 生成证书签名请求

```bash
openssl req  -newkey rsa:4096 -nodes -sha256 -keyout harbor.lhp.com.key
一路回车出现Common Name 输入IP或域名
Common Name (eg, your name or your server's hostname) []:harbor.lhp.com
```

### 生成x509 v3 扩展文件

```bash
cat > v3.ext <<-EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = DNS:harbor.lhp.com
EOF
```

### 生成主机证书

```bash
openssl x509 -req -sha512 -days 3650 \
    -extfile v3.ext \
    -CA ca.crt -CAkey ca.key -CAcreateserial \
    -in harbor.lhp.com.csr \
    -out harbor.lhp.com.crt
```

### 增加docker证书可信

```bash
openssl x509 -inform PEM -in harbor.lhp.com.crt -out harbor.lhp.com.cert
mkdir -p /etc/docker/certs.d/
cp harbor.lhp.com.cert /etc/docker/certs.d/harbor.lhp.com/
cp harbor.lhp.com.key /etc/docker/certs.d/harbor.lhp.com/
cp ca.crt /etc/docker/certs.d/harbor.lhp.com/
```

### 重启docker

```bash
/etc/init.d/docker restart 
```

### 配置harbor.yml

```bash
#set hostname
hostname = harbor.lhp.com     #域名或ip
#set ui_url_protocol
uiurlprotocol = https    #这里改为https****
......
#The path of cert and key files for nginx, they are applied only the protocol is set to https
ssl_cert = /root/ca/harbor.lhp.com.crt     #crt位置
ssl_cert_key = /root/ca/harbor.lhp.com.key  #key的位置
```

### 配置启动harbor

```bash
#到harbor安装目录下更新配置文件
./prepare 
#启动harbor
docker-compose -f docker-compose.yml up -d
```

## 测试

https://harbor.lhp.com

### 重新登录harbor报错 x509

重新编辑/etc/docker/daemon.json文件

```bash
{
  "registry-mirrors": ["https://jliunkl7.mirror.aliyuncs.com"],
  "insecure-registries": ["harbor.lhp.com"]
}
```

重启docker即可.

[官方参考](https://goharbor.io/docs/2.1.0/install-config/configure-https/)




