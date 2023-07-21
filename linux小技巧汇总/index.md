# linux小技巧汇总


### Linux小技巧汇总

### 1.超时不操作自动退出登录TMOUT

```sh
vi /etc/profile 
最后一行加上
TMOUT=90
退出当前用户使配置生效
exit
重新登录
```

查看是否生效

```shell
set | grep -i tmout
TMOUT=90
```

生效了！！！不操作等待90s会自动退出当前用户！！

如何取消呢？

只要将`/etc/profile`文件中`TMOUT=90`删除，然后退出使之生效。再次测试即可。




