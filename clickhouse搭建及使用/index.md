# Clickhouse搭建及使用


## 一、什么是ClickHouse？

`ClickHouse`是一个用于联机分析(`OLAP`)的列式数据库管理系统(`DBMS`)。官方介绍如下[链接](https://clickhouse.com/docs/zh)。

## 二、搭建过程

### 1.使用`docker`部署。

[官方镜像](clickhouse/clickhouse-server)

[第三方镜像](yandex/clickhouse-server)

#### 1.1部署第三方镜像

#### 1.1.1 镜像拉取

```bash
docker pull yandex/clickhouse-server
docker pull yandex/clickhouse-client
```

#### 1.1.2 启动server端

方式一：

```bash
docker run -d -p 8123:8123 -p 9000:9000 --name clickhouse yandex/clickhouse-server
```

方式二：

```bash
# 默认直接启动即可
docker run -d --name [启动之后的名称] --ulimit nofile=262144:262144 yandex/clickhouse-server

# 如果想指定目录启动，这里以clickhouse-test-server命令为例，可以随意写
mkdir /work/clickhouse/clickhouse-test-db       ## 创建数据文件目录
# 使用以下路径启动，在外只能访问clickhouse提供的默认9000端口，只能通过clickhouse-client连接server
docker run -d --name clickhouse-test-server --ulimit nofile=262144:262144 --volume=/work/clickhouse/clickhouse_test_db:/var/lib/clickhouse yandex/clickhouse-server
```

#### 1.1.3 启动client端

```bash
docker run -it --rm --link clickhouse-test-server:clickhouse-server yandex/clickhouse-client --host clickhouse-client
```

### 2.使用`rpm`方式部署

#### 2.1 所需[rpm](https://github.com/ClickHouse/ClickHouse/releases)包

- `clickhouse-server-23.10.5.20.x86_64.rpm`
- `clickhouse-common-static-23.10.5.20.x86_64.rpm`
- `clickhouse-client-23.10.5.20.x86_64.rpm`

#### 2.2 安装顺序

`common-static`->`server`->`client`

#### 2.3 开放远程权限

```bash
vi /etc/clickhouse-server/config.xml
220 行注释去掉
    220      <listen_host>::</listen_host>
```

#### 2.4 启动

```bash
systemctl start clickhouse-server.service
```

## 三、使用

### 1.使用dbeaver连接测试

- 选择`clickhouse`类型数据库
- 数据库/模式：`default`
- 用户名：`default`
- 密码：空

点击`连接测试`会自动下载所需要的驱动。最后点击`完成`。

### 2.建表测试

#### 2.1 使用`MergeTree`引擎创建表。

```bash
CREATE TABLE `default`.test 
( 
    id        UInt64, 
    type      UInt8, 
    create_time DateTime 
) ENGINE = MergeTree() 
  PARTITION BY toYYYYMMDD(create_time) 
  ORDER BY (id) 
  SETTINGS index_granularity = 4;
```

解释：

test表包括id、type、create等三个字段，其中以create_time日期字段作为分区键，并将日期格式转化为YYYYMMDD。按照id字段进行排序。由于没有显式设置主键，所以引擎默认使用ORDER BY设置的id列作为索引字段，并生成索引文件。index_granularity设置为4，意味着每4条数据产生一条索引数据。

#### 2.2 插入一条测试数据

```bash
insert into test.test(id, type, create_time) VALUES (1, 1, toDateTime('2021-03-01 00:00:00'));
```

2.3 查看表信息包括数据物理路径

```bash
SELECT     
    database,     
    table,     
    partition,     
    partition_id,     
    name,     
    active,     
    path 
 FROM system.parts 
 WHERE table = 'test' 
```

注意：`path`字段信息为`/var/lib/clickhouse/store/ced/cedde1b3-016f-472c-a651-14a5e3ce0cbb/20231127_1_1_0/`硬盘存储的物理路径。

详细信息[见](https://zhuanlan.zhihu.com/p/361622782)






