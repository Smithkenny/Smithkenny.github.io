# 个人域名注册购买教程


### 简介

**namesilo是国外的域名服务商，首次购买和续费价格都很实惠，不用翻墙也可以打开，此外还赠送隐私保护，支付方式支持支付宝、Paypal、Visa 等多种付款方式。**

**下面是namesilo购买域名的步骤流程**

### 登陆https://www.namesilo.com/，注册账号

![注册](/postimages/注册.webp)

![注册2](/postimages/注册2.webp)

### 输入要注册的域名查找、选择、结账

![域名购买](/postimages/域名购买.webp)

![域名购买2](/postimages/域名购买2.webp)

![域名购买3](/postimages/域名购买3.webp)

Auto-Renew：设置域名是否自动续费，自行确定，影响不大。

Privacy Setting：代表域名隐私保护设置，建议设置成 WHOIS Privacy ，反正是免费的。

![域名购买4](/postimages/域名购买4.webp)

### 解析域名

登录刚刚注册的账号选择my account

![域名解析1](/postimages/域名解析1.webp)

**选择accounts domains**

![域名解析2](/postimages/域名解析2.webp)

**管理域名**

![域名解析3](/postimages/域名解析3.webp)

**删掉多余的记录**

![域名解析4](/postimages/域名解析4.webp)

**添加一条CNAME记录，添加一条A记录。A记录可以添加多条。**

CNAME 指向github 的域名

github域名格式：[用户名].github.io

A记录指向GIThub域名所解析出来的IP

![域名解析5](/postimages/域名解析5.webp)

**注意：添加后不会立刻生效，此处有说明.**

**You can use the form below to manage the DNS settings for the domain(s) listed above. Changes may take up to 15 minutes to completely process. Please keep in mind that correctly modifying the DNS records for this domain is extremely important. Any errors on your part could make your web site, email and other services inoperable. Please only utilize this form if you are comfortable and experienced managing DNS.**

**差不多半小时，域名就能解析成功。具体测试是否成功，可以在本地cmd的dos界面 ping 域名，查看IP，或者在一些域名查找对应IP的网站查询。**


