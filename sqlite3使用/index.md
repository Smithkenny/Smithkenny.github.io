# sqlite3使用


## sqlite3使用

#### 连接数据库

```python
import sqlite3

conn = sqlite3.connect("test.db")   # 打开或创建数据库文件
print("Opened database successfully")
c = conn.cursor()   # 获取游标
sql = ""
c.execute(sql)  # 执行sql语句
conn.commit()   # 提交数据库操作
conn.close()    # 关闭数据库连接
print("ok")
```

#### 创建数据表

```python
import sqlite3
conn = sqlite3.connect("test.db")   # 打开或创建数据库文件

print("成功打开数据")
c = conn.cursor()   # 获取游标
sql = '''
    create table company
        (id int primary key not null,
        name text not null,
        age int not null,
        address char(50),
        salary real
        );
'''
c.execute(sql)  # 执行sql语句
conn.commit()   # 提交数据库操作
conn.close()    # 关闭数据库连接
print("建表成功")
```

#### 使用pycharm连接数据库

[见](https://blog.csdn.net/qq_40707462/article/details/105685933)

#### 插入数据

```python
import sqlite3
conn = sqlite3.connect("test.db")   # 打开或创建数据库文件

print("成功打开数据")
c = conn.cursor()   # 获取游标

sql1 = '''
    insert into company(id, name,age,address, salary)
        values(1,'张三',32,"成都",8000);

'''

sql2 = '''
    insert into company(id, name,age,address, salary)
        values(2,'李四',30,"重庆",15000);

'''
c.execute(sql1)  # 执行sql语句
c.execute(sql2)  # 执行sql语句

conn.commit()   # 提交数据库操作
conn.close()    # 关闭数据库连接
print("插入数据完毕")
```

#### 查询数据

 ```python
import sqlite3
conn = sqlite3.connect("test.db")   # 打开或创建数据库文件

print("成功打开数据")
c = conn.cursor()   # 获取游标

sql = "select id, name, address, salary from company"

cursor = c.execute(sql)  # 执行sql语句
for row in cursor:
    print("id = ", row[0])
    print("name = ", row[1])
    print("address = ", row[2])
    print("salary = ", row[3], "\n")
conn.close()    # 关闭数据库连接
print("查询完毕")
 ```




