# centos7使用systemd管理程序


## centos7使用systemd管理程序

systemd的配置文件主要放在`/usr/lib/systemd/system`目录，也可能在`/etc/systemd/system`目录。

学习下sshd的系统配置文件写法：

```bash
$cat sshd.service
[Unit]
Description=OpenSSH server daemon
Documentation=man:sshd(8) man:sshd_config(5)
After=network.target sshd-keygen.service
Wants=sshd-keygen.service
[Service]
Type=forking
PIDFile=/var/run/sshd.pid
EnvironmentFile=/etc/sysconfig/sshd
ExecStart=/usr/sbin/sshd $OPTIONS
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
RestartSec=42s
[Install]
WantedBy=multi-user.target
```

简单解释下：

- [Unit] 区块：启动顺序与依赖关系。
  - Description：当前配置文件的描述信息。
  - Documentation：帮助信息。
  - After：表示当前服务是在那个服务后面启动，一般定义为网络服务启动后启动
  - Wants：表示 sshd.service 与 sshd-keygen.service 之间存在” 弱依赖” 关系，即如果”sshd-keygen.service” 启动失败或停止运行，不影响 sshd.service 继续执行。

- [Service] 区块：启动行为
  - Type：定义启动类型。详细[了解点此](https://wiki.archlinux.org/index.php/systemd_(简体中文)#服务类型)。
  - PIDFile：服务的 pid 文件路径。
  - EnvironmentFile：指定当前服务依赖的环境参数文件。
  - ExecStart：定义启动进程时执行的命令。
  - ExecReload：重启服务时执行的命令
  - KillMode：定义 Systemd 如何停止 sshd 服务。
  - Restart：定义了 sshd 退出后，Systemd 的重启方式。
  - RestartSec：表示 Systemd 重启服务之前，需要等待的秒数。上面的例子设为等待 42 秒。
- [Install] 区块：定义如何安装这个配置文件，即怎样做到开机启动。
  - WantedBy：表示该服务所在的 Target。multi-user.target 表明当系统以多用户方式（默认的运行级别）启动时，这个服务需要被自动运行。

配置一个自己的系统服务

日常启动程序方式：

```sh
./etc/test/sipholeWatchDog/sipholeWatchDog
```

现在添加配置通过`systemd`来管理

```sh
vi /usr/lib/systemd/system/sipholeWatchDog.service

添加如下配置：
[Unit]
Description=sipholeWatchDog
After= syslog.target network.target jtsec-firstrun.service
[Service]
Type=forking
ExecStart=/etc/test/sipholeWatchDog/sipholeWatchDog

UMask=0007
RestartSec=10
Restart=always
[Install]
WantedBy=multi-user.target
```

加载单元

```sh
systemctl daemon-reload
```

启动服务并查看服务状态

```sh
systemctl start sipholeWatchDog
systemctl status sipholeWatchDog
● sipholeWatchDog.service - sipholeWatchDog
   Loaded: loaded (/usr/lib/systemd/system/sipholeWatchDog.service; enabled; vendor preset: disabled)
   Active: active (running) since 二 2023-01-03 11:10:57 CST; 14min ago
 Main PID: 22088 (sipholeWatchDog)
   CGroup: /system.slice/sipholeWatchDog.service
           ├─22088 /etc/test/sipholeWatchDog/sipholeWatchDog
           ├─22269 /etc/siphole/siphole_back -c /etc/siphole/siphole_back_5.conf
           └─22281 journalctl _PID=22269 -f

1月 03 11:24:59 video_back siphole_back[22269]: msp-P28181: 0:videoSrvId:00:RUN: MESSAGE: CmdType: RecordInfo Devices: 3301...001389
1月 03 11:25:03 video_back siphole_back[22269]: msp-P28181: 0:videoSrvId:00:Find Route ...
1月 03 11:25:03 video_back siphole_back[22269]: msp-P28181: 0:videoSrvId:00:RUN: MESSAGE: CmdType: RecordInfo Devices: 3301...001389
1月 03 11:25:11 video_back siphole_back[22269]: msp-P28181: 0:videoSrvId:00:Find Route ...
1月 03 11:25:11 video_back siphole_back[22269]: msp-P28181: 0:videoSrvId:00:RUN: MESSAGE: CmdType: RecordInfo Devices: 3301...001391
1月 03 11:25:12 video_back siphole_back[22269]: msp-P28181: 0:videoSrvId:00:Find Route ...
1月 03 11:25:12 video_back siphole_back[22269]: msp-P28181: 0:videoSrvId:00:RUN: MESSAGE: CmdType: RecordInfo Devices: 3301...001391
1月 03 11:25:13 video_back siphole_back[22269]: RUN:plugin/p28181.c 636:videoSrvId:00:Total devices flow: 20 bytes. 0 medias.
1月 03 11:25:14 video_back siphole_back[22269]: msp-P28181: 0:videoSrvId:00:Find Route ...
1月 03 11:25:14 video_back siphole_back[22269]: msp-P28181: 0:videoSrvId:00:RUN: MESSAGE: CmdType: RecordInfo Devices: 3301...001391
Hint: Some lines were ellipsized, use -l to show in full.

```

可以加入开机启动

```sh
systemctl enable sipholeWatchDog
```

#### `systemctl`命令整理

- 显示所有单元状态

```sh
systemctl 或 systemctl list-units
```

- 只显示服务单元的状态

```sh
systemctl --type=service
```

- 验证 sshd 服务当前是否活动

```sh
systemctl is-active sshd
```

- 启动，停止和重启 sshd 服务

```sh
systemctl start sshd.service
systemctl stop sshd.service
systemctl restart sshd.service
```

- 重新加载配置

```sh
systemctl reload sshd.service
```

- 列出活动状态的所有服务单元

```sh
systemctl list-units --type=service
```

- 列出所有服务单元

```sh
systemctl list-units --type=service --all
```

- 查看所有的服务单元是否为开机自启

```sh
systemctl list-unit-files --type=service 
```

- 列出依赖的单元

```sh
systemctl list-dependencies sshd
```

- 验证 sshd 服务是否开机启动

```sh
systemctl is-enabled sshd
```

- 关闭开机自启

```sh
systemctl disable network
```

- 启用 network 开机自启。

```sh
systemctl enable network
```

#### systemd日志管理

systemd 提供了自己日志系统（logging system），称为 journal. 使用 systemd 日志，无需额外安装日志服务（syslog）。读取日志的命令：

- 查看所有日志 (默认显示本次启动的所有日志)

```sh
journalctl
```

- 查看本次启动的所有日志也可以使用

```sh
journalctl -b
```

- 查看内核日志

```sh
journalctl -k
```

- 根据服务筛选

```sh
journalctl -u sshd
```

- 根据进程 ID 查询

```sh
journalctl _PID=1
```

- 查看日志占用的磁盘空间

```sh
journalctl --disk-usage
```

- 设置日志占用的空间

```sh
journalctl --vacuum-size=500M
```

#### Linux log日志占用过大清理方法

[Linux](https://www.uedbox.com/post/tag/linux/)使用`df -h`检查磁盘文件，可以看到`/run`目录下有日志目录`/run/log/journal`，占用了数G空间。

或者直接在相应目录下执行`du --max-depth=1 -h`

#### Linux log 日志清理

检查当前journal磁盘占用量

```sh
journalctl --disk-usage
```

清理方法可以采用按照日期清理，或者按照允许保留的容量清理，只保存7天的日志，最大500M。

```sh
journalctl --vacuum-time=7d
journalctl --vacuum-size=500M
```

如果要手工删除日志文件，则在删除前需要先轮转一次journal日志。

```sh
systemctl kill --kill-who=main --signal=SIGUSR2 systemd-journald.service
```

要启用日志限制持久化配置，可以修改 `/etc/systemd/journald.conf`

```sh
SystemMaxUse=16M
ForwardToSyslog=no
```

然后重启

```sh
systemctl restart systemd-journald.service
```

检查journal是否运行正常以及日志文件是否完整无损坏

```sh
journalctl --verify
```




