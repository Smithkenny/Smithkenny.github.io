# 给博客加个后台

之前写文章的流程：

* 进入笔记本中博客源码的文件夹
* 右键打开git bash，使用'git pull origin master' 将本地与线上进行同步。
* 创建新文章（hugo new posts/新文章.md）
* 手动更改文章分类
* 写文章内容
* 上传文章（git add .. \ git commit -m ""\ git push origin master）
* 最后使用github action自动发布到主页

由于hugo没有web页面，所以每次写文章都要经过以上过程，非常的繁琐。最近看到有人推荐一款开源的博客web，于是开始搜集相关资料自己动手搭建了一个。好处是代码开源而且线上布署，再也没有复杂的创建过程，文章写好后点击上传按钮一键上传，大大提高工作效率。

### 项目地址

```
https://github.com/Qexo/Qexo
```

### 特色功能

* 自定义图床上传图片
* 在线配置编辑
* 在线页面管理
* 开放API
* 自动检查更新
* 在线一键更新
* 快速接入友情链接
* 简单的说说短文
* 字数统计
* 自动填充文章模板

### 部署

#### 申请mongodb

**注册[mongodb账号](https://www.mongodb.com/cloud/atlas/register)，并登录。**

**新建mongodb数据库**：

`Build a Database`—>`Create(Free)`—>`Create Cluster[地区选择**AWS / N. Virginia (us-east-1)**]`—>配置数据库：添加用户、允许所有IP访问(0.0.0.0/0)—>`Finsh and Close`

**数据库连接信息**

在 `Databas`页面点击 `Connect-->Connect with the MonggoDB Shell`

**一键部署**

```
https://vercel.com/new/clone?repository-url=https://github.com/am-abudu/Qexo
```

首次部署会报错，请无视并重新进入项目，在项目设置界面添加环境变量：

到[Dashboard – Vercel](https://vercel.com/dashboard)找到刚刚部署的项目点击进入，进入 `Setting`—>`Environment Variables`添加环境变量。

| 名称（key）   | 示例(value)                             | 意义                                    |
| ------------- | --------------------------------------- | --------------------------------------- |
| MONGODB\_HOST | mongodb+srv://cluster0.xxxx.mongodb.net | MongoDB 数据库连接地址                  |
| MONGODB\_PORT | 27017                                   | MongoDB 数据库通信端口 默认应填写 27017 |
| MONGODB\_USER | abudu                                   | MongoDB 数据库用户名                    |
| MONGODB\_DB   | Cluster0                                | MongoDB 数据库名                        |
| MONGODB\_PASS | JWo0xxxxxxxx                            | MongoDB 数据库密码                      |

在 Deployments 点击 Redeploy 开始部署

若没有 Error 信息即可打开域名进入初始化引导，国内可能无法使用默认域名进入，我们要解析一个自己的域名：

进入 `Project`—>`View Domains`，输入待解析的域名点击 `Add`，按要求添加解析记录然后刷新。

我是用 `cloudflare`作为cnd来加速网站访问的。首先登录[cloudflare](https://dash.cloudflare.com/)根据vercel中你的项目-->settings->domains中输入你的网址（我这里的是qexo.haipengv.com）点击 `add`。再回到[cloudflare](https://dash.cloudflare.com/)，添加 `CNAME`，将所有流量指向 `vercel`项目。

### 配置

#### github/gitlab 仓库

您的 hugo 源代码所在的仓库（不是发布后的web页面）

```
username/repo
```

#### 项目分支

您的hego自动化部署所在仓库的分支

```
master
```

#### GitHub/Gitlab密钥（token）

在 [Github 设置](https://github.com/settings/tokens) / [Gitlab 设置](https://gitlab.com/-/profile/personal_access_tokens) 生成的 Token 需要 Repo 下的至少读取和写入权限不建议给出所有权限。

`GitHub Settings（右上角头像）`—>`Developer settings`—>`Personal access tokens`—>`Tokens(classic)`：

#### 仓库路径

hugo自动化部署所在仓库的路径，若为目录请留空

```
path/
```

#### Vercel_token

您的Vercel 账户密钥在[此处](https://vercel.com/account/tokens)生成

#### Project_ID

您的qexo部署所在项目的ID位于 `Project Settings` —> `General` —> `Project ID`

```
prj_xxxxxxx
```

有了博客后台后，现在写文章流程：

* 打开博客后台web
* 点击文章右边加号新增文章
* 右侧添加标题、发布时间、标签、分类
* 编写文章内容
* 保存、发布（使用github action自动发布到主页）

搭建全过程[参考](https://daiyu.fun/posts/da27.html)

