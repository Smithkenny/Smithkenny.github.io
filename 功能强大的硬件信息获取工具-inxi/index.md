# 功能强大的硬件信息获取工具-inxi


## 安装inxi

```
$ sudo apt-get install inxi   [On Debian/Ubuntu/Linux Mint] 
$ sudo yum install inxi       [On CentOs/RHEL/Fedora] 
$ sudo dnf install inxi       [On Fedora 22+] 
```

**查看 Inxi 所有依赖和推荐的应用，以及各种目录，并显示需要安装哪些包来支持给定的功能。**

```
$ inxi --recommends 
```

输出如下

```sh
inxi will now begin checking for the programs it needs to operate.

Check inxi --help or the man page (man inxi) to see what options are 
available. 
------------------------------------------------------------------------------
Test: core tools:

Perl version:  5.016003
Current shell: 
Default shell: bash
sh links to: /usr/bin/bash
Package manager: rpm
------------------------------------------------------------------------------
Test: required system directories:
 
/proc: ........................................................... Present
/sys: ............................................................ Present
 
All required system directories are present
------------------------------------------------------------------------------
Test: recommended system programs:
 
Note: IPMI sensors are generally only found on servers. To access that data, 
you only need one of the ipmi items. 
 
blockdev: --admin -p/-P (filesystem blocksize).................... Present
bt-adapter: -E bluetooth data (if no hciconfig)................... Missing
dig: -i wlan IP................................................... Present
dmidecode: -M if no sys machine data; -m.......................... Present
doas: -Dx hddtemp-user; -o file-user (alt for sudo)............... Missing
fdisk: -D partition scheme (fallback)............................. Present
file: -o unmounted file system (if no lsblk)...................... Present
fruid_print: -M machine data, Elbrus only......................... Missing
hciconfig: -E bluetooth data (deprecated, good report)............ Present
hddtemp: -Dx show hdd temp, if no drivetemp module................ Present
ifconfig: -i ip LAN (deprecated).................................. Present
ip: -i ip LAN..................................................... Present
ipmitool: -s IPMI sensors (servers)............................... Present
ipmi-sensors: -s IPMI sensors (servers)........................... Present
lsblk: -L LUKS/bcache; -o unmounted file system (best option)..... Present
lsusb: -A usb audio; -J (optional); -N usb networking............. Present
lvs: -L LVM data.................................................. Missing
mdadm: -Ra advanced mdraid data................................... Missing
modinfo: Ax; -Nx module version................................... Present
runlevel: -I fallback to Perl..................................... Present
sensors: -s sensors output........................................ Present
smartctl: -Da advanced data....................................... Missing
strings: -I sysvinit version...................................... Present
sudo: -Dx hddtemp-user; -o file-user (try doas!).................. Present
tree: --debugger 20,21 /sys tree.................................. Missing
upower: -sx attached device battery info.......................... Present
uptime: -I uptime................................................. Present
 
The following recommended system programs are missing:
Program: bt-adapter ~ Install package: bluez-tools
Program: doas ~ Install package: doas
Program: fruid_print ~ Install package: N/A
Program: lvs ~ Install package: lvm2
Program: mdadm ~ Install package: mdadm
Program: smartctl ~ Install package: smartmontools
Program: tree ~ Install package: tree
------------------------------------------------------------------------------
Test: recommended display information programs:
 
glxinfo: -G (X) glx info.......................................... Present
wmctrl: -S active window manager (fallback)....................... Present
xdpyinfo: -G (X) Screen resolution, dpi; -Ga Screen size.......... Present
xprop: -S (X) desktop data........................................ Present
xrandr: -G (X) monitors(s) resolution; -Ga monitor data........... Present
 
All recommended display information programs are present
------------------------------------------------------------------------------
Test: recommended downloader programs (You only need one of these):
 
Perl HTTP::Tiny is the default downloader tool if IO::Socket::SSL is present. 
See --help --alt 40-44 options for how to override default downloader(s) in 
case of issues. 
 
If dig is installed, it is the default for WAN IP data. Strongly recommended. 
Dig is fast and accurate. 
 
curl: -i (if no dig); -w,-W; -U................................... Present
dig: -i wlan IP................................................... Present
wget: -i (if no dig); -w,-W; -U................................... Missing
 
The following recommended downloader programs are missing. However, you really 
only need dig in most cases. All systems should have at least one of the 
downloader options present.: 
Program: wget ~ Install package: wget
------------------------------------------------------------------------------
Test: recommended kernel modules:
 
GPU modules are only needed if applicable. NVMe drives do not need drivetemp 
but other types do. 
 
To load a module: modprobe <module-name> - To permanently load add to 
/etc/modules or /etc/modules-load.d/modules.conf (check your system paths for 
exact file/directory names). 
 
amdgpu: -s AMD GPU sensor data (newer AMD GPUs)................... Missing
drivetemp: -Dx drive temperature (kernel >= 5.6).................. Missing
nouveau: -s Nvidia GPU sensor data (if using free driver)......... Missing
 
The following recommended kernel modules are missing:
Kernel Module: amdgpu
Kernel Module: drivetemp
Kernel Module: nouveau
------------------------------------------------------------------------------
Test: recommended Perl modules (Optional):
 
None of these are strictly required, but if you have them all, you can 
eliminate some recommended non Perl programs from the install. 
 
HTTP::Tiny and IO::Socket::SSL must both be present to use as a downloader 
option. For json export Cpanel::JSON::XS is preferred over JSON::XS, but 
JSON::PP is in core modules. To run --debug 20-22 File::Copy, File::Find, and 
File::Spec::Functions must be present (most distros have these in Core 
Modules). 
 
File::Copy: --debug 20-22 - required to run debugger.............. Present
File::Find: --debug 20-22 - required to run debugger.............. Present
File::Spec::Functions: --debug 20-22 - required to run debugger... Present
HTTP::Tiny: -U; -w,-W; -i (if dig not installed).................. Present
IO::Socket::SSL: -U; -w,-W; -i (if dig not installed)............. Missing
Time::HiRes: -C cpu sleep (not required); --debug timers.......... Present
JSON::PP: --output json (in CoreModules, but slower).............. Missing
Cpanel::JSON::XS: --output json (faster than JSON::PP)............ Present
JSON::XS: --output json (legacy).................................. Present
XML::Dumper: --output xml - Crude and raw......................... Present
Net::FTP: --debug 21,22........................................... Present
 
The following recommended Perl modules are missing:
Perl Module: IO::Socket::SSL ~ Install package: perl-IO-Socket-SSL
Perl Module: JSON::PP ~ Install package: perl-JSON-PP
------------------------------------------------------------------------------
Test: recommended directories:
 
/dev: -l,-u,-o,-p,-P,-D disk partition data....................... Present
/dev/disk/by-id: -D serial numbers................................ Present
/dev/disk/by-label: -l,-o,-p,-P partition labels.................. Missing
/dev/disk/by-path: -D extra data.................................. Present
/dev/disk/by-uuid: -u,-o,-p,-P partition uuid..................... Present
/sys/class/dmi/id: -M system, motherboard, bios................... Present
 
The following recommended directories are missing:
Directory: /dev/disk/by-label
------------------------------------------------------------------------------
Test: recommended files:
 
Note that not all of these are used by every system, so if one is missing it's 
usually not a big deal. 
 
/etc/lsb-release: -S distro version data (older version).......... Missing
/etc/os-release: -S distro version data (newer version)........... Present
/proc/asound/cards: -A sound card data............................ Missing
/proc/asound/version: -A ALSA data................................ Missing
/proc/cpuinfo: -C cpu data........................................ Present
/proc/mdstat: -R mdraid data (if you use dm-raid)................. Present
/proc/meminfo: -I,-tm, -m memory data............................. Present
/proc/modules: -G module data (sometimes)......................... Present
/proc/mounts: -P,-p partition advanced data....................... Present
/proc/scsi/scsi: -D Advanced hard disk data (used rarely)......... Present
/var/log/Xorg.0.log: -G graphics driver load status............... Missing
 
The following recommended files are missing:
File: /etc/lsb-release
File: /proc/asound/cards
File: /proc/asound/version
File: /var/log/Xorg.0.log
------------------------------------------------------------------------------
Ok, all done with the checks. Have a nice day.
```

**获取 Linux 系统信息**

```
[root@QZ ~]# inxi 
CPU: 8-core Intel Xeon E5-2609 v4 (-MCP-) speed/min/max: 1220/1200/1700 MHz
Kernel: 3.10.0-1160.el7.x86_64 x86_64 Up: 13d 4h 6m Mem: 2730.4/15774.7 MiB
(17.3%) Storage: 1.13 TiB (1.1% used) Procs: 169 Shell: Bash inxi: 3.3.21
```

**获取内核和发行版本信息**

```
[root@QZ ~]# inxi -S 
System:
  Host: QZ Kernel: 3.10.0-1160.el7.x86_64 arch: x86_64 bits: 64
    Console: pty pts/0 Distro: CentOS Linux release 7.9.2009 (Core)
```

**获取电脑机型**

```
[root@QZ ~]# inxi -M
Machine:
  Type: Server Mobo: N/A model: SC612DI-8F v: 1.01 serial: ZM194S005553
    UEFI: American Megatrends v: 3.1 date: 06/07/2018
```

**获取 CPU 及主频信息**

```
[root@QZ ~]# inxi -C
CPU:
  Info: 8-core model: Intel Xeon E5-2609 v4 bits: 64 type: MCP cache:
    L2: 2 MiB
  Speed (MHz): avg: 1204 min/max: 1200/1700 cores: 1: 1236 2: 1200 3: 1200
    4: 1200 5: 1200 6: 1200 7: 1200 8: 1200
```

**获取显卡信息**

```shell
[root@QZ ~]# inxi -G
Graphics:
  Device-1: ASPEED Graphics Family driver: ast v: kernel
  Display: server: No display server data found. Headless machine?
  Message: GL data unavailable for root.
```

**获取声卡信息**

```shell
[root@QZ ~]# inxi -A
Audio:
  Message: No device data found.
```

**获取网卡信息**

```shell
[root@QZ ~]# inxi -N
Network:
  Device-1: Intel 82599ES 10-Gigabit SFI/SFP+ Network driver: ixgbe
  Device-2: Intel 82599ES 10-Gigabit SFI/SFP+ Network driver: ixgbe
  Device-3: Intel I350 Gigabit Network driver: igb
  Device-4: Intel I350 Gigabit Network driver: igb
  Device-5: Intel I210 Gigabit Network driver: igb
  Device-6: Intel I210 Gigabit Network driver: igb
```

**获取硬盘信息**

```shell
[root@QZ ~]# inxi -D
Drives:
  Local Storage: total: 1.13 TiB used: 12.9 GiB (1.1%)
  ID-1: /dev/sda vendor: Samsung model: MZ7LH240HAHQ-00005 size: 223.57 GiB
  ID-2: /dev/sdb vendor: Seagate model: ST1000NM0055-1V410C
    size: 931.51 GiB
```

**获取简要的系统信息**

```shell
[root@QZ ~]# inxi -b
System:
  Host: QZ Kernel: 3.10.0-1160.el7.x86_64 arch: x86_64 bits: 64
    Console: pty pts/0 Distro: CentOS Linux release 7.9.2009 (Core)
Machine:
  Type: Server Mobo: N/A model: SC612DI-8F v: 1.01 serial: ZM194S005553
    UEFI: American Megatrends v: 3.1 date: 06/07/2018
CPU:
  Info: 8-core Intel Xeon E5-2609 v4 [MCP] speed (MHz): avg: 1207
    min/max: 1200/1700
Graphics:
  Device-1: ASPEED Graphics Family driver: ast v: kernel
  Display: server: No display server data found. Headless machine?
  Message: GL data unavailable for root.
Network:
  Device-1: Intel 82599ES 10-Gigabit SFI/SFP+ Network driver: ixgbe
  Device-2: Intel 82599ES 10-Gigabit SFI/SFP+ Network driver: ixgbe
  Device-3: Intel I350 Gigabit Network driver: igb
  Device-4: Intel I350 Gigabit Network driver: igb
  Device-5: Intel I210 Gigabit Network driver: igb
  Device-6: Intel I210 Gigabit Network driver: igb
Drives:
  Local Storage: total: 1.13 TiB used: 12.89 GiB (1.1%)
Info:
  Processes: 169 Uptime: 13d 4h 12m Memory: 15.41 GiB used: 2.56 GiB (16.6%)
  Shell: Bash inxi: 3.3.21
```

**获取硬盘分区信息**

```shell
[root@QZ ~]# inxi -p
Partition:
  ID-1: / size: 49.09 GiB used: 6.32 GiB (12.9%) fs: ext4 dev: /dev/sda3
  ID-2: /boot size: 487.9 MiB used: 101.6 MiB (20.8%) fs: ext4
    dev: /dev/sda2
  ID-3: /boot/efi size: 511.7 MiB used: 11.2 MiB (2.2%) fs: vfat
    dev: /dev/sda1
  ID-4: /home size: 49.09 GiB used: 52 MiB (0.1%) fs: ext4 dev: /dev/sda4
  ID-5: /tmpdisk size: 424.61 GiB used: 72 MiB (0.0%) fs: ext4
    dev: /dev/sdb2
  ID-6: /var size: 492.03 GiB used: 6.33 GiB (1.3%) fs: ext4 dev: /dev/sdb1
  ID-7: swap-1 size: 16 GiB used: 0 KiB (0.0%) fs: swap dev: /dev/sda5
```

**获取完整的 Linux 系统信息**

```shell
[root@QZ ~]# inxi -F
```

**监控 Linux 进程的内存使用**

```shell
[root@QZ ~]# inxi  -I
Info:
  Processes: 169 Uptime: 13d 4h 16m Memory: 15.41 GiB used: 2.81 GiB (18.3%)
  Shell: Bash inxi: 3.3.21
```

**监控进程占用的 CPU 和内存资源**

```sh
[root@QZ ~]# inxi  -tc 
Processes:
  CPU top: 5 of 168
  1: cpu: 3.4% command: clickhouse-server pid: 1481
  2: cpu: 1.5% command: etcd pid: 972
  3: cpu: 0.7% command: node_exporter pid: 971
  4: cpu: 0.2% command: prometheus pid: 973
  5: cpu: 0.2% command: mysqld pid: 1562
[root@QZ ~]# inxi  -tm
Processes:
  System RAM: total: 15.41 GiB used: 2.67 GiB (17.3%)
  Memory top: 5 ( 1 processes) of 168
  1: mem: 917.8 MiB (5.8%) command: clickhouse-server pid: 1481
  2: mem: 424.1 MiB (2.6%) command: mysqld pid: 1562
  3: mem: 94.4 MiB (0.5%) command: etcd pid: 972
  4: mem: 74.3 MiB (0.4%) command: prometheus pid: 973
  5: mem: 70.1 MiB (0.4%) command: ysyapi-dashboard pid: 3058
[root@QZ ~]# inxi  -tcm
Processes:
  CPU top: 5 of 168
  1: cpu: 3.4% command: clickhouse-server pid: 1481
  2: cpu: 1.5% command: etcd pid: 972
  3: cpu: 0.7% command: node_exporter pid: 971
  4: cpu: 0.2% command: prometheus pid: 973
  5: cpu: 0.2% command: mysqld pid: 1562
  System RAM: total: 15.41 GiB used: 2.69 GiB (17.4%)
  Memory top: 5 of 168
  1: mem: 932.9 MiB (5.9%) command: clickhouse-server pid: 1481
  2: mem: 424.1 MiB (2.6%) command: mysqld pid: 1562
  3: mem: 94.4 MiB (0.5%) command: etcd pid: 972
  4: mem: 74.3 MiB (0.4%) command: prometheus pid: 973
  5: mem: 70.1 MiB (0.4%) command: ysyapi-dashboard pid: 3058

```

可以在选项 cm 后跟一个整数(在 1-20 之间)设置显示多少个进程，下面的命令显示了前 10 个最消耗 CPU 和内存的进程：

```sh
[root@QZ ~]# inxi  -tcm10 
Processes:
  CPU top: 10 of 168
  1: cpu: 3.4% command: clickhouse-server pid: 1481
  2: cpu: 1.5% command: etcd pid: 972
  3: cpu: 0.7% command: node_exporter pid: 971
  4: cpu: 0.2% command: prometheus pid: 973
  5: cpu: 0.2% command: mysqld pid: 1562
  6: cpu: 0.1% command: networkmanager pid: 711
  7: cpu: 0.1% command: ysyapi-dashboard pid: 3058
  8: cpu: 0.0% command: systemd pid: 1
  9: cpu: 0.0% command: [kthreadd] pid: 2
  10: cpu: 0.0% command: [kworker/0:0h] pid: 4
  System RAM: total: 15.41 GiB used: 2.53 GiB (16.4%)
  Memory top: 10 of 168
  1: mem: 774.8 MiB (4.9%) command: clickhouse-server pid: 1481
  2: mem: 424.1 MiB (2.6%) command: mysqld pid: 1562
  3: mem: 94.4 MiB (0.5%) command: etcd pid: 972
  4: mem: 75.1 MiB (0.4%) command: prometheus pid: 973
  5: mem: 70.1 MiB (0.4%) command: ysyapi-dashboard pid: 3058
  6: mem: 64.1 MiB (0.4%) command: systemd-journald pid: 449
  7: mem: 39.2 MiB (0.2%) command: rsyslogd pid: 978
  8: mem: 38.6 MiB (0.2%) command: nginx: pid: 9622
  9: mem: 38.6 MiB (0.2%) command: nginx: pid: 9625
  10: mem: 38.3 MiB (0.2%) command: nginx: pid: 9621
```

下面的命令会列出网卡信息，包括接口信息、网络频率、mac 地址、网卡状态和网络 IP 等信息。

```sh
[root@QZ ~]# inxi  -Nni 
Network:
  Device-1: Intel 82599ES 10-Gigabit SFI/SFP+ Network driver: ixgbe
  IF: enp2s0f0 state: down mac: 68:91:d0:6a:74:6a
  Device-2: Intel 82599ES 10-Gigabit SFI/SFP+ Network driver: ixgbe
  IF: enp2s0f1 state: down mac: 68:91:d0:6a:74:6b
  Device-3: Intel I350 Gigabit Network driver: igb
  IF: ens1f0 state: down mac: 58:53:c0:44:0a:70
  Device-4: Intel I350 Gigabit Network driver: igb
  IF: ens1f1 state: down mac: 58:53:c0:44:0a:71
  Device-5: Intel I210 Gigabit Network driver: igb
  IF: eth0 state: up speed: 100 Mbps duplex: full mac: ac:1f:6b:d2:3f:30
  Message: Output throttled. IPs: 1; Limit: 10; Override: --limit [1-x;-1
    all]
  Device-6: Intel I210 Gigabit Network driver: igb
  IF: eth1 state: up speed: 1000 Mbps duplex: full mac: ac:1f:6b:d2:3f:31
  Message: Output throttled. IPs: 1; Limit: 10; Override: --limit [1-x;-1
    all]
  WAN IP: 111.207.167.18
```

**监控 CPU 温度和电脑风扇转速**

```shell
[root@QZ ~]# inxi -s 
Sensors:
  System Temperatures: ipmi cpu: 32 C mobo: 28 C
  Fan Speeds (RPM): ipmi fan-1: 1200 fan-3: 1200 fan-10: 1100
  System Temperatures: lm-sensors cpu: 24.0 C mobo: N/A
  Fan Speeds (RPM): lm-sensors N/A
```

**用 Linux 查看天气预报**

```shell
[root@QZ ~]# inxi -w
Weather:
  Report: temperature: 20 C (68 F) conditions: Clear sky
  Locale: Beijing, 22, CHN current time: Fri 21 Oct 2022 01:37:06 PM CST
    (Asia/Harbin) Source: WeatherBit.io
```

**查看所有的 Linux 仓库信息**

```shell
[root@QZ ~]# inxi -r 
Repos:
  No active yum repos in: /etc/yum.conf
  No active yum repos in: /etc/yum.repos.d/CentOS-Base.repo
  No active yum repos in: /etc/yum.repos.d/CentOS-CR.repo
  No active yum repos in: /etc/yum.repos.d/CentOS-Debuginfo.repo
  No active yum repos in: /etc/yum.repos.d/CentOS-Media.repo
  Active yum repos in: /etc/yum.repos.d/CentOS-SCLo-scl-rh.repo
    1: centos-sclo-rh ~ http://mirrorlist.centos.org?arch=$basearch&release=7&repo=sclo-rh
  Active yum repos in: /etc/yum.repos.d/CentOS-SCLo-scl.repo
    1: centos-sclo-sclo ~ http://mirrorlist.centos.org?arch=$basearch&release=7&repo=sclo-sclo
  No active yum repos in: /etc/yum.repos.d/CentOS-Sources.repo
  No active yum repos in: /etc/yum.repos.d/CentOS-Vault.repo
  No active yum repos in: /etc/yum.repos.d/CentOS-fasttrack.repo
  No active yum repos in: /etc/yum.repos.d/CentOS-x86_64-kernel.repo
  No active yum repos in: /etc/yum.repos.d/epel-testing.repo
  Active yum repos in: /etc/yum.repos.d/epel.repo
    1: epel ~ https://mirrors.fedoraproject.org/metalink?repo=epel-7&arch=$basearch&infra=$infra&content=$contentdir
  No active yum repos in: /etc/yum.repos.d/mysql-community-source.repo
  Active yum repos in: /etc/yum.repos.d/mysql-community.repo
    1: mysql-connectors-community ~ http://repo.mysql.com/yum/mysql-connectors-community/el/7/$basearch/
    2: mysql-tools-community ~ http://repo.mysql.com/yum/mysql-tools-community/el/7/$basearch/
    3: mysql-tools-preview ~ http://repo.mysql.com/yum/mysql-tools-preview/el/7/$basearch/
```

**显示基础信息** `-v`后数字1-8 简要信息到完整信息。

```shell
inxi -v 4 
```


