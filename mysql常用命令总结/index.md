# mysql常用命令总结


### 常见命令

#### 连接本地数据库与远程数据库(10.4.xx.xx:3306)

```Shell
mysql -h localhost -u root -p123

mysql -h 10.4.xx.xx -P 3306 -u root -p
```

#### 显示数据库列表

```Shell
show databases;
```

#### 显示库中的数据表

````Shell
show tables;
````

#### 显示数据表的结构

```Shell
describe 表名;
```

默认表结构类型为MYISAM,并在/var/lib/mysql/abc 目录下建立student.frm(表定义文件)，student.MDY(数据文件)，student.MYI(索引文件)。

#### 建库

```Shell
create database 库名;
```

#### 建表

```Shell
create table 表名 (字段设定列表);
```

例如：

```Shell
mysql> create table test (id int(20));
```

#### 删库和删表

```Shell
drop database 库名;
drop table 表名;
```

#### 将表中记录清空

```Shell
delete from 表名;
# 内容清空，自增id不会被清掉，自增id会保留
mysql> truncate table users;
# 数据库返回：“Query OK, 0 rows affected (0.04 sec)”
#（成功返回0）（自增id也一同会被清掉)
```

#### 显示表中的记录

```Shell
select * from 表名;
```

### 库的基本操作

#### 创建数据库

```Shell
mysql> create database ceshi;
```

#### 连接数据库

```Shell
mysql> use ceshi;
```

#### 查看当前使用的数据库

```Shell
mysql> select database();
```

#### 当前数据库包含的表信息

```Shell
mysql> show tables; 
```

#### 删除数据库

```Shell
mysql> drop database ceshi;
```

### 表的基本操作

#### 命令

```Shell
create table <表名> (<字段名 1> <类型 1> [,..<字段名 n> <类型 n>]);
```

**例如**

```Shell
mysql> create table Class(
id int(4) not null primary key auto_increment, 
name varchar(25) not null, 
age int (4) not null default'0');    
# default'0' 设置默认值为0 ,not null(不能为空) ,primary key（主键）,auto_increment（自增长）
```

#### 获取表结构

##### 命令

```Shell
desc 表名，或者show columns from 表名
```

**例如**

```Shell
mysql> desc Class;
mysql> describe Class;
mysql> show columns from Class;
```

#### 插入数据

##### 命令

```Shell
insert into <表名> [( <字段名 1>[,..<字段名 n > ])] values ( 值 1 )[, ( 值 n )]
```

**例如**

```Shell
mysql> insert into Class values(1,'Wrry',26),(2,'ZJW',28);
```

#### 查询表中的数据

##### 查询所有行

```Shell
mysql> select * from Class;
```

##### 查询前几行数据

**例如:查看表 Class 中前 3 行数据**

```Shell
mysql> select * from Class limit 0,3;
```

**或者**

```Shell
mysql> select * from Class order by id limit 0,3;    # order by id:以id排序
```

#### 删除表中数据

##### 命令

```Shell
delete from 表名 where 表达式;
```

##### 例如:删除表 Class 中编号为 6 的记录

```Shell
mysql> delete from MyClass where id=6;
```

#### 修改表中数据

##### 命令

```Shell
update 表名 set 字段=新值,... where 条件;
```

##### 例如

```Shell
mysql> update Class set name='AI' where id=1;
```

#### 在表中增加字段

##### 命令

```Shell
alter table 表名 add 字段 类型 其他;
```

##### 例如:在表 Class 中添加了一个字段 sex,类型为 varchar(25),默认值为 0

```Shell
mysql> alter table Class add sex varchar(25) default '0';
```

#### 更改表名

##### 命令

```Shell
rename table 原表名 to 新表名;
```

##### 例如:在表 Class 名字更改为 MClass

```Shell
mysql> rename table Class to MClass;
```

#### 复制表

```Shell
create table 新表名 like 原表名称;
```

例如

```Shell
mysql> create table Test like test;
```

#### 删除表

```Shell
命令:drop table <表名>
```

##### 例如:删除表名为 MClass 的表

```Shell
mysql> drop table MClass;
```

更多操作[参考](https://www.w3school.com.cn/sql/sql_autoincrement.asp)

### Mysql查看触发器

#### 连接数据库并进入库

```shell
use test;
```

#### 查看当前库中所有表的触发器

```
mysql> show triggers \G;
*************************** 1. row ***************************
             Trigger: trigupdate
               Event: UPDATE
               Table: account
           Statement: INSERT INTO myevent VALUES(1,'after update')
              Timing: AFTER
             Created: NULL
            sql_mode: 
             Definer: root@localhost
character_set_client: utf8
collation_connection: utf8_general_ci
  Database Collation: utf8_general_ci
1 row in set (0.00 sec)

ERROR: 
No query specified
```

#### 触发器的创建

新建表account，表中有两个字段，分别是 INT 类型的 accnum 和 DECIMAL 类型的 amount。SQL 语句和运行结果如下：

```
mysql> CREATE TABLE account(
       accnum INT(4),
       amount DECIMAL(10,2));
Query OK, 0 rows affected (0.49 sec)
```

创建一个名为 trigupdate 的触发器，每次 account 表更新数据之后都向 myevent 数据表中插入一条数据。创建数据表 myevent 的 SQL 语句和运行结果如下：

```
mysql> CREATE TABLE myevent(
       id INT(11) DEFAULT NULL,
       evtname CHAR(20) DEFAULT NULL);
Query OK, 0 rows affected (0.26 sec)
```

创建 trigupdate 触发器的 SQL 代码如下：

```
mysql> CREATE TRIGGER trigupdate AFTER UPDATE ON account
       FOR EACH ROW INSERT INTO myevent VALUES(1,'after update');
Query OK, 0 rows affected (0.15 sec)
```

使用 SHOW TRIGGERS 语句查看触发器（在 SHOW TRIGGERS 命令后添加`\G`，这样显示信息会比较有条理），SQL 语句和运行结果如下：

```
mysql> show triggers\G;
*************************** 1. row ***************************
             Trigger: trigupdate
               Event: INSERT
               Table: account
           Statement: INSERT INTO myevent VALUES(1,'after insert')
              Timing: AFTER
             Created: NULL
            sql_mode: 
             Definer: root@localhost
character_set_client: utf8
collation_connection: utf8_general_ci
  Database Collation: utf8_general_ci
1 row in set (0.00 sec)

ERROR: 
No query specified
```

由运行结果可以看到触发器的基本信息。对以上显示信息的说明如下：

- Trigger 表示触发器的名称，在这里触发器的名称为 trigupdate；
- Event 表示激活触发器的事件，这里的触发事件为更新操作 UPDATE；
- Table 表示激活触发器的操作对象表，这里为 account 表；
- Statement 表示触发器执行的操作，这里是向 myevent 数据表中插入一条数据；
- Timing 表示触发器触发的时间，这里为更新操作之后（AFTER）；
- 还有一些其他信息，比如触发器的创建时间、SQL 的模式、触发器的定义账户和字符集等，这里不再一一介绍。

SHOW TRIGGERS 语句用来查看当前创建的所有触发器的信息。因为该语句无法查询指定的触发器，所以在触发器较少的情况下，使用该语句会很方便。如果要查看特定触发器的信息或者数据库中触发器较多时，可以直接从 information_schema 数据库中的 triggers 数据表中查找。

#### 在triggers表中查看触发器信息

在 MySQL 中，所有触发器的信息都存在 information_schema 数据库的 triggers 表中，可以通过查询命令 SELECT 来查看，具体的语法如下：

```
SELECT * FROM information_schema.triggers WHERE trigger_name= '触发器名';
```

其中，`'触发器名'`用来指定要查看的触发器的名称，需要用单引号引起来。这种方式可以查询指定的触发器，使用起来更加方便、灵活。

下面使用 SELECT 命令查看 trigupdate 触发器，SQL 语句如下：

```
SELECT * FROM information_schema.triggers WHERE TRIGGER_NAME= 'trigupdate'\G;
```

上述命令通过 WHERE 来指定需要查看的触发器的名称，运行结果如下：

```shell
mysql> SELECT * FROM information_schema.triggers WHERE TRIGGER_NAME= 'trigupdate'\G;
*************************** 1. row ***************************
           TRIGGER_CATALOG: def
            TRIGGER_SCHEMA: test
              TRIGGER_NAME: trigupdate
        EVENT_MANIPULATION: INSERT
      EVENT_OBJECT_CATALOG: def
       EVENT_OBJECT_SCHEMA: test
        EVENT_OBJECT_TABLE: account
              ACTION_ORDER: 0
          ACTION_CONDITION: NULL
          ACTION_STATEMENT: INSERT INTO myevent VALUES(1,'after insert')
        ACTION_ORIENTATION: ROW
             ACTION_TIMING: AFTER
ACTION_REFERENCE_OLD_TABLE: NULL
ACTION_REFERENCE_NEW_TABLE: NULL
  ACTION_REFERENCE_OLD_ROW: OLD
  ACTION_REFERENCE_NEW_ROW: NEW
                   CREATED: NULL
                  SQL_MODE: 
                   DEFINER: root@localhost
      CHARACTER_SET_CLIENT: utf8
      COLLATION_CONNECTION: utf8_general_ci
        DATABASE_COLLATION: utf8_general_ci
1 row in set (0.01 sec)
```

由运行结果可以看到触发器的详细信息。对以上显示信息的说明如下：

- TRIGGER_SCHEMA 表示触发器所在的数据库；
- TRIGGER_NAME 表示触发器的名称；
- EVENT_OBJECT_TABLE 表示在哪个数据表上触发；
- ACTION_STATEMENT 表示触发器触发的时候执行的具体操作；
- ACTION_ORIENTATION 的值为 ROW，表示在每条记录上都触发；
- ACTION_TIMING 表示触发的时刻是 AFTER；
- 还有一些其他信息，比如触发器的创建时间、SQL 的模式、触发器的定义账户和字符集等，这里不再一一介绍。

上述 SQL 语句也可以不指定触发器名称，这样将查看所有的触发器，SQL 语句如下：

```
SELECT * FROM information_schema.triggers \G;
```

这个语句会显示 triggers 数据表中所有的触发器信息。

