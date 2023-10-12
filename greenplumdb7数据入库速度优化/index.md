# Greenplumdb7数据入库速度优化


GP调整的参数如下：
```sh
(1)全局死锁检测开关
在Greenplum 6中其默认关闭，需要打开它才可以支持并发更新/删除操作；
gpconfig -c gp_enable_global_deadlock_detector -v on

(2) 禁用GPORCA优化器（据说GPDB6默认的优化器为：GPORCA）
gpconfig -c optimizer -v off

(3)关闭日志
此GUC减少不必要的日志，避免日志输出对I/O性能的干扰。
gpconfig -c log_statement -v none

注意：参数配置修改后，我使用gpstop -u重新加载配置，并用gpconfig -s检查了各个参数确为修改成功

```

注意：如果命令无法使用可以在`postgresql.conf`配置文件中追加参数,并使用`gpstop -u`重新加载配置。

```sh
optimizer=off
gp_enable_global_deadlock_detector=on
log_statement=none
```



登录库：

```sh
[gpadmin@mdw gpseg-1]$ psql -Ugpadmin -d postgres 
psql (12.12)
Type "help" for help.

postgres=# \d
                       List of relations
 Schema |          Name          |   Type   |  Owner  | Storage 
--------+------------------------+----------+---------+---------
 public | company                | table    | gpadmin | heap
 public | customer               | table    | gpadmin | heap
 public | customer_id_sequenct   | sequence | gpadmin | 
 public | demo01                 | table    | gpadmin | heap
 public | demo01_demo01_id_seq   | sequence | gpadmin | 
 public | demo02                 | table    | gpadmin | heap
 public | demo02_id_seq          | sequence | gpadmin | 
 public | jtsec_db_change_01     | table    | gpadmin | heap
 public | jtsec_db_change_02     | table    | gpadmin | heap
 public | jtsec_db_change_046    | table    | gpadmin | heap
 public | jtsec_dep_sequence_01  | sequence | gpadmin | 
 public | jtsec_dep_sequence_02  | sequence | gpadmin | 
 public | jtsec_dep_sequence_046 | sequence | gpadmin | 
 public | product                | table    | gpadmin | heap
 public | product_code_seq       | sequence | gpadmin | 
(15 rows)

postgres=# show log_statement;
 log_statement 
---------------
 none
(1 row)

postgres=# \q
```

[参考](https://www.codenong.com/cs103738656/)

