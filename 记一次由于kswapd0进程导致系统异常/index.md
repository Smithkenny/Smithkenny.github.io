# 记一次由于kswapd0进程导致cpu占满


## 前言

服务器安装pve后发现cpu占用率过高有点不正常，于是查其原因。

参考这个博主的文章。发现现象一致。

`https://blog.csdn.net/jzz601264258/article/details/105850816`

top查看cpu占用率

```bash
root@www:~# top
top - 09:58:10 up 35 min,  2 users,  load average: 8.00, 8.00, 7.09
Tasks: 220 total,   1 running, 219 sleeping,   0 stopped,   0 zombie
%Cpu(s): 99.2 us,  0.8 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :  15651.4 total,   8695.9 free,   6572.6 used,    382.9 buff/cache
MiB Swap:   8192.0 total,   8192.0 free,      0.0 used.   8748.4 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                                                                     
   1334 root      20   0 3375880   5092      4 S 800.0   0.0 255:49.67 kswapd0                                                                                     
      1 root      20   0  166460  10756   7720 S   0.0   0.1   0:01.27 systemd                   
```

发现`kswapd0`进程cpu使用率高达`800%`。我的服务器配置是8核16G。

详细查看该进程下的所有线程

```bash
root@www:~# top -Hp 1334 
top - 09:58:39 up 35 min,  2 users,  load average: 8.05, 8.01, 7.13
Threads:  14 total,   8 running,   6 sleeping,   0 stopped,   0 zombie
%Cpu(s): 99.6 us,  0.4 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :  15651.4 total,   8697.7 free,   6570.8 used,    382.9 buff/cache
MiB Swap:   8192.0 total,   8192.0 free,      0.0 used.   8750.2 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                                                                     
   1431 root      20   0 3375880   5092      4 R  99.9   0.0  32:23.46 kswapd0                                                                                     
   1429 root      20   0 3375880   5092      4 R  99.7   0.0  32:21.50 kswapd0                                                                                     
   1433 root      20   0 3375880   5092      4 R  99.7   0.0  32:23.20 kswapd0                                                                                     
   1434 root      20   0 3375880   5092      4 R  99.7   0.0  32:22.80 kswapd0                                                                                     
   1432 root      20   0 3375880   5092      4 R  99.3   0.0  32:22.95 kswapd0                                                                                     
   1430 root      20   0 3375880   5092      4 R  99.0   0.0  32:21.44 kswapd0                                                                                     
   1435 root      20   0 3375880   5092      4 R  99.0   0.0  32:23.55 kswapd0                                                                                     
   1436 root      20   0 3375880   5092      4 R  99.0   0.0  32:23.23 kswapd0                                                                                     
   1334 root      20   0 3375880   5092      4 S   0.0   0.0   0:00.15 kswapd0                                                                                     
   1335 root      20   0 3375880   5092      4 S   0.0   0.0   0:00.84 kswapd0                                                                                     
   1336 root      20   0 3375880   5092      4 S   0.0   0.0   0:00.00 kswapd0                                                                                     
   1337 root      20   0 3375880   5092      4 S   0.0   0.0   0:00.00 kswapd0                                                                                     
   1338 root      20   0 3375880   5092      4 S   0.0   0.0   0:00.00 kswapd0                                                                                     
   1339 root      20   0 3375880   5092      4 S   0.0   0.0   0:00.00 kswapd0   
```

于是查看系统外部链接

```bash
root@www:~# netstat -aplt
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:sunrpc          0.0.0.0:*               LISTEN      1/init              
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN      1273/sshd: /usr/sbi 
tcp        0      0 localhost.localdom:smtp 0.0.0.0:*               LISTEN      1129/master         
tcp        0      0 localhost.localdomai:85 0.0.0.0:*               LISTEN      1280/pvedaemon      
tcp        0      1 www.pve.com:34882       45.9.148.99:https       SYN_SENT    1305/rsync          
tcp        0    280 www.pve.com:ssh         192.168.1.110:49909     ESTABLISHED 2629/sshd: root@pts 
tcp        0      0 www.pve.com:54690       45.9.148.125:http       ESTABLISHED 1334/./kswapd0              
tcp        0      0 www.pve.com:ssh         192.168.1.182:3219      ESTABLISHED 1314/sshd: root@pts 
tcp6       0      0 [::]:sunrpc             [::]:*                  LISTEN      1/init              
tcp6       0      0 [::]:ssh                [::]:*                  LISTEN      1273/sshd: /usr/sbi 
tcp6       0      0 [::]:3128               [::]:*                  LISTEN      1295/spiceproxy     
tcp6       0      0 ip6-localhost:smtp      [::]:*                  LISTEN      1129/master         
tcp6       0      0 [::]:8006               [::]:*                  LISTEN      1284/pveproxy      
```

发现有两个外部IP正在和本机通信。

使用`www.ip138.com`查看45.9.148.125 、45.9.148.99IP，归属地是荷兰。如此便确定了是挖矿程序。

查看kswapd0 和 rsync 进程对应的文件路径。

```bash
root@www:~# ls -l /proc/1334/exe
lrwxrwxrwx 1 root root 0 Nov 16 10:00 /proc/1334/exe -> /root/.configrc/a/kswapd0
root@www:~# ls -l /proc/1305/exe
lrwxrwxrwx 1 root root 0 Nov 16 10:01 /proc/1305/exe -> /usr/bin/perl
```

先把/root/.configrc 重命名为/root/configrc,并压缩一份拷贝到本机。然后删除该文件夹。

```bash
mv /root/.configrc /root/configrc
tar -zcvf /root/configrc.tgz /root/configrc
rm -rf /root/configrc
```

发现有定时任务定期执行该程序，即时服务器重启也会立即执行对应脚本。

```bash
root@www:~# crontab -l 
1 1 */2 * * /root/.configrc/a/upd>/dev/null 2>&1
@reboot /root/.configrc/a/upd>/dev/null 2>&1
5 8 * * 0 /root/.configrc/b/sync>/dev/null 2>&1
@reboot /root/.configrc/b/sync>/dev/null 2>&1  
0 0 */3 * * /tmp/.X25-unix/.rsync/c/aptitude>/dev/null 2>&1
```

清除定时任务。

回想现象发生的原因：

- root密码设置过于简单
- 参考了一篇文章`https://www.cpci.dev/pve-cloud-init-and-cloud-image/`下载了网上的云镜像虚拟机模板然后就中招了。

处理方式：

设置较复杂的root密码，追踪到挖矿进程对应的配置文件所在的文件夹，删除掉。重启服务器。


