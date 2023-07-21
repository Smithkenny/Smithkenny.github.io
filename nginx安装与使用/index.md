# nginx安装与使用


## nginx 1.16.1 安装使用说明

1.创建目录

mkdir -p /var/temp/nginx

下载：

https://nginx.org/en/download.html

解压

tar -zxvf nginx-1.16.1.tar.gz 

安装编译依赖包：

yum install -y gcc gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel

2.进入nginx-1.16.1

编译参数

```Shell
./configure \
--prefix=/usr/local/nginx \
--pid-path=/var/run/nginx/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-http_gzip_static_module \
--http-client-body-temp-path=/var/temp/nginx/client \
--http-proxy-temp-path=/var/temp/nginx/proxy \
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
--http-scgi-temp-path=/var/temp/nginx/scgi
```

https://nginx.org/en/download.html

### config 编译参数

```Shell
nginx1.16编译安装configure参数：
[root@363ee3055cf0 nginx-1.16.1]# ./configure --help

  --help                             print this message 打印帮助信息

  --prefix=PATH                      set installation prefix 设置安装目录
  --sbin-path=PATH                   set nginx binary pathname 设置sbin路径
  --modules-path=PATH                set modules path 设置模块路径
  --conf-path=PATH                   set nginx.conf pathname 设置配置文件路径
  --error-log-path=PATH              set error log pathname 设置错误日志的路径
  --pid-path=PATH                    set nginx.pid pathname 设置pid路径
  --lock-path=PATH                   set nginx.lock pathname 设置锁路径

  --user=USER                        set non-privileged user for 设置用户
                                     worker processes
  --group=GROUP                      set non-privileged group for 设置组
                                     worker processes

  --build=NAME                       set build name 设置构建名
  --builddir=DIR                     set build directory 设置构建文件夹

  --with-select_module               enable select module 开启select模块
  --without-select_module            disable select module关闭
  --with-poll_module                 enable poll module
  --without-poll_module              disable poll module

  --with-threads                     enable thread pool support

  --with-file-aio                    enable file AIO support

  --with-http_ssl_module             enable ngx_http_ssl_module
  --with-http_v2_module              enable ngx_http_v2_module
  --with-http_realip_module          enable ngx_http_realip_module
  --with-http_addition_module        enable ngx_http_addition_module
  --with-http_xslt_module            enable ngx_http_xslt_module
  --with-http_xslt_module=dynamic    enable dynamic ngx_http_xslt_module
  --with-http_image_filter_module    enable ngx_http_image_filter_module
  --with-http_image_filter_module=dynamic
                                     enable dynamic ngx_http_image_filter_module
  --with-http_geoip_module           enable ngx_http_geoip_module
  --with-http_geoip_module=dynamic   enable dynamic ngx_http_geoip_module
  --with-http_sub_module             enable ngx_http_sub_module
  --with-http_dav_module             enable ngx_http_dav_module
  --with-http_flv_module             enable ngx_http_flv_module
  --with-http_mp4_module             enable ngx_http_mp4_module
  --with-http_gunzip_module          enable ngx_http_gunzip_module
  --with-http_gzip_static_module     enable ngx_http_gzip_static_module
  --with-http_auth_request_module    enable ngx_http_auth_request_module
  --with-http_random_index_module    enable ngx_http_random_index_module
  --with-http_secure_link_module     enable ngx_http_secure_link_module
  --with-http_degradation_module     enable ngx_http_degradation_module
  --with-http_slice_module           enable ngx_http_slice_module
  --with-http_stub_status_module     enable ngx_http_stub_status_module

  --without-http_charset_module      disable ngx_http_charset_module
  --without-http_gzip_module         disable ngx_http_gzip_module
  --without-http_ssi_module          disable ngx_http_ssi_module
  --without-http_userid_module       disable ngx_http_userid_module
  --without-http_access_module       disable ngx_http_access_module
  --without-http_auth_basic_module   disable ngx_http_auth_basic_module
  --without-http_mirror_module       disable ngx_http_mirror_module
  --without-http_autoindex_module    disable ngx_http_autoindex_module
  --without-http_geo_module          disable ngx_http_geo_module
  --without-http_map_module          disable ngx_http_map_module
  --without-http_split_clients_module disable ngx_http_split_clients_module
  --without-http_referer_module      disable ngx_http_referer_module
  --without-http_rewrite_module      disable ngx_http_rewrite_module
  --without-http_proxy_module        disable ngx_http_proxy_module
  --without-http_fastcgi_module      disable ngx_http_fastcgi_module
  --without-http_uwsgi_module        disable ngx_http_uwsgi_module
  --without-http_scgi_module         disable ngx_http_scgi_module
  --without-http_grpc_module         disable ngx_http_grpc_module
  --without-http_memcached_module    disable ngx_http_memcached_module
  --without-http_limit_conn_module   disable ngx_http_limit_conn_module
  --without-http_limit_req_module    disable ngx_http_limit_req_module
  --without-http_empty_gif_module    disable ngx_http_empty_gif_module
  --without-http_browser_module      disable ngx_http_browser_module
  --without-http_upstream_hash_module
                                     disable ngx_http_upstream_hash_module
  --without-http_upstream_ip_hash_module
                                     disable ngx_http_upstream_ip_hash_module
  --without-http_upstream_least_conn_module
                                     disable ngx_http_upstream_least_conn_module
  --without-http_upstream_random_module
                                     disable ngx_http_upstream_random_module
  --without-http_upstream_keepalive_module
                                     disable ngx_http_upstream_keepalive_module
  --without-http_upstream_zone_module
                                     disable ngx_http_upstream_zone_module

  --with-http_perl_module            enable ngx_http_perl_module
  --with-http_perl_module=dynamic    enable dynamic ngx_http_perl_module
  --with-perl_modules_path=PATH      set Perl modules path
  --with-perl=PATH                   set perl binary pathname

  --http-log-path=PATH               set http access log pathname
  --http-client-body-temp-path=PATH  set path to store
                                     http client request body temporary files
  --http-proxy-temp-path=PATH        set path to store
                                     http proxy temporary files
  --http-fastcgi-temp-path=PATH      set path to store
                                     http fastcgi temporary files
  --http-uwsgi-temp-path=PATH        set path to store
                                     http uwsgi temporary files
  --http-scgi-temp-path=PATH         set path to store
                                     http scgi temporary files

  --without-http                     disable HTTP server
  --without-http-cache               disable HTTP cache

  --with-mail                        enable POP3/IMAP4/SMTP proxy module
  --with-mail=dynamic                enable dynamic POP3/IMAP4/SMTP proxy module
  --with-mail_ssl_module             enable ngx_mail_ssl_module
  --without-mail_pop3_module         disable ngx_mail_pop3_module
  --without-mail_imap_module         disable ngx_mail_imap_module
  --without-mail_smtp_module         disable ngx_mail_smtp_module

  --with-stream                      enable TCP/UDP proxy module
  --with-stream=dynamic              enable dynamic TCP/UDP proxy module
  --with-stream_ssl_module           enable ngx_stream_ssl_module
  --with-stream_realip_module        enable ngx_stream_realip_module
  --with-stream_geoip_module         enable ngx_stream_geoip_module
  --with-stream_geoip_module=dynamic enable dynamic ngx_stream_geoip_module
  --with-stream_ssl_preread_module   enable ngx_stream_ssl_preread_module
  --without-stream_limit_conn_module disable ngx_stream_limit_conn_module
  --without-stream_access_module     disable ngx_stream_access_module
  --without-stream_geo_module        disable ngx_stream_geo_module
  --without-stream_map_module        disable ngx_stream_map_module
  --without-stream_split_clients_module
                                     disable ngx_stream_split_clients_module
  --without-stream_return_module     disable ngx_stream_return_module
  --without-stream_upstream_hash_module
                                     disable ngx_stream_upstream_hash_module
  --without-stream_upstream_least_conn_module
                                     disable ngx_stream_upstream_least_conn_module
  --without-stream_upstream_random_module
                                     disable ngx_stream_upstream_random_module
  --without-stream_upstream_zone_module
                                     disable ngx_stream_upstream_zone_module

  --with-google_perftools_module     enable ngx_google_perftools_module
  --with-cpp_test_module             enable ngx_cpp_test_module

  --add-module=PATH                  enable external module
  --add-dynamic-module=PATH          enable dynamic external module

  --with-compat                      dynamic modules compatibility

  --with-cc=PATH                     set C compiler pathname
  --with-cpp=PATH                    set C preprocessor pathname
  --with-cc-opt=OPTIONS              set additional C compiler options
  --with-ld-opt=OPTIONS              set additional linker options
  --with-cpu-opt=CPU                 build for the specified CPU, valid values:
                                     pentium, pentiumpro, pentium3, pentium4,
                                     athlon, opteron, sparc32, sparc64, ppc64

  --without-pcre                     disable PCRE library usage
  --with-pcre                        force PCRE library usage
  --with-pcre=DIR                    set path to PCRE library sources
  --with-pcre-opt=OPTIONS            set additional build options for PCRE
  --with-pcre-jit                    build PCRE with JIT compilation support

  --with-zlib=DIR                    set path to zlib library sources
  --with-zlib-opt=OPTIONS            set additional build options for zlib
  --with-zlib-asm=CPU                use zlib assembler sources optimized
                                     for the specified CPU, valid values:
                                     pentium, pentiumpro

  --with-libatomic                   force libatomic_ops library usage
  --with-libatomic=DIR               set path to libatomic_ops library sources

  --with-openssl=DIR                 set path to OpenSSL library sources
  --with-openssl-opt=OPTIONS         set additional build options for OpenSSL

  --with-debug                       enable debug logging

```



```Shell
#编译
make
#安装
make install 
```

whereis nginx 

在/usr/local/nginx/sbin/内

```Shell
./nginx 启动

 ./nginx -s stop 强制停止
 ./nginx -s quit 优雅停止

./nginx -s reload 重新加载
./nginx -t 配置检测
./nginx -v 版本信息
./nginx -V 详细信息
./nginx -h 帮助
./nginx -c 指定配置文件
```

注意：

1.云服务器需要默认开启nginx 80

2.虚拟机安装，需要关闭防火墙

3.本机win或mac需要关闭防火墙

nginx.conf 

```javascript
    server {
        listen       80; 						#监听端口
        server_name  localhost;					#域名
        location / {							# / 从根开始匹配。html文件夹内的index index.html index.htm
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;   #匹配不到自动转到错误页。html/50x.html 
        location = /50x.html {
            root   html;
        }
```

更改 80 端口后需重新加载。

cd /usr/local/nginx/sbin 

./nginx -s reload 

访问：ip+监听端口



## nginx 的进程模型

master 主进程

worker  工作进程

worker_processes  2; # 默认为1,配置为n-1 .n为cpu数量。

### nginx事件处理。异步非阻塞。

```javascript
events {
    # 默认使用epoll
    use epoll;
    # 每个worker 允许连接的客户端最大连接数
    worker_connections  10240;
}
```



## 配置文件详解：



```javascript
    server {
        listen       89;
        server_name  localhost;
        location / {
            root   html;
            index  imooc.html index.htm;
        }
        }
```

/usr/local/nginx/html/imooc.html

```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to immoc!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome imooc!</h1>
</body>
</html>
```

或者：

nginx.conf中添加

```Shell
  include       imooc.conf;
```

创建imooc.conf文件。

/usr/local/nginx/conf/imooc.conf

```javascript
   server {
        listen       89;
        server_name  localhost;
        location / {
            root   html;
            index  imooc.html index.htm;
        }
        }
```

每次更改后都要重启服务。

## 常见pid报错

### nginx.pid打开失败：

1.nginx: [error] invalid PID number "" in "/usr/local/nginx/logs/nginx.pid"

解决方法：

使用nginx -c的参数指定nginx.conf文件的位置
然后再重新启动，解决问题，可以通过ps -ef|grep nginx 看到服务已经启动成功。

/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf

2.nginx: [error] open() "/var/run/nginx/nginx.pid" failed (2: No such file or directory) 

提示信息说明在 **/var/run/nginx/** 目录下找不到 **nginx.pid** 文件，解决方式有两种

第一种方式：创建默认目录 **/var/run/nginx/** ；

第二种方式：修改 **nginx.conf** 文件，指定 **pid文件** 所在目录。

## 日志切割：(手动)

cat_my_log.sh

```Shell
#!/bin/bash
LOG_PATH="/var/log/nginx/"
RECORD_TIME=$(date -d "yesterday" +%Y-%m-%d+%H:%M)
PID=/var/run/nginx/nginx.pid
mv ${LOG_PATH}/access.log ${LOG_PATH}/access.${RECORD_TIME}.log
mv ${LOG_PATH}/error.log ${LOG_PATH}/error.${RECORD_TIME}.log
#向nginx主进程发送信号，用于重新打开日志文件
kill -USR1 `cat $PID`
```

chmod +x cat_my_log.sh

## 日志切割：(定时)

1.安装定时任务

```Shell
yum install crontabs
```

2.crontab -e 编辑并添加一行新的任务

```Shell
*/1 * * * * /usr/local/nginx/sbin/cut_my_log.sh
```

3.重启定时任务

```Shell
service crond restart 
```

4.常用定时任务

```Shell
service crond start 
service crond stop 
service crond reload #重新加载
service crond restart  #重启服务
crontab -l #查看任务列表
crontab -e # 编辑任务
```

每分钟执行

```Shell
*/1 * * * *
```

每日凌晨（每天晚上23：59）执行：

```Shell
59 23 * * *
```

每日凌晨1点执行：

```Shell
0 1 * * *
```

每天为数据库定时备份：[参考](https://www.cnblogs.com/leechenxiang/p/7110382.html)

## 虚拟主机-使用nginx为静态资源提供服务

静态资源可以是mp4、png、jpg等等。

/usr/local/nginx/conf/imooc.conf中创建静态资源路径。

```javascript
    server {
        listen       90;
        server_name  localhost;
        location / {
            root   /home/foodie-shop;
            index  index.html;
        }

        location /static {
	    alias /home/imooc;
        }
        location /imooc {
            root   /home;
        }
        }
```

请求/home/imooc下的资源。

两种写法：

一、正常请求：

```javascript
        location /imooc {
            root   /home;
        }
```

直接请求：

http://10.4.7.129:90/imooc/456.jpg

二、给路径起别名：

将/home/imooc创建别名 为/static

```javascript
        location /static {
	    alias /home/imooc;
        }
```

请求时用别名请求

http://10.4.7.129:90/static/456.jpg

将imooc.conf文件内容应用到nginx.conf中

编辑/usr/local/nginx/conf/nginx.conf

单独添加一行：

```Shell
    include       imooc.conf;
```

检测配置是否正确：

```Shell
/usr/local/nginx/sbin/nginx -t 

nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful

```

重启nginx

```Shell
/usr/local/nginx/sbin/nginx -s reload 
```

测试结果：

http://10.4.7.129:90/imooc/456.jpg

http://10.4.7.129:90/static/456.jpg

## gzip压缩提升请求效率

```Shell
#开启gzip压缩功能，目的：提高传输效率，节约带宽
gzip  on;
#限制最小压缩，小于1字节的文件不会压缩
gzip_min_length 1;
#的定义压缩的级别（压缩比，文件越大，压缩越多，但是cpu使用会越多） 
gzip_comp_level 3;
#定义压缩文件类型
gzip_types text/plain application/javascript application/x-javascript test/css application/xml text/javascript application/x-httpd/php image/jpeg image/gif image/png application/json;
```

## location匹配规则

空格：默认匹配，普通匹配

```javascript
    server {
        listen       91;
        server_name  localhost;
        location  / {
            root   html;
            index  imooc.html index.htm;
        }
        }
```

= ：精确匹配

```javascript
    server {
        listen       91;
        server_name  localhost;
        #精确匹配
        location = / {
            root   html;
            index  imooc.html index.htm;
        }
        }
```



正则表达式。

```Shell
/home/imooc/img
[root@centos7 img]# ll
total 808
-rw-r--r--. 1 root root  25125 Jun 30 19:40 456.jpg
-rw-r--r--. 1 root root  25125 Dec 19 00:12 456.JPG
-rw-r--r--. 1 root root 383222 Dec 13 19:25 AutoPlayOptIn.gif
-rw-r--r--. 1 root root 383222 Dec 19 00:17 AutoPlayOptIn.GIF
```

~*  ：匹配正则表达式，*代表不区分大小写

http://10.4.7.129:92/imooc/img/456.JGP (可以访问)不论是否定义了.JPG这种类型都能访问。

~ ：匹配正则表达式，区分大小写

http://10.4.7.129:92/imooc/img/456.JGP (不能访问)因为没有定义.JPG这种类型能访问。

```javascript
    server {
        listen       92;
        server_name  localhost;
        #正则表达式。*代表不区分大小写
        location ~* \.(GIF|png|bmp|jpg|jpeg) {
            root   /home;
        }
        }

    server {
        listen       92;
        server_name  localhost;
        #正则表达式。*代表不区分大小写
        location ~ \.(GIF|png|bmp|jpg|jpeg) {
            root   /home;
        }
        }

```

^~ 以某个字符路径开头请求

```javascript
    server {
        listen       93;
        server_name  localhost;
        #^~ 以某个字符路径开头请求
        location ^~ /imooc/img {
            root   /home;
        }
        }
```

## DNS解析域名

user-> client(www.imooc.com) -> nginx(代理服务器192.168.1.88 小区大门) ---> tomcat1 （目标服务器 内网） ##号楼##室

​																																	---> tomcat2 （目标服务器 内网）##号楼##室



## 用switchhosts 模拟本地域名解析访问

https://cloud.tencent.com/developer/article/1408956

https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Foldj%2FSwitchHosts%2Freleases%2Fdownload%2Fv3.3.12%2FSwitchHosts-win32-ia32_v3.3.12.5349.zip

自定义hosts 一键切换。不用更改本机hosts

定义nginx-dev-imooc 的hosts

添加映射：

```Shell
10.4.7.129 www.imooc.com
```

访问：

www.imooc.com 显示10.4.7.129 默认界面

## 跨域访问

![nginx跨域](/postimages/nginx跨域.webp)

![nginx跨域访问success](/postimages/nginx跨域访问success.webp)

![cors跨域资源共享](/postimages/cors跨域资源共享.webp)

当出现403跨域错误的时候 `No 'Access-Control-Allow-Origin' header is present on the requested resource`，需要给Nginx服务器配置响应的header参数：

只需要在Nginx的配置文件中配置以下参数：

```javascript
location / { 
    #允许跨域请求的域，*代表所有
    add_header 'Access-Control-Allow-Origin' *;
    #允许请求的方法，比如GET, POST, DELETE，PUT
    add_header 'Access-Control-Allow-Methods' *;
    #允许带上cookie请求
    add_header 'Access-Control-Allow-Credentails' 'true';
    #允许请求的HEADER
    add_header 'Access-Control-Allow-Headers' *;
    if ($request_method = 'OPTIONS') {
        return 204;
    }
} 
```



## nginx 防盗链技术支持

```javascript
#对源站点验证
valid_referers *.imooc.com;
#非法引入会进入下方判断
if ($invalid_referer) {
	return 404;
}
```

## nginx 模块化体系

![nginx模块化体系](/postimages/nginx模块化体系.webp)

```Shell
[root@centos7 nginx-1.16.1]# ll
total 756
drwxr-xr-x. 6 1001 1001   4096 Dec 17 04:04 auto       #一些判断操作系统支持，编译等相关的文件
-rw-r--r--. 1 1001 1001 296463 Aug 13  2019 CHANGES    #版本的更改日志
-rw-r--r--. 1 1001 1001 452171 Aug 13  2019 CHANGES.ru #俄版版本的更改日志
drwxr-xr-x. 2 1001 1001    168 Dec 17 04:04 conf      #配置文件夹
-rwxr-xr-x. 1 1001 1001   2502 Aug 13  2019 configure #编译配置程序
drwxr-xr-x. 4 1001 1001     72 Dec 17 04:04 contrib   #提供了语法高亮支持脚本，让vim打开时，语法高亮。需要拷贝contrib														中到 本地vim目录(如果根目录没有该目录，先mkdir ~/.vim)
drwxr-xr-x. 2 1001 1001     40 Dec 17 04:04 html      #默认页面
-rw-r--r--. 1 1001 1001   1397 Aug 13  2019 LICENSE   #许可证书
-rw-r--r--. 1 root root    355 Dec 17 04:19 Makefile  #编译后的文件
drwxr-xr-x. 2 1001 1001     21 Dec 17 04:04 man       #使用手册
drwxr-xr-x. 3 root root    174 Dec 17 04:20 objs      #第三方插件
-rw-r--r--. 1 1001 1001     49 Aug 13  2019 README
drwxr-xr-x. 9 1001 1001     91 Dec 17 04:04 src      #源码
```

```Shell
cd conf
[root@centos7 conf]# ll
total 40
-rw-r--r--. 1 1001 1001 1077 Aug 13  2019 fastcgi.conf
-rw-r--r--. 1 1001 1001 1007 Aug 13  2019 fastcgi_params
-rw-r--r--. 1 1001 1001 2837 Aug 13  2019 koi-utf
-rw-r--r--. 1 1001 1001 2223 Aug 13  2019 koi-win
-rw-r--r--. 1 1001 1001 5231 Aug 13  2019 mime.types
-rw-r--r--. 1 1001 1001 2656 Aug 13  2019 nginx.conf # 核心配置文件
-rw-r--r--. 1 1001 1001  636 Aug 13  2019 scgi_params
-rw-r--r--. 1 1001 1001  664 Aug 13  2019 uwsgi_params
-rw-r--r--. 1 1001 1001 3610 Aug 13  2019 win-utf

[root@centos7 html]# ll
total 8
-rw-r--r--. 1 1001 1001 494 Aug 13  2019 50x.html
-rw-r--r--. 1 1001 1001 612 Aug 13  2019 index.html

[root@centos7 objs]# ll
total 3888
-rw-r--r--. 1 root root   17763 Dec 17 04:19 autoconf.err
-rw-r--r--. 1 root root   40485 Dec 17 04:19 Makefile
-rwxr-xr-x. 1 root root 3857136 Dec 17 04:20 nginx
-rw-r--r--. 1 root root    5327 Dec 17 04:20 nginx.8
-rw-r--r--. 1 root root    7363 Dec 17 04:19 ngx_auto_config.h
-rw-r--r--. 1 root root     657 Dec 17 04:19 ngx_auto_headers.h
-rw-r--r--. 1 root root    5975 Dec 17 04:19 ngx_modules.c
-rw-r--r--. 1 root root   32552 Dec 17 04:20 ngx_modules.o
drwxr-xr-x. 9 root root      91 Dec 17 04:19 src

[root@centos7 src]# ll
total 20
drwxr-xr-x. 2 1001 1001 4096 Dec 17 04:04 core   #核心源码
drwxr-xr-x. 3 1001 1001 4096 Dec 17 04:04 event  #事件
drwxr-xr-x. 4 1001 1001 4096 Dec 17 04:04 http
drwxr-xr-x. 2 1001 1001 4096 Dec 17 04:04 mail
drwxr-xr-x. 2 1001 1001   74 Dec 17 04:04 misc   #辅助代码
drwxr-xr-x. 3 1001 1001   18 Dec 17 04:04 os     #系统
drwxr-xr-x. 2 1001 1001 4096 Aug 13  2019 stream 

[root@centos7 http]# ls
modules                        ngx_http_file_cache.c              ngx_http_request.h               ngx_http_upstream_round_robin.h
ngx_http.c                     ngx_http.h                         ngx_http_script.c                ngx_http_variables.c
ngx_http_cache.h               ngx_http_header_filter_module.c    ngx_http_script.h                ngx_http_variables.h
ngx_http_config.h              ngx_http_parse.c                   ngx_http_special_response.c      ngx_http_write_filter_module.c
ngx_http_copy_filter_module.c  ngx_http_postpone_filter_module.c  ngx_http_upstream.c              v2
ngx_http_core_module.c         ngx_http_request_body.c            ngx_http_upstream.h
ngx_http_core_module.h         ngx_http_request.c                 ngx_http_upstream_round_robin.c

```

## 负载均衡

### 四层负载均衡

主要是为了将请求分流到不同服务器

F5 硬负载均衡 基于硬件、商业化

LVS 四层负载均衡

Haproxy 四层负载均衡

Nginx 四层负载均衡

### 七层负载均衡

基于应用层HTTP协议进行负载均衡

Nginx 七层负载均衡

Haproxy 七层负载均衡

apache 七层负载均衡

四层主要是tcp、udp转发请求，而不是处理请求
七层会处理请求，可以过滤、压缩、缓存等

### DNS 地域负载均衡

![DNS地域负载均衡](/postimages/DNS地域负载均衡.webp)

可以根据请求者地域，分配到最近的服务器
减少网络传输的损耗

## 使用Nginx搭建三台tomcat集群

tomcat1 :10.4.7.11

tomcat2 :10.4.7.12

tomcat3 :10.4.7.21

tomcat安装：10.4.7.{11,12,21}都要安装

```Shell
yum install tomcat -y
yum install tomcat-webapps tomcat-admin-webapps tomcat-docs-webapp tomcat-javadoc
#关闭防火墙
systemctl stop firewalld
systemctl disable firewalld
#启动tomcat
systemctl restart tomcat
```

10.4.7.129 (nginx服务器)上配置tomcat集群

vim imooc.conf

```javascript
upstream tomcats {
        server 10.4.7.11:8080;
        server 10.4.7.12:8080;
        server 10.4.7.21:8080;
        }


    server {
        listen       80;
        server_name  www.tomcats.com;
        location / {
            proxy_pass http://tomcats;
        }
        }
```

重启nginx

```Shell
/usr/local/nginx/sbin/nginx -s reload 
```



windwos配置hosts规则：

```Shell
10.4.7.129 www.tomcats.com
```

测试访问。

www.tomcats.com

请求会平均分配给后面三台tomcat.

参考：http://tengine.taobao.org/book/chapter_05.html

## 负载均衡之轮询

默认nginx使用的是轮询。交替出现。出现次数均等。

修改每台tomcat默认页面

/usr/share/tomcat/webapps/ROOT/index.jsp

第37行。home改成对应IP最后一位。

```html
 37 <span id="nav-home"><a href="${tomcatUrl}">12</a></span>
```

重启tomcat

```Shell
systemctl restart tomcat 
```

![nginx 轮询](/postimages/nginx 轮询.webp)

 11 12 21 会交替出现。

## 负载均衡之权重-加权轮询

weight 值越大，出现的次数越多（后端服务器被访问的次数就越多）。

配置在upstream 模块内。

```javascript
upstream tomcats {
        server 10.4.7.11:8080 weight=1;
        server 10.4.7.12:8080 weight=2;
        server 10.4.7.21:8080 weight=3;
        }
```

11:12:21 = 1:2:3

21出现几率最大。

## upstream 指令参数

max_conns :限制`*number*`到代理服务器的最大同时活动连接数.默认值为零，表示没有限制。如果启用了[idle keepalive](https://nginx.org/en/docs/http/ngx_http_upstream_module.html#keepalive)连接、多个[worker](https://nginx.org/en/docs/ngx_core_module.html#worker_processes)和[共享内存](https://nginx.org/en/docs/http/ngx_http_upstream_module.html#zone)，则代理服务器的 active 和 idle 连接总数可能会超过该`max_conns`值。允许最大连接数。

限制每台server的连接数，用于保护避免过载起到限流作用。

测试参考配置如下：

#worker 进程设置1个，便于测试观察成功的连接数。

```javascript
worker_processes 1 
upstream tomcats {
        server 10.4.7.11:8080 max_conns=2;
        server 10.4.7.12:8080 max_conns=2;
        server 10.4.7.21:8080 max_conns=2;
        }
```



slow_start:当节点恢复，不立即加入,而是等待 slow_start 后加入服务对列.(仅商业版支持)

测试参考配置如下：

```javascript
upstream tomcats {
        server 10.4.7.11:8080 weight=6 slow_start=60s;
        server 10.4.7.12:8080 weight=2;
        server 10.4.7.21:8080 weight=2;
        }
```

注意：

该参数不能使用在hash 和random load balancing中

如果在upstream中只有一台server，则该参数失效。

down :将服务器标记为永久不可用。

测试参考配置如下：

```javascript
upstream tomcats {
        server 10.4.7.11:8080 down;
        server 10.4.7.12:8080 weight=2;
        server 10.4.7.21:8080 weight=2;
        }
```



backup:将服务器标记为备份服务器,只有在其他的服务器都宕机以后，自己才会加入到集群中，被用户访问到。

该参数不能与 [hash](https://nginx.org/en/docs/http/ngx_http_upstream_module.html#hash)、[ip_hash](https://nginx.org/en/docs/http/ngx_http_upstream_module.html#ip_hash)和[随机](https://nginx.org/en/docs/http/ngx_http_upstream_module.html#random) 负载平衡方法一起使用。

```javascript
upstream tomcats {
        server 10.4.7.11:8080 backup;
        server 10.4.7.12:8080 weight=1;
        server 10.4.7.21:8080 weight=1;
        }
```



max_fails:失败几次，则标记server已宕机，剔出上游服务。

fail_timeout:表示失败的重试时间。

假设目前设置如下：

max_fails=2  fail_timeout=15s

则表示在15秒内请求某一server失败达到2次后，则认为该server已经挂了或者宕机了，随后再过15s，这15s内不会有新的请求到达刚刚挂掉的节点上，而是会请求到正常运作的server，15秒后会在有新请求尝试连接挂掉的server，如果还是失败，重复上一过程，直到恢复。

## 使用keepalive提高吞吐量

https://nginx.org/en/docs/http/ngx_http_upstream_module.html#keepalive

测试配置：

```javascript
upstream tomcats {
        server 10.4.7.11:8080 weight=1;
        server 10.4.7.12:8080 weight=1;
        server 10.4.7.21:8080 weight=1;
        keepalive 32; #配置长连接的数量，保持连接可以提高吞吐量
        }


    server {
        listen       80;
        server_name  www.tomcats.com;
        location / {
            proxy_pass http://tomcats;
            proxy_http_version 1.1; #代表上面长连接的版本号，（默认1.0，但1.0不是长链接）
            proxy_set_header Connection "";
        }
        }
```

## 负载均衡之ip_hash

hash算法

hash(ip) % node_counts = index

算出用户访问的服务器具体是哪一台

保证用户访问的服务器是同一台，保持会话一致

测试配置：

```javascript
upstream tomcats {
		ip_hash;
        server 10.4.7.11:8080;
        server 10.4.7.12:8080;
        server 10.4.7.21:8080;
        }
```



   10.4.7.110
   10.4.7.120        ---> hash(10 4 7)
   10.4.7.210

分段进行计算，同一片区域，会访问到一个里

### 负载均衡之**url_hash**

![url_hash](/postimages/url_hash.webp)

通过配置hash $request_uri
开启url_hash

## nginx控制浏览器缓存

![nginx缓存](/postimages/nginx缓存.webp)

1、设置静态内容的过期时间(expires time;)

```javascript
location  /static {
    alias  /home/naga;
    expires  10s;   
}
```

2、固定时间 expires @[time]

```Shell
expires  @22h30s;   代表晚上10:30pm失效，浏览器会自动计算时间差
```

3、 expires -[time]

```Shell
 expires  -1h;  距离现在的1小时之前就失效了，相当于不用缓存
```

4、expires eposh; 不使用缓存

5、expires off; 默认值，其实就是不管，由浏览器自己使用自己的默认缓存

6、expires max; 缓存不会过期，除非修改过。

## 上游服务静态内容缓存

keys_zone 是内存缓存 max_size 是硬盘缓存

```javascript
upstream xxx {  ...  }

# proxy_cache_path  设置缓存保存的地址，目录会自动创建
#   keys_zone 设置共享内存，以及占用的空间大小 ## 内存缓存 ##  (mycache是共享内存名，下面)
#   max_size 设置缓存大小
#   inactive 超过此时间，缓存自动清理
#   use_temp_path=off  关闭临时目录
proxy_cache_path  /usr/local/nginx/upstream_cache  keys_zone=mycache:50m  max_size=30g  inactive=8h  use_temp_path=off;

server {
  listen  80;
  server_name  www.naga.cn;
  
  #开启并且使用缓存
  proxy_cache  mycache;
  #针对200和304状态码的缓存设置过期时间
  proxy_cache_valid   200  304  8h;
}
```

## 使用nginx 配置https域名证书

1. 安装ssl模块

   要在nginx中配置https,及必须安装ssl模块，也就是‘http_ssl_module’.

   进入到nginx解压目录：nginx-1.16.1

   新增ssl模块（原来的模块需要保留）--with-http_ssl_module

```Shell
./configure \
--prefix=/usr/local/nginx \
--pid-path=/var/run/nginx/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-http_gzip_static_module \
--http-client-body-temp-path=/var/temp/nginx/client \
--http-proxy-temp-path=/var/temp/nginx/proxy \
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
--http-scgi-temp-path=/var/temp/nginx/scgi \
--with-http_ssl_module
```

编译和安装：

make 

make install 

## 配置ssl

1、云上申请证书，审核完成后将证书下载下来，nginx包含两个文件，一个crt证书文件，一个key密钥文件

2、将文件上传到云服务器，可以放在conf下

3、修改nginx.conf  (事先在nginx里面加入ssl模块    --with-http_ssl_module, 安装后通过  ./nginx -V 查看 )

```javascript
server {
    listen  443;
    server_name  localhost;
    
    # 开启ssl 
    ssl on; 
    # 配置ssl证书   因为放在conf下的，和nginx.conf同一路径，就这样写就行
    ssl_certificate 1_www.imoocdsp.com_bundle.crt; 
    # 配置证书秘钥 
    ssl_certificate_key 2_www.imoocdsp.com.key; 
    # ssl会话cache 
    ssl_session_cache shared:SSL:1m; 
    # ssl会话超时时间 
    ssl_session_timeout 5m; 
    # 配置加密套件，写法遵循 openssl 标准 
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; 
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE; 
    ssl_prefer_server_ciphers on;
}
### 将http的请求转发到https
server {
    listen 80;
    server_name localhost;
    
    # 排除法
    if ($http_name ~* "^online$" ) {
        rewrite ^/(.*)$ https://localhost/online/ permanent;
    }
    rewrite ^ https://$host$1 permanent;
}
```

## 动静分离

1.分布式

2.前后端解耦

3.静态归nginx

4.接口服务化

静态数据：css/js/html/images/audios/videos/...

动态数据：得到的响应可能会和上次不同。

动静分离的问题

跨域：

springboot

nginx 

jsonp

分布式会话： 分布式缓存中间件Redis

## nginx高可用HA keepalived 双机主备

keepalived 使用的是虚拟路由冗余协议 VRRP ，这个协议的特点如下：

1、解决内网单机故障的路由协议，

2、用于构建过个路由MASTER BACKUP，将几台提供相同服务的路由器组成一个路由器组。一个路由就是nginx

3、虚拟IP-VIP（Virtual IP Address）

过程：用户不直接访问nginx，访问虚拟IP，这个虚拟IP是绑定了nginx的master节点，会根据这个关系解析出来结果。 master主节点挂了，心跳检测到后会让虚拟IP找到备用机，多个备用机会根据权重选择。

此外，主备节点的硬件配置应一样。

## 安装keepalived

解压后进入文件夹进行配置，类似nginx，但是需要用 --sysconf=/etc指定配置文件地址

```Shell
yum -y install libnl libnl-devel

./configure --prefix=/usr/local/keepalived  --sysconf=/etc

make && make install
```

prefix：keepalived安装的位置

sysconf：keepalived核心配置文件所在位置，固定位置，改成其他位置则keepalived启动不了。会报错。

libnl libnl-devel 是libnl/libnl-3依赖包。

### 核心配置文件：

```javascript
!Configuration File for keepalived

# 全局配置
global_defs {
   # 节点故障，切换了节点之后会通知管理员，下面还要配置邮箱的协议等，可以不要
   notification_email {
     123456@qq.com
   }
   notification_email_from Alexandre.Cassen@firewall.loc
   smtp_server 192.168.200.1
   smtp_connect_timeout 30
   
   # 路由ID：当前安装keepalived节点主机的标识符，全局唯一
   router_id naga_170
   
   vrrp_skip_check_adv_addr
   vrrp_strict
   vrrp_garp_interval 0
   vrrp_gna_interval 0
}

#基于VRRP协议的对象，计算机节点
vrrp_instance VI_1 {
    state MASTER            # 表示当前为nginx的master主节点
    interface eth0          # 当前实例绑定的网卡， 用 ip addr查看
    virtual_router_id 51     #虚拟路由ID，主备节点需要一致
    priority 100            #权重，备机优先级越高就成为新的master
    advert_int 1            # 主备心跳检查，时间间隔1s
    authentication {        # 认证授权，防止非法节点。每个节点一样就可以
        auth_type PASS      
        auth_pass 1111
    }
    virtual_ipaddress {     # 虚拟IP，用一个就可以了
        192.168.17.17
        #192.168.200.17
        #192.168.200.18
    }
}

#下面的都可以不要
virtual_server 192.168.200.100 443 {
    delay_loop 6
    lb_algo rr
    lb_kind NAT
    persistence_timeout 50
    protocol TCP

    real_server 192.168.201.100 443 {
        weight 1
        SSL_GET {
            url {
              path /
              digest ff20ad2481f97b1754ef3e12ecd3a9cc
            }
            url {
              path /mrtg/
              digest 9b3a0c85a887a256d6939da88aabd8cd
            }
            connect_timeout 3
            retry 3
            delay_before_retry 3
        }
    }
}

virtual_server 10.10.10.2 1358 {
    delay_loop 6
    lb_algo rr
    lb_kind NAT
    persistence_timeout 50
    protocol TCP

    sorry_server 192.168.200.200 1358

    real_server 192.168.200.2 1358 {
        weight 1
        HTTP_GET {
            url {
              path /testurl/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            url {
              path /testurl2/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            url {
              path /testurl3/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            connect_timeout 3
            retry 3
            delay_before_retry 3
        }
    }

    real_server 192.168.200.3 1358 {
        weight 1
        HTTP_GET {
            url {
              path /testurl/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334c
            }
            url {
              path /testurl2/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334c
            }
            connect_timeout 3
            retry 3
            delay_before_retry 3
        }
    }
}

virtual_server 10.10.10.3 1358 {
    delay_loop 3
    lb_algo rr
    lb_kind NAT
    persistence_timeout 50
    protocol TCP

    real_server 192.168.200.4 1358 {
        weight 1
        HTTP_GET {
            url {
              path /testurl/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            url {
              path /testurl2/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            url {
              path /testurl3/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            connect_timeout 3
            retry 3
            delay_before_retry 3
        }
    }

    real_server 192.168.200.5 1358 {
        weight 1
        HTTP_GET {
            url {
              path /testurl/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            url {
              path /testurl2/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            url {
              path /testurl3/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            connect_timeout 3
            retry 3
            delay_before_retry 3
        }
    }
}
```

示例：

```javascript
#### 第一台 ####
! Configuration File for keepalived
#全局变量-路由id，标识主机id
global_defs {
   router_id keep_140
}
#计算机节点
vrrp_instance VI_1 {
    state MASTER	#标示当前机器为主节点，从节点BACKUP
    interface ens33	#当前网卡
    virtual_router_id 51	#保持主备机一致
    priority 100	#权重，最高的当选下一任主机
    advert_int 1	#检查时间间隔1s
    authentication {	#认证授权，防止非法节点进入
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {	#虚拟ip
        10.4.7.211
    }
}

#### 第二台 ####
! Configuration File for keepalived
#全局变量-路由id，标识主机id
global_defs {
   router_id keep_141
}
#计算机节点
vrrp_instance VI_1 {
    state BACKUP	#标示当前机器为从节点
    interface ens33	#当前网卡
    virtual_router_id 51	#保持主备机一致
    priority 80	#权重，最高的当选下一任主机
    advert_int 1	#检查时间间隔1s
    authentication {	#认证授权，防止非法节点进入
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {	#虚拟ip，保持和主节点一致
        10.4.7.211
    }
}
```

测试：

两台机器nginx 和keeplived进程都在。两台一主一备的目的是，主服务器keeplived挂了，vip切到备服务器上，业务不中断。默认vip是在主上生效的。主备的keepalived进程都在，vip会在主上生效。vip切到备服务器上时.主服务器上的keepalived恢复了。vip会再次切回到主服务器上。

### keepalived配置Nginx自动启动

这时如果nginx挂了，keepalived没有挂，还是会无法访问。

1.脚本（主从都配置）

cd /etc/keepalived
vim check_nginx_alive.sh

```Shell
#!/bin/bash
A=`ps -C nginx --no-header | wc -l`
# 进程数是0的话，就尝试重启nginx
if [ $A -eq 0 ]; then
    /usr/local/nginx/sbin/nginx
    # 等一会儿再次检查ngxin，如果nginx没有启动成功，则停止当前机器的keepalived，使用备用机器
    sleep 3
    if [ `ps -C nginx --no-header | wc -l` -eq 0 ]; then
        killall keepalived  
    fi
fi      
```

执行这个脚本后，会检测nginx，挂掉会重启nginx。

我们将它配置到keepalived配置文件里。

```javascript
! Configuration File for keepalived

global_defs {
   router_id keep_140
}
vrrp_script check_nginx_alive {
        script "/etc/keepalived/check_nginx_alive.sh"
        interval 2 #每隔两秒钟运行脚本检测
        weight 10 #脚本运行成功，升级权重+10
}
vrrp_instance VI_1 {
    state MASTER
    interface ens33
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        10.4.7.211
    }
    #在此执行上面的定时器
    track_script{
        check_nginx_alive
        }
}
```

#### 双机主备缺点

这样我们就能保证高可用的架构了，但是双机主备会有资源浪费的问题，主机永远不挂机，备机白干活儿。

#### Keepalived 双主热备

![nginx双主1](/postimages/nginx双主1.webp)

![nginx双主2](/postimages/nginx双主2.webp)

双机使用两个虚拟IP，互为主备。

使用DNS轮询两个虚拟IP进行访问，这样两台服务器都可以使用了。解析域名的时候，给域名解析两个云服务器的ip地址，再均衡权重即可。

配置（原主节点）：

```javascript
! Configuration File for keepalived

global_defs {
   router_id keep_140
}
vrrp_script check_nginx_alive {
        script "/etc/keepalived/check_nginx_alive.sh"
        interval 2 #每隔两秒钟运行脚本检测
        weight 10 #脚本运行成功，升级权重+10
}
#211主节点
vrrp_instance VI_1 {
    state MASTER
    interface ens33
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        10.4.7.211
    }
    track_script{
        check_nginx_alive
        }
}

#212从节点
vrrp_instance VI_2 {        #名字要改
    state BACKUP
    interface ens33
    virtual_router_id 52        #换了一组实例，改
    priority 80
    advert_int 1
    authentication {
        auth_type PASS      
        auth_pass 1111
    }
    track_script {
        check_nginx_alive 
    }
    virtual_ipaddress {
        10.4.7.212           #VIP更改  虚拟ip  virtual IP
    }
    track_script{
    	check_nginx_alive
    }
}
```

配置（原从节点）：

```javascript
! Configuration File for keepalived

global_defs {
   router_id keep_141
}
vrrp_script check_nginx_alive {
        script "/etc/keepalived/check_nginx_alive.sh"
        interval 2 #每隔两秒钟运行脚本检测
        weight 10 #脚本运行成功，升级权重+10
}
#211从节点
vrrp_instance VI_1 {
    state BACKUP
    interface ens33
    virtual_router_id 51
    priority 80
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        10.4.7.211
    }
        track_script {
        check_nginx_alive 
    }
}

#212主节点
vrrp_instance VI_2 {           #第二组实例
    state MASTER            
    interface ens33          
    virtual_router_id 52        #第二组router_id
    priority 100            
    advert_int 1            
    authentication {
        auth_type PASS      
        auth_pass 1111
    }
    virtual_ipaddress {     
        10.4.7.212           #改为第二个虚拟ip
    }
        track_script {
        check_nginx_alive 
    }
}
```
















