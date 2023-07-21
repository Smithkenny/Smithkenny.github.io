# ubuntu20.04安装docker


## ubuntu20.04安装docker

### 操作系统要求

- Docker只支持64位版本的 Ubuntu 18.04 LTS、20.04 LTS、21.04、21.10 操作系统，更旧的 Ubuntu 操作系统如 16.04 官方已不再维护；
- Docker Engine 支持 x86_64 (or amd64)、armhf、arm64、s390x 体系架构。

### 卸载旧版本的 Docker Engine

Docker的旧版本被称为docker，docker.io或docker-engine。如果安装了这些，请卸载它们：

```routeros
sudo apt-get remove docker docker-engine docker.io containerd runc
```

如果您想**保留现有数据**，那么**直接进入下一步**即可。
如果您**不想保留现有数据（可选）**，如 image、container、volume等，执行下面的命令，相比remove命令，purge会同时清除软件包和软件的配置文件。

```vim
sudo apt-get purge docker-ce docker-ce-cli containerd.io
```

主机上的 image、container、volume 或自定义配置文件不会自动删除。要删除所有映像、容器和卷，执行下面的命令，删除对应的文件夹：

```awk
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

卸载完成。

### Docker Engine 安装方法

官方提供了三种安装 Docker Engine 的方法：

- 大多数用户设置 Docker 的仓库，使用包管理器安装，以便于安装和升级任务，这是推荐的方法；
- 一些用户下载 deb 包并手动安装，完全手动管理升级。这在诸如在无法访问 Internet 的系统上安装 Docker 等情况下很有用；
- 在测试和开发环境中，一些用户选择使用自动化脚本来安装 Docker。

**本文只介绍 [Docker Engine 官方安装-Ubuntu](https://link.segmentfault.com/?enc=lLD6lBFPEHRPtpZvEneGSQ%3D%3D.4jwlQxH0tZwVG%2B6XAbuKKMQZmLd5XhJi0rdlWSALk%2B4gW5PXb%2BipKzm17sB8jUE6) 中的第一种方法——使用包管理器安装**。

#### 设置 Docker Engine 存储库

① 更新 apt 包索引并安装 ca-certificates、curl、gnupg、lsb-release等，以允许 apt 通过 HTTPS 使用存储库;

```pgsql
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg lsb-release
```

② 添加 Docker 的官方 GPG 密钥;

```awk
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

③ 使用下面命令设置 stable 仓库。

```gradle
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

#### 安装 Dokcer Engine

安装最新版本的 Dokcer Engine：

```vim
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

安装指定版本的 Dokcer Engine，首先使用cpt-cache命令查看仓库中 docker-ce 的版本，选择对应的版本，使用install命令安装。

```routeros
apt-cache madison docker-ce
sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io
```

#### 更新 Dokcer Engine

```routeros
sudo apt-get update
sudo apt-get upgrade
```

### Docker Compose 安装方法

#### Docker Compose 稳定版 1.29.2 安装

下面的步骤来自于 [Docker Compose 官方安装-Ubuntu ](https://link.segmentfault.com/?enc=Gy2ATrLdA0KIIHYJGgkMpQ%3D%3D.rgr4laT9Mz9YRNWFMReqNfCKhl6HG9TV1OInbtJvwaseSCB5zmL3x42m4dHB4Owd)。

目前 Docker Compose 的稳定版为 1.29.2。
运行下面的命令将 Docker Compose 1.29.2 的二进制版本安装到 `/usr/local/bin/docker-compose` 中。
要安装不同版本的 Compose，请将 1.29.2 替换为您要使用的 Compose 版本。

```awk
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

对二进制文件 `/usr/local/bin/docker-compose` 赋予可执行权限。

```awk
sudo chmod +x /usr/local/bin/docker-compose
```

测试是否安装成功，正确输出 compose 版本号，即成功。

```ada
docker-compose --version
```

#### Docker Compose V2 安装

对Docker Compose V2有兴趣，请访问 [Compose V2](https://link.segmentfault.com/?enc=sNT2ZtN6ng80IkgNEUeryw%3D%3D.6uyB92O7HdbkBDhQtlxCoqfuoICOTkwXDKTXsxdThN%2BGQV9fRXHSdq9tb%2BJ9QenQ) ，来进行安装。

