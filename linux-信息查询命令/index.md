# Linux 信息查询命令

# Linux 信息查询命令

## 硬件查询

查看 CPU 信息

```shell
cat /proc/cpuinfo
```

查看物理 CPU 数量

```shell
cat /proc/cpuinfo |grep "physical id"|sort |uniq|wc -l
```

查看逻辑 CPU 数量

```shell
cat /proc/cpuinfo |grep "processor"|wc -l
```

查看每个 CPU 中 core 的数量 (核心数)

```shell
cat /proc/cpuinfo |grep "cores"|uniq
```

查看 CPU 的主频

```shell
cat /proc/cpuinfo |grep MHz|uniq
```

查看操作系统内核信息

```shell
uname -a
```

查看 CPU 型号

```shell
cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c
# 8 Intel(R) Core(TM) i7-4770 CPU  @ 3.40GHz 前面 8 代表 CPU 数量.
```

查看 CPU 运行在 32bit 或 64bit 模式

```shell
getconf LONG_BIT
# 64 说明当前 CPU 运行在 64bit 模式下,但并不代表不支持 32bit,反之亦然.
```

查看 CPU 是否支持 64bit 计算

```shell
cat /proc/cpuinfo | grep flags | grep ' lm ' | wc -l
# 8 (结果大于 0, 说明支持 64bit 计算. lm 指 long mode, 支持 lm 则是 64bit)
```

查看机器型号

```shell
dmidecode | grep "Product Name" 
```

查看网卡信息

```shell
dmesg | grep -i eth 
```

查看内存信息

```shell
cat /proc/meminfo 
```

查看内存总量

```shell
grep MemTotal /proc/meminfo
```

查看空闲内存总量

```shell
grep MemFree /proc/meminfo
```

查看指定目录的大小

```shell
du -sh <目录名>  
```

查看各分区使用情况

```shell
df -h
```

查看系统运行时间、用户数、负载

```shell
uptime
```

查看操作系统版本

```shell
head -n 1 /etc/issue
```

查看环境变量

```shell
env
```

查看计算机名

```shell
hostname
```

