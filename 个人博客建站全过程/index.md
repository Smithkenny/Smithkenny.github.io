# 个人博客建站全过程


## 技术选型

使用Hexo+keep+Github Actions+jsdelivr这一套技术实现blog搭建及自动化部署

### 开始搭建

#### 安装node

下载地址：[node下载](https://nodejs.org/en) .安装LTS版本。

#### 安装 Hexo 

```sh
npm install hexo-cli -g
```

**我们强烈建议永远安装最新版本的 Hexo，以及 [推荐的 Node.js 版本](https://hexo.io/zh-cn/docs/#安装前提)。**

| Hexo 版本   | 最低兼容 Node.js 版本 |
| :---------- | :-------------------- |
| 5.0+        | 10.13.0               |
| 4.1 - 4.2   | 8.10                  |
| 4.0         | 8.6                   |
| 3.3 - 3.9   | 6.9                   |
| 3.2 - 3.3   | 0.12                  |
| 3.0 - 3.1   | 0.10 or iojs          |
| 0.0.1 - 2.8 | 0.10                  |

#### 初始化博客目录

```sh
hexo init Smithkenny.github.io 
# 这里的Smithkenny换成你自己的英文名
```

#### 初始化完成后，我们就进入我们的目录

```sh
cd Smithkenny.github.io
```

#### 安装

```sh
npm install
```

#### clean一下，然后生成静态页面

```sh
hexo clean
hexo g
```

#### 把你的网站运行起来

```sh
hexo s
```

打开你的浏览器，输入 `localhost:4000` 。

自此，你的个人网站就这么速度的搭建起来了！

## 主题选择

初始化博客后，指定文件夹的目录如下：

```
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

**_config.yml**

网站的 [配置](https://hexo.io/zh-cn/docs/configuration) 信息，您可以在此配置大部分的参数。

我的配置如下：

```sh
# Site
title: Peng's Blog
subtitle: Peng 的个人博客
description: ''
keywords:
author: Haipeng
language: zh-CN
timezone: Asia/Shanghai

# URL
## Set your site url here. For example, if you use GitHub Page, set url as 'https://username.github.io/project'
url: https://www.haipengv.com
```

**package.json**

应用程序的信息。[EJS](https://ejs.co/), [Stylus](http://learnboost.github.io/stylus/) 和 [Markdown](http://daringfireball.net/projects/markdown/) renderer 已默认安装，您可以自由移除。

**scaffolds**

[模版](https://hexo.io/zh-cn/docs/writing) 文件夹。当您新建文章时，Hexo 会根据 scaffold 来建立文件。

Hexo的模板是指在新建的文章文件中默认填充的内容。例如，如果您修改scaffold/post.md中的Front-matter内容，那么每次新建一篇文章时都会包含这个修改。

**source**

资源文件夹是存放用户资源的地方。除 `_posts` 文件夹之外，开头命名为 `_` (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到 `public` 文件夹，而其他文件会被拷贝过去。

**themes**

[主题](https://hexo.io/zh-cn/docs/themes) 文件夹。Hexo 会根据主题来生成静态页面。

### **选择一个主题**

这里可以选择你要的[主题](https://hexo.io/themes/)，我选择的是、keep主题，因为它很简洁。

#### **下载主题**

keep主题github地址：https://github.com/XPoet/hexo-theme-keep

`keep `主题官方文档：https://keep-docs.xpoet.cn/

```sh
# 这里使用npm方式安装方便后期进行代码托管维护
npm install hexo-theme-keep
```

#### **配置主题**

- 主题下载完之后，在你根目录下的 _config.yml 文件中，修改 theme 为你的主题名字

  ```sh
  theme: keep
  ```

我这里选择自定义`hexo-theme-keep`主题，可以参考keep主题[平滑升级](https://keep-docs.xpoet.cn/usage-tutorial/advanced.html#%E5%B9%B3%E6%BB%91%E5%8D%87%E7%BA%A7)

在博客根目录下的 `source`目录中新建文件夹`_data`

将`node_modules`下的`hexo-theme-keep`目录中复制出`_config.yml`文件到`source/_data/`中，并重命名为`keep.yml`。

在`keep.yml`可根据文档配置对应的属性。

#### **重新生成和运行**

```sh
hexo clean
hexo g
hexo s
```

**keep主题**

![keep主题](/postimages/keep主题.webp)

## 博客文章创建

有两种方式来新建你的博文

### **命令形式**

在你的blog目录下使用如下命令：

```sh
hexo new hello # 这里的article写上你的文章的名称
```

你的source/_posts下就会生成一个 `hello.md`文件，在这个文件下就可以写上你的博客内容了。用 Markdown 的语法去写。

### **直接新建方式**

直接点的方式就是直接在`source/_posts`新建一个 Markdown 文件。

#### **打上标签**

打标签能让你的文章方便检索。

打开标签功能：

```sh
hexo new page tags
```

这时候你的source/下生成 tags/index.md 文件，我们将其打开，然后把它改成：

```bash
type: "tags" comments: false
```

这时候你要为你的文章打上标签就可以在文章的头部写上

```bash
---
title: Java 安装与使用
date: 2021-12-28 15:36:27
tags: Java
tags: <<其他你想打的标签>>
---
```

#### **添加分类**

分类，归档，是你博客的特性之一。

打开分类功能：

```bash
hexo new page categories
```

这说你的source目录下生成 categories/index.md 文件，我们将其打开，把它改成：

```bash
type: "categories"
comments: false
```

这时候你就可以给你的文章归类存档了，使用方式就是在你的文章的头部加上

```sh
---
title: Java 安装与使用
date: 2021-12-28 15:36:27
tags: Java
categories: Java
categories: <<其他你想分的类>>
---
```

注意：标签和分类要确定你的配置文件 _config.yml 是否有打开了 tag_dir: tags 和 category_dir: categories。另外，Markdown 的语法是写作最优雅最简洁最简单的，如果之前没用过的建议去学一下[Markdown 语法说明](https://www.appinn.com/markdown/)，一般一个钟左右就能掌握。因为它和HTML那样简单。

#### **添加评论系统**

keep主题提供评论系统，详情见[keep comment](https://keep-docs.xpoet.cn/usage-tutorial/configuration-guide.html#comment)

这里先暂时不开启评论了。因为发现创建完之后还是无法评论。

#### **部署到线上**

可以通过`GitHub Pages`来创建免费的线上访问地址。

## 安装 Git

- Windows：下载并安装 [git](https://git-scm.com/download/win).
- Mac：使用 [Homebrew](http://mxcl.github.com/homebrew/), [MacPorts](http://www.macports.org/) 或者下载 [安装程序](http://sourceforge.net/projects/git-osx-installer/)。
- Linux (Ubuntu, Debian)：`sudo apt-get install git-core`
- Linux (Fedora, Red Hat, CentOS)：`sudo yum install git-core`

> Mac 用户
>
> 如果在编译时可能会遇到问题，请先到 App Store 安装 Xcode，Xcode 完成后，启动并进入 **Preferences -> Download -> Command Line Tools -> Install** 安装命令行工具。

> Windows 用户
>
> 对于中国大陆地区用户，可以前往 [淘宝 Git for Windows 镜像](https://npm.taobao.org/mirrors/git-for-windows/) 下载 git 安装包。

## 创建Github pages 仓库

创建一个`xxx.github.io`的 public 仓库，这里的xxx写你的名字，比如我写的是 [Smithkenny.github.io](https://Smithkenny.github.io),那么到时我就可以通过 `Smithkenny.github.io` 来访问我的网站了。 创建完成之后，那么你就有自己的 Git 地址了。

安装hexo-deployer-git

```sh
cd Smithkenny.github.io
npm install hexo-deployer-git --save
```

### 配置你的Git

打开你的配置文件`_config.yml`：

```sh
deploy:
  type: git
  repo: https://github.com/xxx/xxx.github.io.git // 输入你的git地址
```

github地址查看：

![public-地址](/postimages/public-地址.webp)

### 推送你的网站到Github上

```sh
hexo d
# d 就是 deploy，部署上去的意思。
```

### 访问你的网站

直接在浏览器输入你的 `xxx.github.io` 就可以访问了。

#### **绑定自己的域名**

##### 添加 CNAME 文件

```sh
# 进入sources
cd sources
# 创建CNAME文件，写入自己的域名，如：www.haipengv.com
touch CNAME
```

##### 在你的域名商后台进行 DNS 解析添加两条记录

```bash
主机记录： @
  记录类型：A
  记录值：ip1 或者 ip2
主机记录： www
  记录类型： CNAME
  记录值： Smithkenny.github.io  (这里就是你的github仓库名称)
```

### GitHub 设置域名

在你的 GitHub 设置域名，在你的 GitHub 博客项目中点击 Settings，在 GitHub Pages 下的 Custom domain 写上你的域名，然后 save。

![github域名绑定](/postimages/github域名绑定.webp)

### 自动部署

根据之前的步骤我们使用 Hexo + [Keep](https://github.com/XPoet/hexo-theme-keep) 主题搭建了一个在线博客，下面教你如何使用 GitHub Actions 将博客自动部署到 GitHub Pages。以下内容参考了keep主题作者的[博文](https://juejin.cn/post/6943895271751286821).

#### GitHub Actions

##### 创建 GitHub 仓库

创建一个**私有仓库**用来存储 Hexo 项目源代码。（保证你的重要信息不泄露）

```
myblog-source仓库的master分支用来存放源代码
```

注意私有仓库默认分支已经变为main。需要更改名称为master

![私有仓库默认名称更改1](/postimages/私有仓库默认名称更改1.webp)

![私有仓库默认名称更改2](/postimages/私有仓库默认名称更改2.webp)

**创建私有仓库**

![私有仓库创建](/postimages/私有仓库创建.webp)

**公共仓库**用来存储编译之后的静态页面。（这里就不用在创建仓库了，之前我们部署过github pages到xxx.github.io）

xxx.github.io这个仓库的master分支用来存储静态文件

当私有仓库的 `master` 有内容 `push` 进来时（例如：主题文件，文章 md 文件、图片等）， 会触发 GitHub Actions 自动编译并部署到公共仓库的 `master`分支。

##### 创建 GitHub Token

- 创建一个有 **repo** 和 **workflow** 权限的 [GitHub Token](https://github.com/settings/tokens/new) 。

![个人token2](/postimages/个人token2.webp)

![个人token3](/postimages/个人token3.webp)

![个人token1](/postimages/个人token1.webp)

- 新生成的 Token 只会显示一次，如有遗失，重新生成即可。

##### 创建 repository secret

- 将上面生成的 Token 添加到私有仓库的 `Secrets` 里，并将这个新增的 `secret` 命名为 `HEXO_DEPLOY` （名字无所谓，看你喜欢）。
- 步骤：私有仓库 -> `settings` -> `Secrets` -> `New repository secret`。

![私有仓库secrets](/postimages/私有仓库secrets.webp)

新创建的 secret `HEXO_DEPLOY` 在 Actions 配置文件要用到，需跟配置文件保持一致！

##### 添加 Actions 配置文件

1. 在你的 Hexo 项目根目录下创建 `.github` 文件夹。
2. 在 `.github` 文件夹下创建 `workflows` 文件夹。
3. 在 `workflows` 文件夹下创建 `hexo-deploy.yml` 文件。

`hexo-deploy.yml` 文件配置如下：(这里是关键)

```yaml
name: deploying Hexo project to GitHub pages
on:
  push:
    branches:
      - master # master 分支有 push 行为时就触发这个 action

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@master

      - name: Build and Deploy
        uses: theme-keep/hexo-deploy-github-pages-action@master # 使用专门部署 Hexo 到 GitHub pages 的 action
        env:
          PERSONAL_TOKEN: ${{ secrets.HEXO_DEPLOY }} # secret 名
          PUBLISH_REPOSITORY: Smithkenny/Smithkenny.github.io # 公共仓库，格式：GitHub 用户名/仓库名
          BRANCH: master # 分支，我这里是推送到Smithkenny/Smithkenny.github.io仓库里的master分支(根据自己的仓库进行填写)
          PUBLISH_DIR: ./public # 部署 public 目录下的文件
```

##### 自动部署触发流程

- 修改你的 Hexo 博客源代码（例如：增加文章、修改文章、更改主题、修改主题配置文件等等）。
- 把你修改过的 Hexo 项目内容（只提交修改过的那部分内容） `push` 到 GitHub 公共仓库（本例：Smithkenny.github.io）的 `master` 分支。
- GitHub Actions 检测到 `master` 分支有内容 `push` 进来，会自动执行 action 配置文件的命令，将 Hexo 项目编译成静态页面，然后部署到私有仓库的 `master` 分支。
- 在私有仓库的 Actions 可以查看到你配置的 action。
- 这样的好处是源码保存在线上私有仓库中，具有可移植性。更改源码后不用再手动重新部署、上传。

![私有仓库action](/postimages/私有仓库action.webp)



### **源码上传**

#### 上传前的准备

##### 配置ssh key

win10 ssh key 默认在当前用户的.ssh文件夹内。

```sh
ssh-keygen -t rsa -C "xxx@qq.com"
```

密钥路径：
.ssh\id_rsa.pub 公钥
.ssh\id_rsa 私钥

##### 打开你的github主页，进入个人设置 -> SSH and GPG keys -> New SSH key：

名字：git-push
密钥输入**公钥**内容。
测试时是否成功：

```sh
ssh -T git@github.com
```

##### 在网站根路径下右键Git Bash Here,通过git 命令来上传源码到私有仓库。

##### 还需要配置：

```sh 
git config --global user.name "Smithkenny"// 你的github用户名，非昵称
git config --global user.email  "xxx@qq.com"// 填写你的github注册邮箱
```

### **上传源码**

#### 初始化git 

```sh
git init
```

#### 将改动添加到暂存区

```sh
git add . 
```

#### 添加说明

```sh
git commit -m "代码上传"
```

#### 将本地更改推送到远程master分支

```sh
git push origin master
```

**注意**

如果报错，说明本地仓库代码和私有仓库代码不一致，可以用以下方式解决。

先从私有库（myblog-source）把代码拉下来，会自动合并的（不用操心）

```sh
git pull origin master
```

可以通过如下命令进行代码合并

```sh
git pull --rebase origin master 
```

再将本地更改推送到远程master分支

```sh
git push origin master 
```

每次新增、修改文章都需要执行以下代码一次：

```sh
git add . （将改动添加到暂存区）
git commit -m "提交说明"
git push origin master 将本地更改推送到远程master分支。
```

然后私有仓库中的action检测到有代码改动会自动重新部署。

### **总结：**

笔记本生成id_rsa是私钥，id_rsa.pub是公钥。公钥可以用记事本打开
配置路径：github网站–>Settings–>SSH and GPG keys 配置公钥。用于笔记本和github连接。
github action 执行流程，公共仓展示静态页面，私有仓保存文章，源代码等等。action检测到私有仓库内变动，会自动打包部署到公共仓。不用在笔记本本地再部署，上传。

上传代码需要用笔记本创建的私钥和私有仓库中secrects 中的公钥进行匹配。

hexo g 会在网站的根路径下创建public目录和db.json

hexo clean 会删除hexo g 执行后所创建的内容。

### **遇到的问题**：

源代码上传后，页面使用的主题配置没生效，修改过的配置都没生效，logo也不显示，绑定的自定义域名也失效了。

#### **解决方法**：

在网站根路径下的source文件夹内创建_data文件夹，把keep主题配置 _config.yml复制一份命名为keep.yml。

如果以后配置有所更改，需要_data文件夹内的keep.yml和keep主题内的 _config.yml文件保持一致。

source文件夹内新建images 文件夹，用来存放图片。再创建CNAME 文件，文件内填写个人域名。如：haiewebpv.com 

再次上传源码。等待部署完成后查看网站。

D:\newblog\source\keep.yml 文件为D:\newblog\node_modules\hexo-theme-keep\_config.yml的备份文件。平滑升级hexo后
_config.yml会重置，所以要备份。

站点根路径：

D:\newblog\

D:\newblog\source 内文件夹：

```sh
_data
_posts
about
categories
images
links

CNAME # 文件
```

keep 主题配置文件路径

```sh
D:\newblog\node_modules\hexo-theme-keep\_config.yml
```

如何访问网站？

```sh
https://smithkenny.github.io/
https://haipengcv.com
```

### **新上传文章流程**

#### 在网站根目录内，右键git bash here 并创建文章标题

```sh
hexo new title "new-article"
```

上述命令的结果是在 `./hexo/source/_posts` 路径下新建了一个 new-article.md` 文件。

#### 进入到/source/_posts内编辑文章内容。

```sh
cd source/_posts
vim new-article.md

---
title: new-article
date: 2022-01-01 18:11:25
tags:
categories：
---
```

也可以用typora打开，直接编辑。也可以创建分类categories 便于搜索。

### **博客中的图片处理**

有以下两种方式保存图片：

#### 将图片上传到网站根路径source/images中，在文章中引用相对路径。

#### 使用网路路径，然后在文章中引用该路径。

**下面来说明如何使用网路路径**：

搭建个人图床，使用github公有仓库充当云存储。新建公有仓库，起名为Myimage。

并在仓库内创建一个img文件夹并加以说明。

![图床1](/postimages/图床1.webp)

##### **创建个人访问令牌**

获取路径

**Settings** --> **Developer settings**--> **Personal access tokens** --> **New personal access token** --> **copy token**

#####  **jsdelivr充当cdn，使图片访问更快**

https://www.jsdelivr.com/?docs=gh

获取cdn地址

![cdn1](/postimages/cdn1.webp)

**举例：**

我的这个图床仓库名是Smithkenny/Myimage

https://github.com/Smithkenny/Myimage

**cdn地址为：**

https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img图片名称.webp

**使用picGo充当图片git push 工具**

https://github.com/Molunerfinn/PicGo

安装地址：https://github.com/Molunerfinn/PicGo/releases

![picgo](/postimages/picgo.webp)

#### **配置picGo**

![picgo1](/postimages/picgo1.webp)

将github图床设置为默认图床。

##### **上传图片处理**

PicGo支持批量和单个的图片上传

- 批量上传，把图片批量拖拽到大红框
- 单个上传，复制后，点击小红框

![picgo2](/postimages/picgo2.webp)

文章中使用图床中的图片格式

中括号小括号

### **后记**

**keep**主题相关设置

设置标题、作者、url自定义logo、背景图片。开启百分比滚动条。添加个人联系方式。增加categories、links、about。开启文章预览。字数统计、作者标签。文章中代码可复制、启用左侧目录。开启文章最下方版权所有，开启全局搜索功能、开启rss订阅、cdn、慢加载功能。

### 更换电脑如何上传文章到GitHub

#### 前期准备

新电脑需要安装git、hexo。并把旧电脑用户目录下.ssh文件夹内公钥和私钥复制出来到新电脑的用户目录的.ssh文件夹内。

#### 具体步骤

创建myblog文件夹

```
/e/myblog
```

右键git bush here。初始化git，会自动在/e/myblog目录内创建.git文件夹

```sh
git init 
```

测试笔记本到GitHub私有仓库的连通性。

```sh
ssh -T git@github.com

Hi Smithkenny! You've successfully authenticated, but GitHub does not provide shell access.
```

添加信息

```sh
git config --global user.name "Smithkenny"// 你的github用户名，非昵称
git config --global user.email  "xxx@qq.com"// 填写你的github注册邮箱
```

配置远端仓库并远程拉取源代码到本地

```sh
git remote add origin git@github.com:Smithkenny/myblog-source.git
```

```bash
git pull origin master

remote: Enumerating objects: 345, done.
remote: Counting objects: 100% (345/345), done.
remote: Compressing objects: 100% (248/248), done.
remote: Total 345 (delta 167), reused 231 (delta 71), pack-reused 0
Receiving objects: 100% (345/345), 1.87 MiB | 105.00 KiB/s, done.
Resolving deltas: 100% (167/167), done.
From github.com:Smithkenny/myblog-source
 * branch            master     -> FETCH_HEAD
 * [new branch]      master     -> origin/master
```

然后就可以修改文章，最后上传文章了。步骤见新文章上传流程。

#### 报错信息

使用拉取源码到本地时，当本地的环境和远端环境中某些文件名字相同内容不同时，会出现远端部分文件拉取失败。此时只要删除提示中本地的文件，再次执行拉取命令即可。

```sh
git pull origin master

remote: Enumerating objects: 360, done.
remote: Counting objects: 100% (360/360), done.
remote: Compressing objects: 100% (256/256), done.
remote: Total 360 (delta 178), reused 242 (delta 78), pack-reused 0
Receiving objects: 100% (360/360), 1.87 MiB | 1.33 MiB/s, done.
Resolving deltas: 100% (178/178), done.
From github.com:Smithkenny/myblog-source
 * branch            master     -> FETCH_HEAD
 * [new branch]      master     -> origin/master
error: The following untracked working tree files would be overwritten by merge:
        .github/dependabot.yml
        .gitignore
        _config.landscape.yml
        _config.yml
        package-lock.json
        package.json
        scaffolds/draft.md
        scaffolds/page.md
        scaffolds/post.md
        themes/.gitkeep
Please move or remove them before you merge.
Aborting
```

清空github上私有仓库源代码，将本地重新创建好的博客源码上传到远端私有仓库。

#### github actions报错

需要重新创建博客源码文件夹，重复以下操作。

```sh
1.初始化博客文件夹,如果初始化缓慢需要设置代理。
#$ export http_proxy=http://<Windows 主机的 IP 地址>:<代理端口>
#$ export https_proxy=http://<Windows 主机的 IP 地址>:<代理端口>
#使用下面的命令取消代理：
#$ unset http_proxy https_proxy
hexo init myblog-source
2.进入到目录
cd myblog-source
npm install
3.将旧文件夹内_config.yaml复制到myblog-source内
4.下载主题，使用keep主题
npm install hexo-theme-keep
5.重新生成和运行
hexo clean
hexo g
hexo s
6.创建标题、分类、归档、友联、关于，在Myblog-source\source内新建images文件夹，并分别在旧文件夹下对应位置将内容复制到新博客内
hexo new page "categories"
hexo new page "about"
hexo new page "links"
7.将旧文件夹source\_data复制到\Myblog-source\source\_data内
8.将旧文件夹\node_modules\hexo-theme-keep中_config.yml复制到新博客路径\Myblog-source\node_modules\hexo-theme-keep内
9.将旧文件夹根路径下CNAME文件复制到新博客根路径下
10.在新博客文件夹内初始化git，会生成.git文件夹。
git init
git add .
git commit -m "代码上传"
git push origin master
如果报以下错误：Github "Updates were rejected because the remote contains work that you do not have locally."
则：
查看git远程仓库
git remote -v
没有则使用以下命令添加：
git remote add origin [//your github url]
拉取远程仓库镜像
git pull origin master --allow-unrelated-histories
10.然后再执行以下命令推送到仓库
git add .
git commit -m "代码上传"
git push origin master
```

报错解决[参考](https://stackoverflow.com/questions/18328800/github-updates-were-rejected-because-the-remote-contains-work-that-you-do-not-h)

