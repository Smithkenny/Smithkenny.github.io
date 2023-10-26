# Linux日志大文件切割


## **1.根据行数切割**

如下以一个372M大小的日志文件做切割演示，每一个文件按照50000行做切割，指定文件名为split-line，-d参数以数字的方式显示。

```sh
#源文件大小
[root@localhost /tmpdisk/test]$ ls -lh
总用量 372M
-rw-------. 1 root root 372M 10月 26 09:08 messages
#按行切割
[root@localhost /tmpdisk/test]$ split -l 50000 -d --verbose messages split-line 
#查看大小
[root@localhost /tmpdisk/test]$ ls -lh
总用量 743M
-rw-------. 1 root root 372M 10月 26 09:08 messages
-rw-r--r--. 1 root root 4.5M 10月 26 09:21 split-line00
-rw-r--r--. 1 root root 4.2M 10月 26 09:21 split-line01
-rw-r--r--. 1 root root 7.7M 10月 26 09:22 split-line02
-rw-r--r--. 1 root root 4.2M 10月 26 09:22 split-line03
-rw-r--r--. 1 root root 4.2M 10月 26 09:22 split-line04
-rw-r--r--. 1 root root 4.2M 10月 26 09:22 split-line05
-rw-r--r--. 1 root root 4.2M 10月 26 09:22 split-line06
-rw-r--r--. 1 root root 4.2M 10月 26 09:22 split-line07
-rw-r--r--. 1 root root 4.2M 10月 26 09:22 split-line08
-rw-r--r--. 1 root root 4.2M 10月 26 09:22 split-line09
-rw-r--r--. 1 root root 4.3M 10月 26 09:22 split-line10
```

##  **2.根据大小切割**

除了按照行数切割之外，split还支持通过文件大小进行切割，通过指定-b参数指定文件大小进行切割，文件大小单位支持K, M, G, T, P, E, Z，如下以切割为50M演示文件切割过程。

```sh
[root@localhost /tmpdisk/test]$ split -b 50M -d --verbose messages split-size
正在创建文件"split-size00"
正在创建文件"split-size01"
正在创建文件"split-size02"
正在创建文件"split-size03"
正在创建文件"split-size04"
正在创建文件"split-size05"
正在创建文件"split-size06"
正在创建文件"split-size07"
[root@localhost /tmpdisk/test]$ ls -lh 
总用量 743M
-rw-------. 1 root root 372M 10月 26 09:08 messages
-rw-r--r--. 1 root root  50M 10月 26 09:25 split-size00
-rw-r--r--. 1 root root  50M 10月 26 09:25 split-size01
-rw-r--r--. 1 root root  50M 10月 26 09:25 split-size02
-rw-r--r--. 1 root root  50M 10月 26 09:25 split-size03
-rw-r--r--. 1 root root  50M 10月 26 09:25 split-size04
-rw-r--r--. 1 root root  50M 10月 26 09:25 split-size05
-rw-r--r--. 1 root root  50M 10月 26 09:25 split-size06
-rw-r--r--. 1 root root  22M 10月 26 09:25 split-size07
```

## **3.多文件合并**

split是用户将大文件切割为多个小文件，如果需要将多个小文件合并为一个文件怎么处理呢？可以使用文件重定向方式实现，如下演示两个小文件合并为一个文件。

```sh
[root@oneway_out /tmpdisk/test]$ cat split-size00 split-size01 > messages1
[root@oneway_out /tmpdisk/test]$ ls
messages  messages1  split-size00  split-size01  split-size02  split-size03  split-size04  split-size05  split-size06  split-size07
[root@oneway_out /tmpdisk/test]$ ls -lh
总用量 843M
-rw-------. 1 root root 372M 10月 26 09:08 messages
-rw-r--r--. 1 root root 100M 10月 26 09:27 messages1
-rw-r--r--. 1 root root  50M 10月 26 09:25 split-size00
-rw-r--r--. 1 root root  50M 10月 26 09:25 split-size01
-rw-r--r--. 1 root root  50M 10月 26 09:25 split-size02
-rw-r--r--. 1 root root  50M 10月 26 09:25 split-size03
-rw-r--r--. 1 root root  50M 10月 26 09:25 split-size04
-rw-r--r--. 1 root root  50M 10月 26 09:25 split-size05
-rw-r--r--. 1 root root  50M 10月 26 09:25 split-size06
-rw-r--r--. 1 root root  22M 10月 26 09:25 split-size07
```

合并方式通过读取文件的方式+输出重定向，对于大文件一样会存在性能的问题，建议根据需要使用。

## **4.多文件同一关键字筛选**

```sh
[root@localhost /tmpdisk/test]$ grep faild split-size* 
split-size01:2023-09-07T10:54:01.447068+08:00 localhost jman[691]: init mysql default password faild
split-size01:2023-10-07T13:27:47.356650+08:00 localhost jman[695]: init mysql default password faild
```

[参考]([微信公众平台 (qq.com)](https://mp.weixin.qq.com/s/rXMz3TlRjrmg-tD2kkXJug))
