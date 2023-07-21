# 前端框架


## nvm管理node.js

### 环境：

- **centos 7.9**

### nvm 安装

**新创建一个名为`myapp`的桥接网卡**

```bash
cd /root
wget https://github.com/nvm-sh/nvm/archive/refs/tags/v0.39.3.tar.gz
mkdir -p /root/.nvm
tar -zxvf nvm-0.39.3.tar.gz -C /root/.nvm
```

**配置环境**

```bash
vim ~/.bashrc
```

**在~/.bashrc的末尾，添加如下语句：**

```bash
export NVM_DIR="$HOME/.nvm/nvm-0.39.3"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

**启用环境**

```bash
source ~/.bashrc
```

**安装node.js，测试**

```bash
nvm install 8.16.0
```

**切换版本**

```bash
nvm use 8.16.0
```

**查看已安装的版本**

```bash
nvm ls
```


