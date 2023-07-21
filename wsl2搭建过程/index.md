# wsl2搭建全过程


## 什么是 WSL

WSL 的全称叫做：Windows Subsystem for Linux，即「适用于 Linux 的 Windows 子系统」。WSL 的诞生让 Windows 用户（开发人员）按原样运行 GNU/Linux 环境 —— 包括大多数命令行工具、实用工具和应用程序 —— 且不会产生虚拟机开销。

## 用 WSL 可以做什么？

好的，在 Windows 上用 WSL 我们到底能干什么呢？

- 你可以在 Windows 上「安装」你喜欢的 GNU/Linux 发行版
- 你可以直接在 Windows 上运行 `grep`、`awk`、`sed` 等 Linux 原生可执行文件
- 你可以在 Windows 上直接使用 Vim、Emacs 等工具，直接使用 Linux 版本的 Javascript/Node.js、Ruby、Python、C/C++、Rust、Go 等语言进行开发，直接运行 MySQL、Apache 等 Linux 原生应用和服务等

最为重要的是，利用 WSL 我们可以直接在 **不抛弃 Windows 全部优秀工具的前提下**，**在没有因为虚拟机开销而牺牲太多性能的情况下**，直接运行使用完整的 Linux 系统。

## WSL 2 中采用的新措施

- 在 Windows 上实现完整的 Linux 原生系统调用（Full System Call Compatibility）
- 用 WSL 执行曾经 1.0 版本无法运行的 Linux 原生程序，比如依赖虚拟化技术的 Docker 容器。
- 极大的突破了 WSL 1.0 中出现的 I/O 性能瓶颈：WSL 2 中，`git clone`、`npm install`、`apt update`、`apt upgrade` 等依赖文件系统性能的操作和命令的速度都有显著提高。
- 如果你在 WSL 2 中运行 Web 服务器，你会发现映射到 Windows 中的端口并不一样。不过这一不同并无大碍。
- 你会发现 WSL 2 中在 Windows 侧和 Linux 侧之间相互移动文件的速度会比 WSL 的 1.0 版本慢。因此，为了尽可能保证我们 WSL 文件系统的存储速度，**我们应该将在 Linux 中使用的文件全部放在 Linux 文件系统下**。
- WSL 2 目前对硬件的支持有限，因此你可能无法直接通过 WSL 2 访问外设。不过，WSL 2 目前已经支持了 GPU 的连接,同时，我们也可以通过一些 hacky 的手段让 WSL 2 能够访问 USB 接口.
- WSL 2 对 VMware 等虚拟机软件也有一些要求，也就是：你无法在运行 WSL 2 的情况下直接运行 VMware 或旧版本的 Virtual Box。这是因为 WSL 2 中的虚拟化技术依赖于 Hyper-V，而 VMware 和 6.0 版本之前的 Virtual Box 都和 Hyper-V 不兼容.

## 安装

只有 Windows 10 才能安装使用 WSL。Windows 7、8 或之前的任何版本都无法使用，请及时将系统版本更新至最新。

只有 Windows 10 版本 16215 或以后的版本才能够正常运行 WSL。你可以在「Windows 设置 > 系统 > 关于」处找到你的 Windows 10 操作系统版本。

**只有 Windows 10 版本 18362 或 18363 以及以后的版本，或小版本号为 1049 的版本，才能够正常运行 WSL 2**。需要明确，WSL 2 目前只能在 Windows 10 版本 1903、1909 和 2004 中使用（其中 1903 和 1909 仅支持 **x64 系统**），因此你需要将自己的 Windows 系统进行升级至合适的版本，才能使用正确的 Windows 10 版本安装 WSL 2。

### 开启「适用于 Linux 的 Windows 子系统」的附加功能

无论使用 WSL 1 还是 WSL 2，我们都需要开启「适用于 Linux 的 Windows 子系统」的附加功能：

- 以管理员身份打开 PowerShell 终端
- 运行下面的命令：

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

- 按照提示重启电脑

### 官方版本 WSL

微软官方支持的 WSL Linux 发行版可以直接从微软商店下载。

官方支持的 Linux 发行版有：

- Ubuntu: [最新版](https://www.microsoft.com/store/productId/9NBLGGH4MSV6)，[16.04 LTS](https://www.microsoft.com/store/productId/9PJN388HP8C9)，[18.04 LTS](https://www.microsoft.com/store/productId/9N9TNGVNDL3Q)，和 [20.04 LTS](https://www.microsoft.com/p/ubuntu-2004-lts/9n6svws3rx71)
- OpenSUSE Leap: [42](https://www.microsoft.com/store/productId/9NJVJTS82TJX), [15.0](https://www.microsoft.com/store/productId/9N1TB6FPVJ8C)
- SUSE Linux Enterprise Server: [12](https://www.microsoft.com/store/productId/9P32MWBH6CNS), [15](https://www.microsoft.com/store/productId/9PMW35D7FNLX)
- [Debian GNU/Linux](https://www.microsoft.com/store/productId/9MSVKQC78PK6)
- [Kali Linux](https://www.microsoft.com/store/productId/9PKR34TNCV07)
- [Pengwin](https://www.microsoft.com/store/productId/9NV1GV1PXZ6P) - [GitHub | WhitewaterFoundry/Pengwin](https://github.com/WhitewaterFoundry/Pengwin)付费
- [Alpine WSL](https://www.microsoft.com/store/productId/9P804CRF0395) - [GitHub | agowa338/WSL-DistroLauncher-Alpine](https://github.com/agowa338/WSL-DistroLauncher-Alpine)
- [Fedora Remix for WSL](https://www.microsoft.com/en-us/p/fedora-remix-for-wsl/9n6gdm4k2hnc) - [GitHub | WhitewaterFoundry/Fedora-Remix-for-WSL](https://github.com/WhitewaterFoundry/Fedora-Remix-for-WSL) 付费

### 社区支持的 WSL

另外也有社区支持的 WSL Linux 发行版，社区支持的第三方 WSL 发行版：

- [Arch Linux](https://github.com/yuk7/ArchWSL)
- [Alpine Linux](https://github.com/yuk7/AlpineWSL)

第三方支持的发行版需要按照其官方使用说明进行安装。

下载成功后，我们需要对刚刚这一发行版进行初始化。在开始菜单中，没有意外的话，你应该看到刚刚下载完成的 Ubuntu 快捷方式，我们点击打开 Ubuntu，等待初始化完成即可。（初始化过程会将下载好的 Linux 发行版的文件解压缩，并存储在电脑上供你使用，往往会需要 1 分钟甚至更多。）

初始化完成之后，Linux 会提示你输入一个 UNIX 用户名并为之设置一个 UNIX 密码。这一用户名和密码与你的 Windows 登录账号密码完全无关，你可以任意设置。完成之后，你的 Ubuntu 发行版就安装成功了。

## WSL 2 的安装

WSL 2 需要在 WSL 已经安装完毕的基础之上才能进行安装。

### 开启支持 WSL 2 的可选组件

如果你希望安装 WSL 2，你需要确保你已安装「适用于 Linux 的 Windows 子系统」和「虚拟机平台」这两个可选组件。[[1\]](https://dowww.spencerwoo.com/1-preparations/1-1-installation.html#fn1)同样：

- 以管理员身份打开 PowerShell 终端
- 运行下面的命令：

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

- 根据提示重启电脑

### 使用命令行设置需要由 WSL 2 支持的发行版

同样，用管理员身份打开 PowerShell 终端：

- 执行下面命令找到你当前安装的 Linux 发行版及其名字：

    ```powershell
    wsl --list
    ```

- 之后，执行下面的命令将你需要的发行版设置为由 WSL 2 支持，将其中的 `<Distro>` 修改为 Linux 发行版名称：

    ```powershell
    wsl --set-version <Distro> 2
    ```

- 另外，你也可以通过下面的命令将之后新安装的发行版均设置为默认由 WSL 2 支持：

    ```powershell
    wsl --set-default-version 2
    ```

- 执行下面的命令，如果发现最后一列的 `VERSION` 数为 2，说明你的配置是成功的：

    ```powershell
    wsl --list --verbose
    ```

如果查询到的版本还是1，并且提示当前windows需要升级内核。则使用wsl_update_x64.rar工具升级并重启电脑。再次尝试，直到版本为2.

## 安装Fluent Terminal

Fluent Terminal 是目前相对稳定的非 Electron 构建的终端。得益于 UWP 技术，Fluent Terminal 能够同样拥有美丽的亚克力半透明背景，同时也支持 iTerm2 色彩主题。

安装Fluent Terminal之前先安装Scoop.

### 自定义路径安装Scoop

用管理员运行powershell,创建scoop环境变量.

```bash
env:SCOOP='D:\Applications\Scoop'
[Environment]::SetEnvironmentVariable('SCOOP',$env:SCOOP,'User')

env:SCOOP_GLOBAL='D:\GlobalScoopApps'
[Environment]::SetEnvironmentVariable('SCOOP_GLOBAL',$env:SCOOP_GLOBAL,'User')
```

windows环境变量查看方式：

设置-系统设置-高级系统设置-环境变量

![环境变量](/postimages/环境变量.webp)

#### 安装Scoop(包管理工具)

[Scoop官网](https://scoop.sh/)

[参考](https://sspai.com/post/52496)

需要设置代理才能安装：

暂时先用以下方法设置代理

```bash
使用下面的命令将当前 session（会话）的代理进行配置：
export http_proxy=http://<Windows 主机的 IP 地址>:<代理端口>
export https_proxy=http://<Windows 主机的 IP 地址>:<代理端口>
使用下面的命令取消代理：
unset http_proxy https_proxy
我们可以使用下面的命令检测自己的对外端口：
# 一个接口
curl ipinfo.io
# 另一个接口
curl cip.cc
```

安装命令：

```bash
iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
## 或者
iwr-useb get.scoop.sh|iex
```

卸载命令：

```sh
scoop uninstall scoop
这个卸载，会删除你配置的scoop下面的所有软件，非常危险。
```

安装 Scoop 之后，我们在 PowerShell 中执行下面的命令，即可安装 Fluent Terminal：

```sh
scoop bucket add nonportable
scoop install fluent-terminal-np
```

## Shell 环境

为了加速 Ubuntu 软件包在中国大陆的下载速度，我们将包管理工具 `apt` 源更换至清华 TUNA 软件源镜像。Ubuntu 的软件源配置文件是 `/etc/apt/sources.list`。将系统自带的该文件做个备份，将该文件替换为下面内容，即可使用 TUNA 的软件源镜像。

- 备份原文件：

    ```bash
    sudo cp /etc/apt/sources.list /etc/apt/sources.list.bac
    ```

- 在配置文件 `/etc/apt/sources.list` 最前面添加下面的内容（下面内容为默认 Ubuntu 20.04 LTS 版本的内容）：

    ```bash
    # 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
    deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
    deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
    deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
    deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
    
    # 预发布软件源，不建议启用
    # deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
    ```

- 更新缓存：

    ```bash
    sudo apt update && sudo apt upgrade
    ```

更多内容请参考：[Ubuntu 镜像使用帮助 - 清华大学开源软件镜像站](https://mirror.tuna.tsinghua.edu.cn/help/ubuntu/)

### zsh

### 安装、配置

安装 `zsh` 并将之设置为默认 Shell：

- 利用 apt 安装 `zsh`

    ```bash
    sudo apt install zsh
    ```

下载安装 [oh-my-zsh](https://ohmyz.sh/)，可能是市面上最好的 `zsh` 配置管理工具：

- 运行命令下载安装

    ```bash
    sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
    ```

- 将 `zsh` 作为默认的 Shell 环境（如果刚刚安装脚本没有这样做的话）：

    ```bash
    chsh -s $(which zsh)
    ```

安装zsh时如果报以下错误：

```sh
chsh always asking a password , and get `PAM: Authentication failure`
```

解决方法

```sh
将/etc/pam.d/chsh中
auth       required   pam_shells.so 
改成
auth       sufficient   pam_shells.so 
```

重新进入当前用户并重新安装zsh。

### 插件、主题模板推荐

`zsh` 拥有相当丰富的主题、插件等拓展内容，这里我仅列举一些我常用的配置插件和主题模板：

#### 插件

在.zshrc中设置

![zsh插件](/postimages/zsh插件.webp)

- [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)：为 `zsh` 提供基于输入历史的自动命令提示
- [autojump](https://github.com/wting/autojump)：快速跳转不同的目录、路径、文件夹
- [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)：为 `zsh` 命令提供色彩高亮

退出当前登录用户并重新进，如果发现提示插件需要重新安装。则用以下命令安装。

```sh
git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting
git clone https://github.com/wting/autojump ~/.oh-my-zsh/custom/plugins/autojump
```

autojump代码仓库克隆后需要进入到autojump 手动安装

```sh
安装
cd autojump
./install.py 
卸载
./uninstall.py
```

## 安装git进行版本控制

[参考](https://dowww.spencerwoo.com/2-cli/2-3-cli-tools.html#%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6)

### 使用脚本自动执行代理配置

```sh
WINDOWS_IP=$(ip route | grep default | awk '{print $3}')
PROXY_HTTP="http://${WINDOWS_IP}:7890"
PROXY_SOCKS5="${WINDOWS_IP}:7890"

# Set proxy
set_proxy () {
  export http_proxy="${PROXY_HTTP}"
  export https_proxy="${PROXY_HTTP}"
  proxy_git
}

# Unset proxy
unset_proxy () {
  unset http_proxy
  unset https_proxy
  git config --global --unset http.https://github.com.proxy
}

# Set alias
alias proxy=set_proxy
alias deproxy=unset_proxy
```

实际上是先获取到网关IP，自动设置代理。

### 使用方式

开启代理：proxy

关闭代理：deproxy

IP检测：

```sh
我们可以使用下面的命令检测自己的对外端口：
# 一个接口
curl ipinfo.io
# 另一个接口
curl cip.cc
```

## 安装 Python

在 WSL 环境中安装 Python：

- 使用 APT 安装最新版本的 Python 3：

    ```bash
    # 安装最新版本的 Python 3
    sudo apt install python-is-python3
    ```

- 安装 Python 包管理 `pip` 工具：

    ```bash
    sudo apt install python3-pip
    ```

- 更新 `pip` 包管理源为清华大学 TUNA 镜像源：[[1\]](https://dowww.spencerwoo.com/3-vscode/3-3-python.html#fn1)

    ```bash
    # 使用 TUNA 镜像源更新 pip
    pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pip -U
    
    # 将 pip 源设置为 TUNA
    pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
    ```

## 安装代码优化工具

在上面安装完成 VS Code 的 Python 插件之后，如果顺利，那么 VS Code 应该提示你缺少 Python 代码检查工具（Python linter），如果你尝试格式化文档，那么 VS Code 还会提示你缺少 Python 代码格式化工具（Python code formatter）。我们需要手动安装这些组件。

- 安装 Python 代码检查工具，VS Code 支持 Pylint（默认）、Flake8、mypy 等多个 Python linter，我更推荐大家使用 [Flake8](https://flake8.pycqa.org/en/latest/)：

    ```bash
    pip install flake8
    ```

- 安装 Python 格式化代码工具，VS Code 支持 autopep8（默认）、yapf 和 black，我更推荐大家使用 [black](https://black.readthedocs.io/en/stable/) 来格式化代码：

    ```bash
    pip install black
    ```

## 用 Poetry 管理 Python 项目

> Poetry -- Python packaging and dependency management made easy.[[2\]](https://dowww.spencerwoo.com/3-vscode/3-3-python.html#fn2)

熟悉 Python 项目开发的同学可能知道，Python 需要利用虚拟环境工具 `virtualenv` 来创建虚拟环境运行 Python 项目，也需要 `pip` 包管理工具来安装 Python 依赖。使用两个单独的工具管理一个项目可能会出现诸多问题，同时 `requirements.txt` 的管理也相当不优雅。因此我们用 Poetry 作为统一管理 Python 环境和依赖的工具。Poetry 之于 Python 就如 yarn / npm 之于 Node.js、cargo 之于 Rust、composer 之于 PHP……

### 安装 Poetry

在 WSL 中安装 Poetry：

```bash
curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python
```

将 Tab 补全加入我们所使用的 Shell 环境，比如 zsh 中：

```bash
poetry completions zsh > ~/.zfunc/_poetry
```

### 基本使用

我们可以用 Poetry 直接新建一个模板 Python 项目，比如叫做 `poetry-demo`：

```bash
poetry new poetry-demo
```

这一命令会在 `poetry-demo` 目录下生成如下的文件内容：

```
poetry-demo
├── pyproject.toml
├── README.rst
├── poetry_demo
│   └── __init__.py
└── tests
    ├── __init__.py
    └── test_poetry_demo.py
```

其中的 `pyproject.toml` 文件就是定义我们当前 Python 项目所使用依赖库的文件。

我们可以用下面的命令安装一个新的 Python 依赖：

```bash
# 安装一个依赖
poetry add requests

# 安装一个仅在开发中使用的依赖（比如格式化工具 black）
poetry add black --dev # 或 -D
```

对于一个已定义 `pyproject.toml` 的项目，我们可以用下面的命令安装所有依赖：

```bash
# 安装所有依赖
poetry install

# 安装除了仅限开发使用的依赖外的所有依赖库
poetry install --no-dev
```

使用 remove 关键词来移除某个依赖：

```bash
poetry remove requests
```

> 更多 Poetry 命令行工具的使用，推荐大家参考 Poetry 官方文档：[Poetry - Commands](https://python-poetry.org/docs/cli/).

## Node.js

版本更新

在开始之前，我先来介绍几个 Node.js 相关的概念：

- Node.js - A JavaScript runtime built on Chrome's V8 JavaScript engine 是一个不依赖浏览器的 JavaScript 运行环境，大部分前端项目比如 Vue、React 和后端项目比如 Express、Koa 均依赖于 Node.js 生态系统；
- `n` - Interactively Manage Your Node.js Versions：是一个 Node.js 版本管理工具，我们可以使用 `n` 来安装不同版本的 Node.js 环境；
- npm 和 yarn，分别是 Node.js 的包管理工具，其中我更推荐大家使用后者（yarn）来管理安装 Node.js 依赖；

接下来，我们将在 WSL 中使用 `n` 来安装 Node.js 环境，并配置包管理工具 yarn 来管理 Node.js 环境中的依赖。

## 安装 Node.js

建议大家使用 [`n`](https://github.com/tj/n) 来管理与安装 Node.js，便于切换版本和快捷安装。首先，我们使用 [n-install](https://github.com/mklement0/n-install) 安装 `n`：

```bash
curl -L https://git.io/n-install | bash
```

n-install（也就是上面的命令）默认会自动帮我们将 `n` 的重要环境变量 `PREFIX` 和 `N_PREFIX` 设置到 `$HOME/n`，并将 `n` 安装到 `$HOME/n/bin`。同时，n-install 会帮助我们更新当前我们所使用的 Shell（比如 zsh），在相应的配置文件中将 `$HOME/n/bin` 添加到 `PATH` 中。最后，n-install 会帮我们安装最新的 LTS 版本的 Node.js 环境。

> 更多关于 n-install 的使用，请参考 [n-install 的 README 文档](https://github.com/mklement0/n-install)。

使用 n-install 安装好 `n` 以及 Node.js 之后，我们可以通过下面的命令进行验证：

```bash
# 验证 n 的安装情况
n --version
6.7.0

# 验证 Node.js 的安装情况
node --version
v12.18.3

# 验证 npm 的安装情况
npm --version
6.14.6
```

另外，我们也可以用 `n` 下载其他版本的 Node.js：

```bash
# 下载最新的 LTS 版本 Node.js
n lts

# 下载特定版本的 Node.js
n 10.16.0
```

直接运行命令 `n` 也可以进入 interactive 界面，让我们选择使用某个版本的 Node.js。

> 有关使用 `n` 下载安装与使用 Node.js 的命令和方法，请参考 [n 的官方 README 文档](https://github.com/tj/n)。

## 配置 Node.js 包管理工具

接下来，我们安装 [yarn](https://yarnpkg.com/)，推荐大家安装使用 yarn：这个更加现代、科学的 Node.js 包管理工具。刚刚下载的 Node.js 中包含有 npm，因此我们可以直接用 npm 来安装 yarn：

```bash
npm install -g yarn
```

此时我们安装的 yarn 实际上是 1.22.0：

```bash
yarn --version

1.22.4
```

此时我们安装的是 yarn 1.0 版本，也就是比较传统的管理安装 Node.js 依赖的工具，跟 npm 比较类似。yarn 在 2.0+ 版本中经历了重大的变化，因此如果我们需要对某个项目开启 yarn 的 2.0 版本，需要使用下面的命令手动开启：

```bash
yarn set version berry
```

其中，yarn 2.0 所支持的 Plug and play - Plug'n'Play 功能是 yarn 2.0 的重磅功能，也是我们使用 yarn 2.0 的主要原因，Plug'n'Play 重点解决了 Node.js 的 `node_modules` 存在于每个项目中的问题，更多内容请见：[yarn - Plug'n'Play](https://yarnpkg.com/features/pnp)。需要注意的是，并非不是所有的 Node.js 库均支持 yarn 2.0，因此推荐预先查看你所使用的库是否支持 yarn 2.0：[Compatibility Table](https://yarnpkg.com/features/pnp#compatibility-table)。

> 如果你之前使用 yarn 1.0 版本，推荐查看 yarn 官方的 1.0 至 2.0 版本迁移指南：[Migrating from Yarn 1](https://yarnpkg.com/advanced/migration)。

有关在 VS Code 中开发 Node.js 项目的方法，更多请参考：[Node.js tutorial in Visual Studio Code - Visual Studio Code Docs](https://code.visualstudio.com/docs/nodejs/working-with-javascript)

