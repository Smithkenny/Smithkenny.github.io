# 局域网中其他主机访问本机wsl2中的服务

## 前言

由于我使用的ubuntu20.04中docker所启动的服务除了本机能访问，局域网中其他设备都无法访问。容器端口和windows本机端口不相通，需要windows开启端口转发。网上找了好多资料发现有位github用户开发的脚本简单易用并且能完美解决端口转发问题。

### windows上使用

[链接](https://github.com/HobaiRiku/wsl2-auto-portproxy/releases/download/v1.0.0/wslpp.exe)

下载wslpp.exe即可使用，默认所有IP都能访问本机的docker生成的服务端口，并且不用配置。

### linux中使用

环境

wsl中必须安装net-tools工具

```bash
sudo apt-get install net-tools
```

在Windows PowerShell中, 将其clone到本地:

```bash
git clone https://github.com/HobaiRiku/wsl2-auto-portproxy.git
```

在WSL2中, 编译此项目,注意,是需要Go的环境的, 而且这一步骤需要在Windows的管理员模式进行

```bash
sudo apt install golang-go
cd /mnt/c/Users/`windows-user-name`/wsl2-auto-portproxy/
make build
```

配置wslpp的配置文件, 在WSL中执行

```bash
mkdir /mnt/c/Users/14408/.wslpp
vim  /mnt/c/Users/14408/.wslpp/config.json

输入:
{
  "onlyPredefined": true,
  "predefined": {
    "tcp": [
      "22:22"
    ]
  },
  "ignore": {
    "tcp": [
      445
    ]
  }
}:
```

回到Windows PowerShell中, 执行:

```bash
cd .\wsl2-auto-portproxy\dist\
.\wslpp.exe
```

此外, WSL操作Windows的文件,是需要在管理员模式下进行的.


