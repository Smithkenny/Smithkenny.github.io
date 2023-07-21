# harbor安装与简单使用


## 前言

Harbor 是一个开源的容器镜像仓库，它提供用户权限管理、镜像复制等功能，提高使用的registry的效率。确保镜像被扫描并且没有漏洞，并将镜像标记为可信。

## harbor架构

![harbor架构图](/postimages/harbor架构图.webp)

主要组件包括proxy，他是一个nginx前端代理，主要是分发前端页面ui访问和镜像上传和下载流量，上图中通过深蓝色先标识；
ui提供了一个web管理页面，当然还包括了一个前端页面和后端API，底层使用mysql数据库。
registry是镜像仓库，负责存储镜像文件，当镜像上传完毕后通过hook通知ui创建repository，上图通过红色线标识，当然registry的token认证也是通过ui组件完成。
adminserver是系统的配置管理中心附带检查存储用量，ui和jobserver启动时候回需要加载adminserver的配置，通过灰色线标识。jobsevice是负责镜像复制工作的，他和registry通信，从一个registry pull镜像然后push到另一个registry，并记录job_log，上图通过紫色线标识。
log是日志汇总组件，通过docker的log-driver把日志汇总到一起，通过浅蓝色线条标识。

## 环境准备

运行环境

- Ubuntu  20.04 LTS
- docker-ce：20.10.12
- docker-compose：v2.2.3
- harbor-offline：v1.10.10

## 安装docker

官方安装[参考](https://docs.docker.com/engine/install/ubuntu/)

我是在wsl2中使用ubuntu 20.04系统安装docker的。

#### 删除旧版docker

```bash
sudo apt-get remove docker docker-engine docker.io containerd runc
```

如果之前安装过docker,那么所有配置文件都在`/var/lib/docker/`中,包括images, containers, volumes, and networks。

不用可以删除。

#### 开始安装设置repo源

```bash
sudo apt-get update

sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

#### 添加Docker的官方GPG key

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

#### 设置稳定版repo源

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

#### 安装docker-ce

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

#### 测试安装是否成功

```bash
sudo docker run hello-world
```

#### 安装docker-compose

harbor需要用docker-compose来管理。

源码地址：https://github.com/docker/compose/releases

下载指定版本。

```bash
curl -L https://github.com/docker/compose/releases/download/v2.2.3/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```

添加执行权限。

```bash
chmod +x /usr/local/bin/docker-compose
```

验证一下。

```bash
$docker-compose --version
 
输出：
Docker Compose version v2.2.3
```

## 安装 harbor

源码地址：https://github.com/goharbor/harbor/releases

项目分有在线版，以及离线版，这里使用离线版的安装。

### 下载安装包。

```bash
wget https://github.com/goharbor/harbor/releases/download/v1.10.10/harbor-offline-installer-v1.10.10.tgz
```

或许用迅雷下载，速度更佳。

解压安装包。

```bash
mkdir -p softwere
cd /root/softwere
mv harbor-offline-installer-v1.10.10.tgz /root/softwere
tar xvf harbor-offline-installer-v1.10.10.tgz
ls harbor
LICENSE  common  common.sh  docker-compose.yml  harbor.v1.10.10.tar.gz  harbor.yml  install.sh  prepare
```

### 更改配置

这里使用域名+5000端口来访问，关闭https。完整配置如下：

vim harbor.yml

```bash
# Configuration file of Harbor

# The IP address or hostname to access admin UI and registry service.
# DO NOT use localhost or 127.0.0.1, because Harbor needs to be accessed by external clients.
hostname: harbor.lhp.com

# http related config
http:
  # port for http, default is 80. If https enabled, this port will redirect to https port
  port: 5000

# https related config
# https:
  # https port for harbor, default is 443
  # port: 443
  # The path of cert and key files for nginx
  # certificate: /your/certificate/path
  # private_key: /your/private/key/path

# Uncomment external_url if you want to enable external proxy
# And when it enabled the hostname will no longer used
# external_url: https://reg.mydomain.com:8433

# The initial password of Harbor admin
# It only works in first time to install harbor
# Remember Change the admin password from UI after launching Harbor.
harbor_admin_password: Toor12345

# Harbor DB configuration
database:
  # The password for the root user of Harbor DB. Change this before any production use.
  password: root123
  # The maximum number of connections in the idle connection pool. If it <=0, no idle connections are retained.
  max_idle_conns: 50
  # The maximum number of open connections to the database. If it <= 0, then there is no limit on the number of open connections.
  # Note: the default number of connections is 100 for postgres.
  max_open_conns: 100

# The default data volume
data_volume: /data

# Harbor Storage settings by default is using /data dir on local filesystem
# Uncomment storage_service setting If you want to using external storage
# storage_service:
#   # ca_bundle is the path to the custom root ca certificate, which will be injected into the truststore
#   # of registry's and chart repository's containers.  This is usually needed when the user hosts a internal storage with self signed certificate.
#   ca_bundle:

#   # storage backend, default is filesystem, options include filesystem, azure, gcs, s3, swift and oss
#   # for more info about this configuration please refer https://docs.docker.com/registry/configuration/
#   filesystem:
#     maxthreads: 100
#   # set disable to true when you want to disable registry redirect
#   redirect:
#     disabled: false

# Clair configuration
clair:
  # The interval of clair updaters, the unit is hour, set to 0 to disable the updaters.
  updaters_interval: 12

jobservice:
  # Maximum number of job workers in job service
  max_job_workers: 10

notification:
  # Maximum retry count for webhook job
  webhook_job_max_retry: 10

chart:
  # Change the value of absolute_url to enabled can enable absolute url in chart
  absolute_url: disabled

# Log configurations
log:
  # options are debug, info, warning, error, fatal
  level: info
  # configs for logs in local storage
  local:
    # Log files are rotated log_rotate_count times before being removed. If count is 0, old versions are removed rather than rotated.
    rotate_count: 50
    # Log files are rotated only if they grow bigger than log_rotate_size bytes. If size is followed by k, the size is assumed to be in
kilobytes.
    # If the M is used, the size is in megabytes, and if G is used, the size is in gigabytes. So size 100, size 100k, size 100M and size 100G
    # are all valid.
    rotate_size: 200M
    # The directory on your host that store log
    location: /var/log/harbor

  # Uncomment following lines to enable external syslog endpoint.
  # external_endpoint:
  #   # protocol used to transmit log to external endpoint, options is tcp or udp
  #   protocol: tcp
  #   # The host of external endpoint
  #   host: localhost
  #   # Port of external endpoint
  #   port: 5140

#This attribute is for migrator to detect the version of the .cfg file, DO NOT MODIFY!
_version: 1.10.0

# Uncomment external_database if using external database.
# external_database:
#   harbor:
#     host: harbor_db_host
#     port: harbor_db_port
#     db_name: harbor_db_name
#     username: harbor_db_username
#     password: harbor_db_password
#     ssl_mode: disable
#     max_idle_conns: 2
#     max_open_conns: 0
#   clair:
#     host: clair_db_host
#     port: clair_db_port
#     db_name: clair_db_name
#     username: clair_db_username
#     password: clair_db_password
#     ssl_mode: disable
#   notary_signer:
#     host: notary_signer_db_host
#     port: notary_signer_db_port
#     db_name: notary_signer_db_name
#     username: notary_signer_db_username
#     password: notary_signer_db_password
#     ssl_mode: disable
#   notary_server:
#     host: notary_server_db_host
#     port: notary_server_db_port
#     db_name: notary_server_db_name
#     username: notary_server_db_username
#     password: notary_server_db_password
#     ssl_mode: disable

# Uncomment external_redis if using external Redis server
# external_redis:
#   host: redis
#   port: 6379
#   password:
#   # db_index 0 is for core, it's unchangeable
#   registry_db_index: 1
#   jobservice_db_index: 2
#   chartmuseum_db_index: 3
#   clair_db_index: 4

# Uncomment uaa for trusting the certificate of uaa instance that is hosted via self-signed cert.
# uaa:
#   ca_file: /path/to/ca

# Global proxy
# Config http proxy for components, e.g. http://my.proxy.com:3128
# Components doesn't need to connect to each others via http proxy.
# Remove component from `components` array if want disable proxy
# for it. If you want use proxy for replication, MUST enable proxy
# for core and jobservice, and set `http_proxy` and `https_proxy`.
# Add domain to the `no_proxy` field, when you want disable proxy
# for some special registry.
proxy:
  http_proxy:
  https_proxy:
  # no_proxy endpoints will appended to 127.0.0.1,localhost,.local,.internal,log,db,redis,nginx,core,portal,postgresql,jobservice,registry,registryctl,clair,chartmuseum,notary-server
  no_proxy:
  components:
    - core
    - jobservice
    - clair
```

### 添加映射关系

vi /etc/hosts

```bash
172.28.238.43   harbor.lhp.com
```

### windows也需要添加映射关系

用管理员用户编辑`C:\Windows\System32\drivers\etc\hosts`

```bash
#同样添加下面内容
172.28.238.43   harbor.lhp.com
```

### 修改后刷新

```bash
ipconfig /flushdns
```

配置daemon.json，docker登录harbor时需要。

vi /etc/docker/daemon.json

```bash
{
  "registry-mirrors": ["https://jliunkl7.mirror.aliyuncs.com"],
  "insecure-registries": ["harbor.lhp.com:5000"]
}
```

### 重启docker

```bash
service docker stop
service docker start
```

### 执行install.sh脚本安装

```bash
./install.sh
```

### 测试访问harbor

http://harbor.lhp.com:5000

用户名：admin

密码：Toor12345

![harbor登录](/postimages/harbor登录.webp)

### docker登录harbor

```bash
docker login -u admin http://harbor.lhp.com:5000
```

### harbor创建项目

![harbor新建项目](/postimages/harbor新建项目.webp)

### 测试上传和下载到harbor

```bash
#push
docker tag alpine:latest harbor.lhp.com:5000/images/alpine:latest
docker push harbor.lhp.com:5000/images/alpine:latest
#pull
docker pull harbor.lhp.com:5000/images/alpine:latest
```

portainer中添加harbor仓库。以后就可以在portainer中可视化上传和下载镜像啦！

## 登录portainer并新增仓库

![portainer添加harbor仓库1](/postimages/portainer添加harbor仓库1.webp)

![portainer添加harbor仓库2](/postimages/portainer添加harbor仓库2.webp)

### 测试上传和下载。

镜像打tag时注意要在镜像前加harbor项目文件夹，文中的为images。

将busybox:latest上传到harbor的images中。

![portainer上传到harbor1](/postimages/portainer上传到harbor1.webp)

进入busybox镜像并打tag，注意格式。

![portainer上传到harbor2](/postimages/portainer上传到harbor2.webp)

上传镜像到harbor仓库

![portainer上传到harbor3](/postimages/portainer上传到harbor3.webp)

harbor中查看

![portainer上传到harbor5](/postimages/portainer上传到harbor5.webp)

harbor上拉取镜像操作

选择要拉取的镜像并拉取。

![harbor仓库镜像拉取1](/postimages/harbor仓库镜像拉取1.webp)

## harbor登录时遇到的坑

如果显示harbor登录失败，而密码又是harbor.yml中设置的密码。此时需要停止harbor所有相关镜像。进入到harbor安装目录。

```bash
cd /root/softwere/harbor
#停止harbor所有相关镜像
docker-compose down -v
#开启harbor所有相关镜像
docker-compose up -d
```

停止docker服务，删除harbor的数据卷,清除日志，路径在`harbor.yml`中。

```bash
rm -rf /data
rm -rf /var/log/harbor
#path
data_volume: /data
location: /var/log/harbor
```

最后删除harbor文件夹，重新解压安装即可。

```bash
rm -rf /root/softwere/harbor
cd /root/softwere/
tar -xf harbor.v1.10.10.tar.gz
```

## 其他功能-Harbor镜像漏洞扫描

安装harbor自带的漏洞扫描

#### 添加扫描模块

```bash
#进入到harbor目录中操作
./prepare --with-clair

prepare base dir is set to /root/softwere/harbor
/usr/src/app/utils/configs.py:100: YAMLLoadWarning: calling yaml.load() without Loader=... is deprecated, as the default Loader is unsafe. Please read https://msg.pyyaml.org/load for full details.
  configs = yaml.load(f)
WARNING:root:WARNING: HTTP protocol is insecure. Harbor will deprecate http protocol in the future. Please make sure to upgrade to httpsClearing the configuration file: /config/db/env
Clearing the configuration file: /config/jobservice/env
Clearing the configuration file: /config/jobservice/config.yml
Clearing the configuration file: /config/nginx/nginx.conf
Clearing the configuration file: /config/registryctl/env
Clearing the configuration file: /config/registryctl/config.yml
Clearing the configuration file: /config/registry/root.crt
Clearing the configuration file: /config/registry/config.yml
Clearing the configuration file: /config/core/env
Clearing the configuration file: /config/core/app.conf
Clearing the configuration file: /config/log/rsyslog_docker.conf
Clearing the configuration file: /config/log/logrotate.conf
Generated configuration file: /config/log/logrotate.conf
Generated configuration file: /config/log/rsyslog_docker.conf
Generated configuration file: /config/nginx/nginx.conf
Generated configuration file: /config/core/env
Generated configuration file: /config/core/app.conf
Generated configuration file: /config/registry/config.yml
Generated configuration file: /config/registryctl/env
Generated configuration file: /config/db/env
Generated configuration file: /config/jobservice/env
Generated configuration file: /config/jobservice/config.yml
loaded secret from file: /secret/keys/secretkey
Generated configuration file: /config/clair/postgres_env
Generated configuration file: /config/clair/config.yaml
Generated configuration file: /config/clair/clair_env
Generated configuration file: /config/clair-adapter/env
Generated configuration file: /compose_location/docker-compose.yml
/usr/src/app/utils/configs.py:90: YAMLLoadWarning: calling yaml.load() without Loader=... is deprecated, as the default Loader is unsafe. Please read https://msg.pyyaml.org/load for full details.
  versions = yaml.load(f)
Clean up the input dir
#会自动生成docker-compose.yml文件
➜  harbor ls
LICENSE  common  common.sh  docker-compose.yml  harbor.v1.10.10.tar.gz  harbor.yml  install.sh  prepare
```

#### 重启启动harbor服务（注意这里要指定docker-compose.yaml文件）

```bash
docker-compose -f docker-compose.yml up -d
```

#### 测试扫描

可以看到在项目里面已经显示安装好了扫描器插件，不安装这里是没有的。

![harbor扫描器1](/postimages/harbor扫描器1.webp)

选择镜像我们就可以进行扫描了。

镜像未扫描前

![harbor扫描器2](/postimages/harbor扫描器2.webp)

镜像扫描后

![harbor扫描器3](/postimages/harbor扫描器3.webp)


