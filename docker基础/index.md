# docker基础

## 安装及命令

### Docker命令

#### 常用

```shell
docker version  
docker info  
docker pull  
docker login  
docker logout  
docker images  
docker ps -a  
docker start|stop|restart  
docker rm xxxx  
docker rmi xxxx  
docker exec -it `name OR id` /bin/bash
```

#### 批量停止删除容器和镜像

停止所有容器

```shell
docker stop $(docker ps -aq)
```

删除所有的容器

```shell
docker rm $(docker ps -aq)
```

删除所有的镜像

```shell
docker rmi $(docker images -q)
```

#### Docker目录拷贝

本机`/root/test`目录拷贝到容器内`/root`目录内

```shell
docker cp /root/test 28a186bbebd4:/root/
```

本机`/root/test`目录拷贝到容器内,并更名为`/test123`.注意命令后没有`/`.

```shell
docker cp /root/test 28a186bbebd4:/test123
```

将容器内的`/test`目录拷贝到主机的`/root`目录内.

```shell
docker cp  28a186bbebd4:/test /root/
```

#### Docker 文件拷贝

本机`1.db`文件拷贝到容器内`root`目录内.

```shell
docker cp 1.db 28a186bbebd4:/root/
```

将容器内的`/root/1.db`文件拷贝到主机的当前目录.

```shell
docker cp 28a186bbebd4:/root/1.db .
```

#### Docker命令补全

安装 bash-completion

```shell
sudo yum install -y bash-completion
```

根据官方文档进一步配置

[官网链接](https://gdevillele.github.io/compose/completion/)

```shell
curl -L https://raw.githubusercontent.com/docker/compose/$(docker-compose version --short)/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose
```

重新进入终端即可补全命令。

### Docker镜像

#### 容器创建镜像

通过容器提交生成镜像

```shell
docker commit 容器名 镜像名
```

保存镜像到文件

```shell
docker save 镜像名 > 文件名.tar 
```

从文件恢复到镜像

```shell
docker load < 文件名.tar
```

通过镜像启动新容器

```shell
docker run --name 容器名 相关参数 最后接新的镜像名
```

例如

赋予容器所有权限

```shell
docker run -it --name test -d --privileged=true centos:7.6.1810 /usr/sbin/init 
```

容器暴露端口给外面访问,容器内20、80，容器外2000、8080

```shell
docker run -it --name test -p 2000:20 -p 8080:80 -d centos:7.6.1810 /usr/sbin/init
```

容器不间断运行，docker重启容器会自动重启不用人工干预。

做数据持久化存储。本地目录容器目录映射。容器内端口5000 容器外端口5000。

容器内目录：/var/lib/registry

容器外目录：/opt/registry

```shell
docker run -it --name registry -v /opt/registry:/var/lib/registry -p 5000:5000 --restart=always registry
```

退出容器后立即销毁

```shell
docker run -it --rm --name test2 alpine sh
```

进入容器

```shell
docker exec -it 容器名/容器id /bin/bash 
```

### 导入导出镜像

导出镜像

```shell
docker images
# 查看IMAGE ID
docker save bb2b73d4a4a6 > xxx.tar
```

导入镜像

```shell
docker load < xxx.tar
```

修改镜像名

```shell
docker tag bb2b73d4a4a6 xxx/xxx:latest
```

### Docker hub

#### 上传本地镜像

登录

```shell
docker login -u 用户名 ip/域名 -p 密码
```

上传镜像前打标签

```shell
docker tag SOURCE_IMAGE[:TAG] harbor.lhp.com/images/IMAGE[:TAG]
```

上传镜像

```shell
docker push harbor.lhp.com/images/IMAGE[:TAG]
```

下载镜像

```shell
docker pull harbor.lhp.com/images/alpine:latest
```

登录成功信息查看

```shell
cat /root/.docker/config.json
auth后面字段可以用base64进行解码
#加密
echo test|base64
dGVzdAo=
#解密
echo "YWRtaW46SGFyYm9yMTIzNDU=" | base64 -d
```

#### 自动构建镜像

Docker hub创建仓库

- 链接 GitHub
- 配置 Build Rules

| **Source Type** | **Source**     | **Docker Tag** | **Dockerfile location** |
| --------------- | -------------- | -------------- | ----------------------- |
| Branch          | Master         | Latest         | /                       |
| Tag             | /^v([0-9.]+)$/ | {\1}           | /                       |

Tag

Github Tag

- `/^v([0-9.]+)$/` 对应 v1.0 或 v1.0.0
- `/^([0-9.]+)$/` 对应 1.0 或 1.0.0
- `/^([0-9]+)$/` 对应 20210101

Docker Tag

- `{sourceref}` 对应 Github Tag
- `{\1}` 对应 Github Tag 去除 v

Github push 

- 正常 commit 并 push 到 GitHub
- Docker Hub 自动构建 master 分支为 latest 镜像
- 本地 tag commit 并 push tag
- Docker Hub 自动构建 tag 标签为 tag名 镜像

创建标签

```shell
git log
# 复制 commit ID
git tag -a v1.0.0 commitID -m "v1.0.0"
# 本地 tag 版本号标签
git push origin v1.0.0 
# 提交本地标签到 GitHub
```

删除标签

```shell
git tag -d 标签名
git push origin :refs/tags/标签名
```

### Docker安装

#### Docker官方脚本安装

下载一键安装脚本

```shell
curl -fsSL https://get.docker.com -o get-docker.sh
```

运行一键安装脚本(阿里云加速镜像)

```shell
sudo sh get-docker.sh --mirror Aliyun
```

#### Docker官方手动升级

升级yum

```shell
yum update
```

安装依赖包

```shell
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

配置仓库(国内用户推荐使用下面阿里云加速镜像)

```shell
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

配置仓库(阿里云加速镜像)

```shell
sudo yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

排序查看仓库内文件

```shell
yum list docker-ce --showduplicates | sort -r
```

安装最新版Docker

```shell
sudo yum install docker-ce docker-ce-cli containerd.io
```

### Docker启动命令

启动Docker

```shell
sudo systemctl start docker
或
sudo /etc/init.d/docker restart
```

查看Docker版本

```shell
sudo docker version
```

设置Docker开机自动启动

```shell
sudo systemctl enable docker
```

### Docker-compose 安装

1. 官方安装 - 速度慢,有可能被DNS污染导致失败.
2. 手动安装 - 手动下载,离线安装.
3. PIP 在线安装 - 使用 Python 的 pip 包管理工具在线安装.

#### 官方安装

官网选择版本 https://github.com/docker/compose/releases
**以下命令手动修改版本号,例如`v2.2.3`**

```shell
curl -L https://github.com/docker/compose/releases/download/v2.2.3/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```

执行权限

```shell
chmod +x /usr/local/bin/docker-compose
```

检查 docker compose 版本

```shell
docker-compose version
```

#### 手动安装

官网选择版本 https://github.com/docker/compose/releases
选择相应版本,下载到本地或者服务器中.
更名为`docker-compose`,并移动到`/usr/local/bin`目录下.

添加执行权限

```shell
chmod +x /usr/local/bin/docker-compose
```

检查 docker compose 版本

```shell
docker-compose version
```

#### pip在线安装

安装依赖

```shell
yum -y install epel-release
```

安装 PIP

```shell
yum -y install python-pip
```

升级 PIP

```shell
pip install --upgrade pip
```

升级报错

You are using pip version 8.1.2, however version 22.0.3 is available

解决

```shell
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py >> get-pip.py
python get-pip.py
```

3.6版本的get-pip.py

```shell
curl https://bootstrap.pypa.io/get-pip.py >> get-pip.py
python get-pip.py
```

检查 PIP 版本

```shell
pip --version
pip 21.3.1 from /usr/local/lib/python3.6/site-packages/pip (python 3.6)
```

安装 docker compose。

```shell
pip install -U docker-compose
```

检查 docker compose 版本

```shell
docker-compose version

docker-compose version 1.29.2, build unknown
docker-py version: 5.0.3
CPython version: 3.6.8
OpenSSL version: OpenSSL 1.0.2k-fips  26 Jan 2017
```

### Docker配置镜像加速及私有仓库

配置保存命令

配置`daemon.json`需要重载并重启生效

编辑 daemon.json

```shell
vi /etc/docker/daemon.json
```

重新加载 daemon.json

```shell
sudo systemctl daemon-reload
```

重启 Docker

```shell
sudo systemctl restart docker
```

### 配置镜像加速

公共镜像加速

添加`Docker 中国官方镜像加速`

```json
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```

更多公共镜像加速服务

```json
{
  "registry-mirrors": [
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com",
    "https://registry.docker-cn.com",
    "https://reg-mirror.qiniu.com",
    "https://dockerhub.azk8s.cn",
    "https://docker.mirrors.ustc.edu.cn"
  ]
}
```

### 私有镜像加速

可使用`IP`或`反向代理域名`来配置私有镜像加速服务.
请注意镜像加速的 HTTP/HTTPS 协议,以及开放防火墙端口.

私有域名镜像加速

```shell
{
    "registry-mirrors": [
      "https://registry.yourdomain.com"
    ]
}
```

私有IP镜像加速

```shell
{
    "registry-mirrors": [
      "http://101.102.103.104:5000"
    ]
}
```

### 配置私有仓库

私有仓库可以使用`内网 IP`和`公网 IP`的形式配置.

使用反向代理配置了域名的私有仓库无需配置`daemon.json`.

内网IP

```shell
{
    "insecure-registries": [
      "192.168.1.5:5000"
    ]
}
```

公网IP

```shell
{
    "insecure-registries": [
      "101.102.103.104:5000"
    ]
}
```

同时配置镜像加速和私有仓库

```shell
{
    "registry-mirrors": ["https://registry.yourdomain.com"],
    "insecure-registries": ["192.168.1.5:5000"]
}
```

更多的镜像加速服务和私有仓库

```json
{
    "registry-mirrors": [
      "https://registry.yourdomain.com",
      "https://hub-mirror.c.163.com",
      "https://mirror.baidubce.com"
    ],
    "insecure-registries": [
      "192.168.1.5:5000",
      "101.102.103.104:5000"
    ]
}
```

## Docker迁移/var/lib/docker目录

### 前言

由于`/var/lib/docker/overlay2`目录占用磁盘空间过大,即使使用`docker system prune -a`命令也无法清理`overlay2`目录下的文件,手动删除则会导致容器无法启动.所以只有将 docker 根目录迁移至空间更大的目录.

#### 迁移流程

停止docker服务

```shell
systemctl stop docker
```

#### 迁移目录

创建新的 docker 根目录,确保创建的目录空间容量够大.

```shell
mkdir -p /home/docker/lib
```

迁移`/var/lib/docker`目录至`/home/docker/lib`

```shell
rsync -avz /var/lib/docker /home/docker/lib/
```

配置`devicemapper.conf`,先查看该文件是否存在.如不存在则新建.

```shell
mkdir -p /etc/systemd/system/docker.service.d/
vi /etc/systemd/system/docker.service.d/devicemapper.conf
```

写入以下代码保存:

```shell
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd  --graph=/home/docker/lib/docker
```

重加载 docker

```shell
systemctl daemon-reload
systemctl restart docker
systemctl enable docker
```

#### 检查

执行以下命令检查

```shell
docker info
```

确认显示信息中 Docker Root Dir 路径正确

```json
 Docker Root Dir: /home/docker/lib/docker
 Debug Mode: false
 Registry: https://index.docker.io/v1/
```

#### 完成

再次启动容器,检查无误后可删除原路径`/var/lib/docker/`中的文件.

### Docker 私有镜像仓库

#### 安装服务端

**本文以部署至公网,开启账号密码,并配置域名反向代理为例.**
命令参数过多,为方便配置参数,建议使用`docker compose`部署.

创建htpasswd账号密码

启动一个一次性容器用于创建账号密码.密码文件路径以`/root/registry/htpasswd`为例,账号密码以`admin`和`12345678`为例.

```shell
docker run --rm --entrypoint \
    htpasswd httpd:2 -Bbn \
    admin 12345678 > /root/registry/htpasswd
```

docker-compose.yml 

**volumes** 挂载`htpasswd`密码文件,数据目录,时区文件.配置文件`config.yml`作为高级用户可选挂载.
**environment** 环境变量开启认证,并开启删除镜像功能.

```shell
version: "3"
services:
  registry:
    image: registry:2
    container_name: registry
    volumes:
      # - ./config.yml:/etc/docker/registry/config.yml
      - ./htpasswd:/auth/htpasswd
      - ./registry:/var/lib/registry
      - /etc/localtime:/etc/localtime
    ports:
      - 5000:5000
    environment:
      - REGISTRY_AUTH=htpasswd
      - REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd
      - REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm
      - REGISTRY_STORAGE_DELETE_ENABLED=true
    networks:
      - registry
    restart: always

networks:
  registry:
```

启动

```shell
docker-compose up -d
```

成功启动后私有镜像仓库内网地址为: **192.168.1.5:5000**
可根据需求使用域名配置反向代理,例如: **https://registry.yourdomain.com**

注意需开放防火墙 5000 端口

### 配置客户端

以`CentOS`为例,创建或修改`/etc/docker/daemon.json`文件.

**注意,如使用域名配置反向代理并开启`HTTPS`,则无需配`daemon.json`文件.**

```json
{
    "insecure-registries": [
      "192.168.1.5:5000"
    ]
}
```

或

```json
{
    "insecure-registries": [
      "101.102.103.104:5000"
    ]
}
```

### 使用

#### 登录

```shell
docker login registry.yourdomain.com
docker login 192.168.1.5:5000
docker login 101.102.103.104:5000
# 使用上文创建的账号密码 admin 12345678 登录
```

#### 登出

```shell
docker logout registry.yourdomain.com
docker logout 192.168.1.5:5000
docker logout 101.102.103.104:5000
```

#### Push

将现有镜像 tag 为私有仓库镜像名

```shell
docker images
# 获取现有镜像的 IMAGE ID
docker tag 102816b1ee7d registry.yourdomain.com/mysql:8.0.13
docker tag 102816b1ee7d 192.168.1.5:5000/mysql:8.0.13
docker tag 102816b1ee7d 101.102.103.104:5000/mysql:8.0.13
```

Push 至私有镜像仓库

```shell
docker push registry.yourdomain.com/mysql:8.0.13
docker push 192.168.1.5:5000/mysql:8.0.13
docker push 101.102.103.104:5000/mysql:8.0.13
```

#### Pull

```shell
docker pull registry.yourdomain.com/mysql:8.0.13
docker pull 192.168.1.5:5000/mysql:8.0.13
docker pull 101.102.103.104:5000/mysql:8.0.13
```

#### 查看镜像仓库清单

```shell
curl -u admin:12345678 -X GET https://registry.yourdomain.com/v2/_catalog
```

#### 查看镜像 tag 清单

```shell
curl -u admin:12345678 -X GET https://registry.yourdomain.com/v2/mysql/tags/list
```

#### 删除镜像

```
docker-compose.yml`环境变量中开启`REGISTRY_STORAGE_DELETE_ENABLED=true
```

##### 获取镜像 digest hash

```shell
curl -u admin:12345678 --header "Accept: application/vnd.docker.distribution.manifest.v2+json" -I -X GET https://registry.yourdomain.com/v2/mysql/manifests/8.0.13
# 获取 digest hash 如下
sha256:45a2a291xxx223123fc03d9be551e362b460exxs56787736919baa
```

##### 删除镜像清单

```shell
curl -u admin:12345678 -I -X DELETE https://registry.yourdomain.com/v2/mysql/manifests/sha256:45a2a291xxx223123fc03d9be551e362b460exxs56787736919baa
```

##### 清理磁盘空间

```shell
docker exec registry bin/registry garbage-collect /etc/docker/registry/config.yml
```

##### 手动删除目录

完成上述操作后还可以删除存储目录中的空目录文件,如不删除依旧可以被上述`查看镜像仓库`的命令查询到结果.
**依照上文示例,挂载存储目录路径如下:**

./registry/docker/registry/v2/repositories


