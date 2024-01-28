# 

## 安装版本

* Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production

[下载链接](https://www.oracle.com/technetwork/cn/database/bi-datawarehousing/downloads/index.html&wd=&eqid=cf87ed1c00053c5e00000006648fad1f)

[安装向导参考](https://www.cnblogs.com/slqleozhang/p/14711070.html)

创建用户（fidas）及密码(sa),并授予用户增、删、改权限。

```
CREATE USER fidas IDENTIFIED BY sa;
GRANT create session TO fidas;
ALTER USER fidas DEFAULT TABLESPACE users QUOTA UNLIMITED ON users;
GRANT SELECT ANY TABLE, INSERT ANY TABLE, UPDATE ANY TABLE, DELETE ANY TABLE TO fidas;
COMMIT;
```

windows启动1521端口监听及启动Oracle服务

```
win +x 用管理员运行cmd

cd D:\app\用户名\product\12.2.0\dbhome_1\BIN

开启监听：监听器是用于接受客户端连接的进程。
lsnrctl start

启动Oracle服务：
net start oracleserviceorcl
```

查看表空间

```
SELECT *
FROM user_tablespaces
WHERE tablespace_name = 'FIDAS';
```

登录超级管理员

```
 sqlplus / as sysdba
```

添加表空间

```
CREATE TABLESPACE FIDAS DATAFILE 'path_to_datafile' SIZE 10M;
```

使用fidas 用户创建表提示权限不足？

```
登录超级管理员：
 sqlplus / as sysdba

使用以下命令将 'CREATE TABLE' 权限授予 'FIDAS' 用户：
GRANT CREATE TABLE TO FIDAS;
使用以下命令将 'FIDAS' 用户的默认表空间更改为 'FIDAS' 表空间：
ALTER USER FIDAS DEFAULT TABLESPACE FIDAS;
```

ORACLE ORA-01950 对表空间无权限 错误问题

```
用超级管理员授权给fidas 用户
grant unlimited tablespace to fidas; 
```

查询fidas用户下所有表的数量：

```
SELECT COUNT(*) FROM all_tables WHERE owner = 'FIDAS';
```

