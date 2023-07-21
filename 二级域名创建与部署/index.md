# 二级域名创建与部署


## 二级域名创建与部署

### 首先你需要拥有一个顶级域名

我是在[namesoli](https://www.namesilo.com/)购买的域名。可以查看该顶级域名能够创建二级域名最大数量。

登录账户，点击account Domains。

![accountdomains](/postimages/accountdomains.webp)

可以看到我们购买的域名，然后点击域名，进入域名控制台。

![mydomain](/postimages/mydomain.webp)

可以看到子域名转发选项，点击它。进入子域名配置界面。

![sub-domain-forwarding](/postimages/sub-domain-forwarding.webp)

点击help按钮，可以看到当前我们购买的顶级域名最多能够创建50个子域名。

![max-sub-domains](/postimages/max-sub-domains.webp)

#### 二级域名（子域名）的格式

比如我的网站是`www.haipengv.com`,可以通过访问`blog.haipengv.com`查看我的博客。在`haipengv.com`前面用`.`隔开加任意字符就是二级域名的格式。

### 部署烟花网站

首先在github上部署项目

登录[github](https://github.com/)，创建一个公开的新仓库。

![new-repository](/postimages/new-repository.webp)

复制仓库的ssh地址，一会连接库时需要。

![ssh-repository](/postimages/ssh-repository.webp)

准备上传烟花代码，在本地创建一个文件夹`E:\fireworkshow`。需要确保本地已经搭建好git环境。将烟花代码复制到`E:\fireworkshow`内。创建一个新文件夹`test`并进入`test`文件夹。此时所在位置`E:\fireworkshow\test`。

将仓库代码同步到本地test目录内

```sh
git clone git@github.com:Smithkenny/fireworkshow.git
```

将`E:\fireworkshow\test\fireworkshow`文件夹内的所有文件复制到`E:\fireworkshow`。然后删除空文件夹`test`和`fireworkshow`。

运行以下命令

```sh
git add . 
git commit -m "上传代码测试"
git push origin main
```

**这里注意我们默认创建的仓库主分支是`main`。**

代码上传完毕后，此时还不能访问。

我这里使用`cloudflare`做一条域名解析。

登录[cloudflare](https://dash.cloudflare.com/)，找到DNS选项，添加一条`A`记录。指向一个ipv4地址。这里的地址是ping 仓库域名得来的。

`2023.haipengv.com`-->`ipv4地址`

![A-record](/postimages/A-record.webp)

添加好`A`记录后，回到仓库，点击settings，找到pages选项，配置我们二级域名。稍等十分钟左右，就可以通过二级域名`2023.haipengv.com`访问我们的网站了。

![pages-index](/postimages/pages-index.webp)

#### 遇到的问题

网站中音乐文件很大，超过2M，音乐加载超时，如何提升加载速度？

#### 解决办法

将大文件上传到网盘，复制网盘对应的文件路径，将该路径添加到`index.html`文件中。我这里用的是[永硕网盘](http://yesjohn.ysepan.com/)。

最后访问`2023.haipengv.com`欣赏烟花吧！

相关参考

- https://blog.wangriyu.wang/2018/01-githubpage.html

