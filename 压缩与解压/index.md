# 压缩与解压缩


## 全文件压缩与解压

### 压缩

```bash
tar -zcvf 文件名.tgz 源文件
```

### 解压

先创建解压目录

```bash
mkdir -p /root/a
```

再将文件指定压缩到该目录内

```bash
tar -zxvf 文件名.tgz -C /root/a
```

## 排除日志文件的压缩与解压

### 压缩

注意`--exclude=`参数要放到打包目录之前。

排除xxx/logs文件，其余都压缩。

```bash
tar -zcvf xxx.tgz --exclude=xxx/logs xxx
```

例如：打包`/var/log/*`下所有文件除了`/var/log/landscape`文件夹之外。

```bash
tar -zcvf exclude-log.tgz  --exclude=/var/log/landscape /var/log/*
```

### 解压到指定文件夹

```bash
tar -zxvf 文件名.tgz -C /root/a
```

## 同时压缩多个文件夹

多个路径用空格隔开

```bash
 tar -zcvf myfile.tgz /etc/adduser.conf /var/log/docker.log
```

解压到指定文件夹内（该文件夹必须存在！）

```bash
tar -zxvf myfile.tgz -C /root/a
```

## 只显示压缩内容不解压

```bash
tar -tvf myfile.tgz 
```

或者

```bash
vim myfile.tgz
```


