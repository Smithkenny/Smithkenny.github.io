# mysql的user表详解


mysql中引用了一张表来存储mysql的用户信息 `mysql.user`

### 基础字段

Host：  访问数据库客户端机器的IP地址。'%':通配符，匹配所有的ip地址
User：  用户名。超级管理员为root
authentication_string：     密码的加密字符串

要注意的是，mysql是通过用户名（user）和客户端ip（host）来唯一确定一个用户。即，可以设置同一个用户名在不同的客户端登陆时的密码不同，登陆后的权限也不同。通过查看mysql.user的表结构也可以看出，host和user是作为联合主键存在的。

```Shell
mysql> desc mysql.user;
+------------------------+-----------------------------------+------+-----+-----------------------+-------+
| Field                  | Type                              | Null | Key | Default               | Extra |
+------------------------+-----------------------------------+------+-----+-----------------------+-------+
| Host                   | char(60)                          | NO   | PRI |                       |       |
| User                   | char(32)                          | NO   | PRI |                       |       |
| Select_priv            | enum('N','Y')                     | NO   |     | N                     |       |
| Insert_priv            | enum('N','Y')                     | NO   |     | N                     |       |
| Update_priv            | enum('N','Y')                     | NO   |     | N                     |       |
| Delete_priv            | enum('N','Y')                     | NO   |     | N                     |       |
| Create_priv            | enum('N','Y')                     | NO   |     | N                     |       |
| Drop_priv              | enum('N','Y')                     | NO   |     | N                     |       |
| Reload_priv            | enum('N','Y')                     | NO   |     | N                     |       |
| Shutdown_priv          | enum('N','Y')                     | NO   |     | N                     |       |
| Process_priv           | enum('N','Y')                     | NO   |     | N                     |       |
| File_priv              | enum('N','Y')                     | NO   |     | N                     |       |
| Grant_priv             | enum('N','Y')                     | NO   |     | N                     |       |
| References_priv        | enum('N','Y')                     | NO   |     | N                     |       |
| Index_priv             | enum('N','Y')                     | NO   |     | N                     |       |
| Alter_priv             | enum('N','Y')                     | NO   |     | N                     |       |
| Show_db_priv           | enum('N','Y')                     | NO   |     | N                     |       |
| Super_priv             | enum('N','Y')                     | NO   |     | N                     |       |
| Create_tmp_table_priv  | enum('N','Y')                     | NO   |     | N                     |       |
| Lock_tables_priv       | enum('N','Y')                     | NO   |     | N                     |       |
| Execute_priv           | enum('N','Y')                     | NO   |     | N                     |       |
| Repl_slave_priv        | enum('N','Y')                     | NO   |     | N                     |       |
| Repl_client_priv       | enum('N','Y')                     | NO   |     | N                     |       |
| Create_view_priv       | enum('N','Y')                     | NO   |     | N                     |       |
| Show_view_priv         | enum('N','Y')                     | NO   |     | N                     |       |
| Create_routine_priv    | enum('N','Y')                     | NO   |     | N                     |       |
| Alter_routine_priv     | enum('N','Y')                     | NO   |     | N                     |       |
| Create_user_priv       | enum('N','Y')                     | NO   |     | N                     |       |
| Event_priv             | enum('N','Y')                     | NO   |     | N                     |       |
| Trigger_priv           | enum('N','Y')                     | NO   |     | N                     |       |
| Create_tablespace_priv | enum('N','Y')                     | NO   |     | N                     |       |
| ssl_type               | enum('','ANY','X509','SPECIFIED') | NO   |     |                       |       |
| ssl_cipher             | blob                              | NO   |     | NULL                  |       |
| x509_issuer            | blob                              | NO   |     | NULL                  |       |
| x509_subject           | blob                              | NO   |     | NULL                  |       |
| max_questions          | int(11) unsigned                  | NO   |     | 0                     |       |
| max_updates            | int(11) unsigned                  | NO   |     | 0                     |       |
| max_connections        | int(11) unsigned                  | NO   |     | 0                     |       |
| max_user_connections   | int(11) unsigned                  | NO   |     | 0                     |       |
| plugin                 | char(64)                          | NO   |     | mysql_native_password |       |
| authentication_string  | text                              | YES  |     | NULL                  |       |
| password_expired       | enum('N','Y')                     | NO   |     | N                     |       |
| password_last_changed  | timestamp                         | YES  |     | NULL                  |       |
| password_lifetime      | smallint(5) unsigned              | YES  |     | NULL                  |       |
| account_locked         | enum('N','Y')                     | NO   |     | N                     |       |
+------------------------+-----------------------------------+------+-----+-----------------------+-------+
```



### 权限字段

```Shell
Select_priv：   确定用户是否可以通过SELECT命令选择数据。

Insert_priv：   确定用户是否可以通过INSERT命令插入数据。

Update_priv：   确定用户是否可以通过UPDATE命令修改现有数据。

Delete_priv：   确定用户是否可以通过DELETE命令删除现有数据。

Create_priv：   确定用户是否可以创建新的数据库和表。

Drop_priv：     确定用户是否可以删除现有数据库和表。

Reload_priv：   确定用户是否可以执行刷新和重新加载MySQL所用各种内部缓存的特定命令，包括日志、权限、主机、查询和表。

Shutdown_priv： 确定用户是否可以关闭MySQL服务器。在将此权限提供给root账户之外的任何用户时，都应当非常谨慎。

Process_priv：  确定用户是否可以通过SHOW PROCESSLIST命令查看其他用户的进程。

File_priv： 确定用户是否可以执行SELECT INTO OUTFILE和LOAD DATA INFILE命令。

Grant_priv：    确定用户是否可以将已经授予给该用户自己的权限再授予其他用户。例如，如果用户可以插入、选择和删除foo数据库中的信息，并且授予了GRANT权限，则该用户就可以将其任何或全部权限授予系统中的任何其他用户。

References_priv：   目前只是某些未来功能的占位符；现在没有作用。

Index_priv：    确定用户是否可以创建和删除表索引。

Alter_priv：    确定用户是否可以重命名和修改表结构。

Show_db_priv：  确定用户是否可以查看服务器上所有数据库的名字，包括用户拥有足够访问权限的数据库。可以考虑对所有用户禁用这个权限，除非有特别不可抗拒的原因。

Super_priv：    确定用户是否可以执行某些强大的管理功能，例如通过KILL命令删除用户进程，使用SET GLOBAL修改全局MySQL变量，执行关于复制和日志的各种命令。

Create_tmp_table_priv： 确定用户是否可以创建临时表。

Lock_tables_priv：  确定用户是否可以使用LOCK TABLES命令阻止对表的访问/修改。

Execute_priv：  确定用户是否可以执行存储过程。此权限只在MySQL 5.0及更高版本中有意义。

Repl_slave_priv：   确定用户是否可以读取用于维护复制数据库环境的二进制日志文件。此用户位于主系统中，有利于主机和客户机之间的通信。

Repl_client_priv：  确定用户是否可以确定复制从服务器和主服务器的位置。

Create_view_priv：  确定用户是否可以创建视图。此权限只在MySQL 5.0及更高版本中有意义。关于视图的更多信息，参见第34章。

Show_view_priv：    确定用户是否可以查看视图或了解视图如何执行。此权限只在MySQL 5.0及更高版本中有意义。

Create_routine_priv：   确定用户是否可以更改或放弃存储过程和函数。此权限是在MySQL 5.0中引入的。

Alter_routine_priv：    确定用户是否可以修改或删除存储函数及函数。此权限是在MySQL 5.0中引入的。

Create_user_priv：  确定用户是否可以执行CREATE USER命令，这个命令用于创建新的MySQL账户。

Event_priv：    确定用户能否创建、修改和删除事件。这个权限是MySQL 5.1.6新增的。

Trigger_priv：  确定用户能否创建和删除触发器，这个权限是MySQL 5.1.6新增的。
```

### mysql.db表
user表中的每项权限为Y的时候，则代表该用户对所有数据库的所有表都拥有该权限。
而如果某个用户只对单独的某个数据库的某张表有该权限时，则user表中该权限对应的字段为N，并在db表中添加一条记录用来标识此项权限。

测试失败。给某个用户添加了一个权限，但是mysql.db中并没有增加记录。

### 基础命令
### 创建用户

```Shell
create user 'username'@'host' identified by 'password'; (不建议用)
```

如果不填host（`create user 'username' identified by 'password';`），则默认为%:即所有ip的客户端均可访问。

### 赋予权限

```Shell
-- 赋予单个权限
grant all on database_name.table_name to 'username'@'host' [identified by 'password'];

-- 赋予多个权限
grant insert, select on database_name.table_name to 'username'@'host' [identified by 'password'];
```

常见权限有：all, create, drop, insert, update, delete, select等。
如果带上[]里面的内则相当于修改此用户的密码。

这里发现一个问题，当修改密码或修改权限时，如果此用户已经登陆，则不会立刻受到权限修改的影响（过一段时间以后会不会影响暂没做测试。），还是可以正常的使用已经收回的权限。

### 收回权限

```Shell
-- 收回单个权限
revoke all on database_name.table_name from 'username'@'host';

-- 收回多个权限
revoke select, insert on database_name.table_name from 'username'@'host';

```

与赋予权限不一样的是，收回权限时不允许同时修改密码，而且将`to user` 改为 `from user`。

### 删除用户

```Shell
drop user 'username'@'host';
```

### 查看某个用户的权限

```Shell
show grants for 'username'@'host';
+---------------------------------------------------------------------+
| Grants for root@localhost                                           |
+---------------------------------------------------------------------+
| GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' WITH GRANT OPTION |
| GRANT PROXY ON ''@'' TO 'root'@'localhost' WITH GRANT OPTION        |
+---------------------------------------------------------------------+

```

### 查看MYSQL数据库中所有用户

```python
mysql> SELECT DISTINCT CONCAT('User: ''',user,'''@''',host,''';') AS query FROM mysql.user;
+------------------------------------+
| query                              |
+------------------------------------+
| User: 'root'@'%';                  |
| User: 'mysql.session'@'localhost'; |
| User: 'mysql.sys'@'localhost';     |
| User: 'root'@'localhost';          |
+------------------------------------+
4 rows in set (0.00 sec)
```

### 查看数据库中具体某个用户的权限

```python
mysql> show grants for 'root'@'%';
+-------------------------------------------------------------+
| Grants for root@%                                           |
+-------------------------------------------------------------+
| GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION |
+-------------------------------------------------------------+
1 row in set (0.00 sec)
```

### 查看user表中某个用户的具体权限

```python
mysql> select * from mysql.user where user='root' \G;
*************************** 1. row ***************************
                  Host: localhost
                  User: root
           Select_priv: Y
           Insert_priv: Y
           Update_priv: Y
           Delete_priv: Y
           Create_priv: Y
             Drop_priv: Y
           Reload_priv: Y
         Shutdown_priv: Y
          Process_priv: Y
             File_priv: Y
            Grant_priv: Y
       References_priv: Y
            Index_priv: Y
            Alter_priv: Y
          Show_db_priv: Y
            Super_priv: Y
 Create_tmp_table_priv: Y
      Lock_tables_priv: Y
          Execute_priv: Y
       Repl_slave_priv: Y
      Repl_client_priv: Y
      Create_view_priv: Y
        Show_view_priv: Y
   Create_routine_priv: Y
    Alter_routine_priv: Y
      Create_user_priv: Y
            Event_priv: Y
          Trigger_priv: Y
Create_tablespace_priv: Y
              ssl_type: 
            ssl_cipher: 
           x509_issuer: 
          x509_subject: 
         max_questions: 0
           max_updates: 0
       max_connections: 0
  max_user_connections: 0
                plugin: mysql_native_password
 authentication_string: *064C811C8A9344D23DFC73BDBE590528C07A6FCC
      password_expired: N
 password_last_changed: 2022-07-26 13:03:51
     password_lifetime: NULL
        account_locked: N
```

### 修改账号密码

```Shell
-- 修改当前登录账号的密码
set password = password('pwd');

-- 修改别的账号密码
set password for 'username'@'host' = password('pwd');

-- 可以使用赋予权限的方式修改密码（上文提到过），用此种方式时最好赋予的是登陆权限，这样可以不影响账号当前的权限。
grant usage on *.* to 'username'@'host' identified by 'pwd';
```

### mysql的各种权限（共27种）

（以下操作都是以root身份登陆进行grant授权，以p1@localhost身份登陆执行各种命令。）

#### usage

连接（登陆）权限，建立一个用户，就会自动授予其usage权限（默认授予）。

```Shell
grant usage on *.* to 'p1'@'localhost' identified by '123';
```

该权限只能用于数据库登陆，不能执行任何操作；且usage权限不能被回收，也即REVOKE用户并不能删除用户。

#### select

必须有select的权限，才可以使用select table

```Shell
grant select on pyt.* to ‘p1′@’localhost’;
select * from shop;
```

#### create

必须有create的权限，才可以使用create table

```Shell
grant create on pyt.* to ‘p1′@’localhost’;
```

#### create routine

必须具有create routine的权限，才可以使用{create | alter | drop} {procedure | function}

```Shell
grant create routine on pyt.* to ‘p1′@’localhost’;
-- 当授予create routine时，自动授予EXECUTE, ALTER ROUTINE权限给它的创建者：

show grants for ‘p1′@’localhost’;
 
+—————————————————————————+
Grants for p1@localhost
+————————————————————————–+
| GRANT USAGE ON *.* TO ‘p1′@’localhost’ IDENTIFIED BY PASSWORD ‘*23AE809DDACAF96AF0FD78ED04B6A265E05AA257′ |
| GRANT SELECT, CREATE, CREATE ROUTINE ON `pyt`.* TO ‘p1′@’localhost’|
| GRANT EXECUTE, ALTER ROUTINE ON PROCEDURE `pyt`.`pro_shop1` TO ‘p1′@’localhost’ |
+————————————————————————————-+

```

#### create temporary tables(注意这里是tables，不是table)

必须有create temporary tables的权限，才可以使用create temporary tables.

```Shell
grant create temporary tables on pyt.* to ‘p1′@’localhost’;
[mysql@mydev ~]$ mysql -h localhost -u p1 -p pyt
create temporary table tt1(id int);
```

#### create view

必须有create view的权限，才可以使用create view

```Shell
grant create view on pyt.* to ‘p1′@’localhost’;
create view v_shop as select price from shop;
```

#### create user

要使用CREATE USER，必须拥有mysql数据库的全局CREATE USER权限，或拥有INSERT权限。

```Shell
grant create user on *.* to ‘p1′@’localhost’;
-- 也可以使用
grant insert on *.* to p1@localhost;
```

#### insert

必须有insert的权限，才可以使用insert into …… values….

#### alter

必须有alter的权限，才可以使用alter table

```Shell
alter table shop modify dealer char(15);
```

#### alter routine

必须具有alter routine的权限，才可以使用{alter |drop} {procedure|function}

```Shell
grant alter routine on pyt.* to ‘p1′@’ localhost ‘;
drop procedure pro_shop;
 
revoke alter routine on pyt.* from ‘p1′@’localhost’;

[mysql@mydev ~]$ mysql -h localhost -u p1 -p pyt

drop procedure pro_shop;

ERROR 1370 (42000): alter routine command denied to user ‘p1′@’localhost’ for routine ‘pyt.pro_shop’
```

#### update

必须有update的权限，才可以使用update table

```Shell
update shop set price=3.5 where article=0001 and dealer=’A’;
```

#### delete

必须有delete的权限，才可以使用delete from ….where….(删除表中的记录)

#### drop

必须有drop的权限，才可以使用drop database db_name; drop table tab_name;

```Shell
drop view vi_name; drop index in_name;
```

#### show database

通过show database只能看到你拥有的某些权限的数据库，除非你拥有全局SHOW DATABASES权限。

对于p1@localhost用户来说，没有对mysql数据库的权限，所以以此身份登陆查询时，无法看到mysql数据库：

```Shell
show databases;
+——————–+
| Database |
+——————–+
| information_schema|
| pyt |
| test |
+——————–+
```

#### show view

必须拥有show view权限，才能执行show create view。

```Shell
grant show view on pyt.* to p1@localho
show create view v_shop;
```

#### index

必须拥有index权限，才能执行[create |drop] index

```Shell
grant index on pyt.* to p1@localhost;
create index ix_shop on shop(article);
drop index ix_shop on shop;
```

#### excute

执行存在的Functions,Procedures

```Shell
call pro_shop1(0001,@a)；
+———+
| article |
+———+
| 0001 |
| 0001 |
+———+
select @a;
+——+
| @a |
+——+
| 2 |
+——+
```

#### lock tables

必须拥有lock tables权限，才可以使用lock tables

```Shell
grant lock tables on pyt.* to p1@localhost;
lock tables a1 read;
unlock tables;
```

#### references

有了REFERENCES权限，用户就可以将其它表的一个字段作为某一个表的外键约束。

#### reload

必须拥有reload权限，才可以执行flush [tables | logs | privileges]

```Shell
grant reload on pyt.* to p1@localhost;
ERROR 1221 (HY000): Incorrect usage of DB GRANT and GLOBAL PRIVILEGES
grant reload on *.* to ‘p1′@’localhost’;
flush tables;
```

#### replication client

拥有此权限可以查询master server、slave server状态。

```Shell
show master status;
ERROR 1227 (42000): Access denied; you need the SUPER,REPLICATION CLIENT privilege for this operation
grant Replication client on *.* to p1@localhost;
-- 也可以使用下面的命令
grant super on *.* to p1@localhost;
show master status;
+——————+———-+————–+——————+
| File | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+——————+———-+————–+——————+
| mysql-bin.000006 | 2111 | | |
+——————+———-+————–+——————+
show slave status;
```

#### replication slave

拥有此权限可以查看从服务器，从主服务器读取二进制日志。

```Shell
show slave hosts;
ERROR 1227 (42000): Access denied; you need the REPLICATION SLAVE privilege for this operation
show binlog events;
ERROR 1227 (42000): Access denied; you need the REPLICATION SLAVE privilege for this operation
grant replication slave on *.* to p1@localhost;
show slave hosts;
show binlog events;
+—————+——-+—————-+———–+————-+————–+
| Log_name | Pos | Event_type | Server_id| End_log_pos|Info |
+—————+——-+————–+———–+————-+—————+
| mysql-bin.000005 | 4 | Format_desc | 1 | 98 | Server ver: 5.0.77-log, Binlog ver: 4 | |mysql-bin.000005|98|Query|1|197|use `mysql`; create table a1(i int)engine=myisam|
……………………………………
```

#### Shutdown

关闭MySQL：

```Shell
[mysql@mydev ~]$ mysqladmin shutdown
重新连接：
[mysql@mydev ~]$ mysql
4 ERROR 2002 (HY000): Can’t connect to local MySQL server through socket ‘/tmp/mysql.sock’ (2)
[mysql@mydev ~]$ cd /u01/mysql/bin
[mysql@mydev bin]$ ./mysqld_safe &
[mysql@mydev bin]$ mysql
```

#### grant option

拥有grant option，就可以将自己拥有的权限授予其他用户（仅限于自己已经拥有的权限）

```Shell
grant Grant option on pyt.* to p1@localhost;
grant select on pyt.* to p2@localhost;
```

#### ile

拥有file权限才可以执行 select …into outfile和load data infile…操作，但是不要把file, process, super权限授予管理员以外的账号，这样存在严重的安全隐患。

```Shell
grant file on *.* to p1@localhost;
load data infile ‘/home/mysql/pet.txt’ into table pet;
```

#### super

这个权限允许用户终止任何查询；修改全局变量的SET语句；使用CHANGE MASTER，PURGE MASTER LOGS。

```Shell
grant super on *.* to p1@localhost;
purge master logs before ‘mysql-bin.000006′;
```

#### process

通过这个权限，用户可以执行SHOW PROCESSLIST和KILL命令。默认情况下，每个用户都可以执行SHOW PROCESSLIST命令，但是只能查询本用户的进程。

```Shell
show processlist;
+—-+——+———–+——+———+——+——-+——————+
| Id | User | Host | db | Command | Time | State | Info |
+—-+——+———–+——+———+——+——-+——————+
| 12 | p1 | localhost | pyt | Query | 0 | NULL | show processlist |
+—-+——+———–+——+———+——+——-+——————+
```


