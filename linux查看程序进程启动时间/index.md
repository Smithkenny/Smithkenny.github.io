# 

使用以下命令查看进程启动时间

```
 ps -eo lstart,cmd  | grep sip 
```

结果：

```
[root@video_end /var/log]$  ps -eo lstart,cmd  | grep sip 
Mon Jan 29 14:12:46 2024 /etc/jtsec/sipholeWatchDog/sipholeWatchDog
Tue Feb  6 21:20:04 2024 /usr/sbin/siphole_end
Wed Feb  7 20:39:46 2024 grep --color=auto sip
```

