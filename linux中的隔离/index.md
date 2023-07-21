# linux中的隔离技术

# linux中的隔离技术

隔离的意义在于处于不同命名空间下的进程不能看到对方的信息。命名空间在Liunx内核中，不是一块分配，你可以定制隔离程度，比如只隔离网络，但不隔离进程ID空间。下面是命名空间可以隔离的七种类型。

| 名称   | 内核版本 | 隔离内容                   |
| ------ | -------- | -------------------------- |
| UTS    | 3.0      | 主机名和域名               |
| MNT    | 3.8      | 挂载点（文件系统）         |
| IPC    | 3.0      | 信号量、消息队列和共享内存 |
| PID    | 3.8      | 进程编号                   |
| NET    | 3.0      | 网络设备、网络栈、端口等   |
| USER   | 3.8      | 用户和用户组               |
| Cgroup | 4.6      | 以根的形式呈现出来         |

`3.10.0`内核支持6种类型的`namespace`

```shell
[root@localhost ~]# ll /proc/$$/ns
total 0
lrwxrwxrwx 1 root root 0 Oct 16 12:50 ipc -> ipc:[4026531839]
lrwxrwxrwx 1 root root 0 Oct 16 12:50 mnt -> mnt:[4026531840]
lrwxrwxrwx 1 root root 0 Oct 16 12:50 net -> net:[4026531956]
lrwxrwxrwx 1 root root 0 Oct 16 12:50 pid -> pid:[4026531836]
lrwxrwxrwx 1 root root 0 Oct 16 12:50 user -> user:[4026531837]
lrwxrwxrwx 1 root root 0 Oct 16 12:50 uts -> uts:[4026531838]
```

**`$$`代表Shell本身的PID（ProcessID），即当前进程的PID。**

而`ubuntu 5.4.0`内核的支持7种类型的`namespace`

```shell
lrwxrwxrwx 1 root root 0 Oct 16 13:03 cgroup -> 'cgroup:[4026531835]'
lrwxrwxrwx 1 root root 0 Oct 16 13:03 ipc -> 'ipc:[4026531839]'
lrwxrwxrwx 1 root root 0 Oct 16 13:03 mnt -> 'mnt:[4026531840]'
lrwxrwxrwx 1 root root 0 Oct 16 13:03 net -> 'net:[4026531992]'
lrwxrwxrwx 1 root root 0 Oct 16 13:03 pid -> 'pid:[4026531836]'
lrwxrwxrwx 1 root root 0 Oct 16 13:51 pid_for_children -> 'pid:[4026531836]'
lrwxrwxrwx 1 root root 0 Oct 16 13:03 user -> 'user:[4026531837]'
lrwxrwxrwx 1 root root 0 Oct 16 13:03 uts -> 'uts:[4026531838]'
```

- 上述六个文件分别代表对应namespace的文件描述符，中括号内对应其inode number
- 如果两个进程的inode number一致，说明这两个进程属于同一个namespace

**如何创建一个PID namespace**

```shell
# --mount-proc 如何不带这个参数，ps aux 依然会读取主机其他进程
# -f --fork 让/bin/bash命令成为unshare的子进程
# -p --pid 代表pid命名空间
[root@bogon ~]# unshare --mount-proc -fp /bin/bash
[root@bogon ~]# ps aux
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.0  0.0 115544  1992 pts/1    S    06:59   0:00 /bin/bash
root         10  0.0  0.0 155472  1844 pts/1    R+   06:59   0:00 ps aux
```

**REF**

```shell
https://blog.csdn.net/chenleiking/article/details/87915185
```




