# 使用浏览器登录linux服务器


## 使用浏览器登录linux服务器

### 项目地址

```
https://github.com/huashengdun/webssh/releases/tag/v1.6.1
在这里我选在下载的版本是：v1.6.1
```

**上传到服务器上**

### 开始安装

### 创建解压目录

```bash
mkdir -p /tmpdisk/webssh
```

### 解压离线webssh安装包

```bash
cd /tmpdisk/webssh
unzip webssh-1.6.1.zip .
```

### 进入解压目录

```bash
cd webssh-1.6.1
```

### 使用docker-compose一键启动

```bash
docker-compose up 
```

### 安装docker-compose

```bash
wget https://github.com/docker/compose/releases/download/1.24.0/docker-compose-Linux-x86_64
```

### 赋予文件执行权限

```bash
chmod +x docker-compose-Linux-x86_64
```

### 拷贝到目录

```bash
cp docker-compose-Linux-x86_64 /usr/local/bin/docker-compose
```

### 添加软链接

```bash
ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

### 最后查看版本

```sh
docker-compose --version
```

### webssh使用

使用docker-compose 启动后默认将容器内的8888 端口映射到宿主机8888.通过访问宿主机的8888端口，打开webssh页面，登录Linux服务器。


