# 网站访客统计搭建

## 一、使用zeabur平台搭建umami
`zeabur`官方[<span style="color:orange">部署说明</span>](https://zeabur.com/docs/zh-CN/marketplace/umami)
根据官方说明，首先需要部署`PostageSQL`数据库，用来存储`umami`的数据。需要注意的是，部署好数据库后需要手动创建一个名称为`umami`的数据库。由于平台没有用于连接数据库的终端，所以我这里使用`DBeaver`连接数据库。

### 1.连接数据库

![umami1](/postimages/umami1.webp)

查看`PostageSQL`数据库中连接信息

![umami2](/postimages/umami2.webp)

创建连接，第一次连接需要下载驱动。

![umami5](/postimages/umami5.webp)

![umami4](/postimages/umami4.webp)

### 2.创建数据库

![umami6](/postimages/umami6.webp)

## 二、创建umami项目
这里可以根据平台文档来创建。需要注意的是：创建完毕后，需要将刚刚创建的`PostageSQL`数据库的用户名、密码、端口信息应用到`umami`项目中。最后重启等待部署完毕。

![umami7](/postimages/umami7.webp)

## 三、自定义域名
将自定义的域名（umami.haipengv.com）指向名平台默认域名(我这里是myumami.zeabur.app),注意勾选`Redirect to another domain`选项。

![umami8](/postimages/umami8.webp)

## 四、域名映射
最后需要在`cloudflare`中新增一条A记录，将`umami.haipengv.com`和ip绑定。这里的IP可以通过ping平台默认域名（myumami.zeabur.app）来得到。
DNS->添加记录->类型选择'A'。

![umami9](/postimages/umami9.webp)

最后可以通过访问我们自定义的[域名](https://umami.haipengv.com)，来查看访客记录了。
`umami`默认的用户名是：`admin`,密码是：`umami`。
[官方项目地址](https://github.com/umami-software/umami)


