# python常用模块


## 日志模块角色

1.`Logger`:产生日志
2.`Filter`:几乎不用
3.`Handler`: 接收Logger传过来的日志，进行日志格式化，可以打印到终端，也可以打印到文件
4.`Formatter`：日志格式

## 日志模块相关说明

可在`logging.basicConfig()`函数中可通过具体参数来更改`logging`模块默认行为，可用参数有.
`filename`：用指定的文件名创建`FiledHandler`，这样日志会被存储在指定的文件中。
`filemode`：文件打开方式，在指定了`filename`时使用这个参数，默认值为“a”还可指定为“w”。
`format`：指定`handler`使用的日志显示格式。
`datefmt`：指定日期时间格式。
`level`：设置`rootlogger`的日志级别
`stream`：用指定的`stream`创建`StreamHandler`。可以指定输出到`sys.stderr,sys.stdout`或者文件，默认为`sys.stderr`。若同时列出了`filename`和`stream`两个参数，则`stream`参数会被忽略。

### format参数中可能用到的格式化串

```
%(name)s Logger的名字
%(levelno)s 数字形式的日志级别
%(levelname)s 文本形式的日志级别
%(pathname)s 调用日志输出函数的模块的完整路径名，可能没有
%(filename)s 调用日志输出函数的模块的文件名
%(module)s 调用日志输出函数的模块名
%(funcName)s 调用日志输出函数的函数名
%(lineno)d 调用日志输出函数的语句所在的代码行
%(created)f 当前时间，用UNIX标准的表示时间的浮 点数表示
%(relativeCreated)d 输出日志信息时的，自Logger创建以 来的毫秒数
%(asctime)s 字符串形式的当前时间。默认格式是 “2003-07-08 16:49:45,896”。逗号后面的是毫秒
%(thread)d 线程ID。可能没有
%(threadName)s 线程名。可能没有
%(process)d 进程ID。可能没有
%(message)s用户输出的消息
```

## 日志打印实现过程

```python
import logging
#1.Logger:产生日志
logger1=logging.getLogger('访问日志') 
#2.Handler: 接收Logger传过来的日志，进行日志格式化，可以打印到终端，也可以打印到文件
sh=logging.StreamHandler() #打印到终端
fh1=logging.FileHandler('s1.log', encoding='utf-8') #打印到文件
fh2=logging.FileHandler('s2.log', encoding='utf-8')
#3.Formatter：日志格式
formatter1=logging.Formatter(
    fmt='%(asctime)s - %(name)s - %(levelname)s - %(module)s: %(message)s',
    datefmt='%Y-%m-%d %H:%M:%S %p'
)
formatter2=logging.Formatter(
    fmt='%(asctime)s : %(message)s',
    datefmt='%Y-%m-%d %H:%M:%S %p'
)
formatter3=logging.Formatter(
    fmt='%(asctime)s : %(module)s : %(message)s',
    datefmt='%Y-%m-%d %H:%M:%S %p'
)

#4.为Handler绑定日志格式
sh.setFormatter(formatter1)
fh1.setFormatter(formatter2)
fh2.setFormatter(formatter3)
#5.为logger 绑定handler
logger1.addHandler(sh)
logger1.addHandler(fh1)
logger1.addHandler(fh2)
#6.设置日志级别:logger 对象的日志级别应该<=handler的日志级别
#logger1.setLevel(30)
logger1.setLevel(10)
sh.setLevel(10)
fh1.setLevel(10)
fh2.setLevel(10)

#7.测试
logger1.debug('测试者玩')
logger1.info('运行还算正常')
logger1.warning('可能要有bug了')
logger1.error('不好了，真出bug了')
logger1.critical('推到重写')
```

去注释版

```python
import logging
logger1=logging.getLogger('访问日志') 
sh=logging.StreamHandler()
fh1=logging.FileHandler('s1.log', encoding='utf-8') 
fh2=logging.FileHandler('s2.log', encoding='utf-8')
formatter1=logging.Formatter(
    fmt='%(asctime)s - %(name)s - %(levelname)s - %(module)s: %(message)s',
    datefmt='%Y-%m-%d %H:%M:%S %p'
)
formatter2=logging.Formatter(
    fmt='%(asctime)s : %(message)s',
    datefmt='%Y-%m-%d %H:%M:%S %p'
)
formatter3=logging.Formatter(
    fmt='%(asctime)s : %(module)s : %(message)s',
    datefmt='%Y-%m-%d %H:%M:%S %p'
)
sh.setFormatter(formatter1)
fh1.setFormatter(formatter2)
fh2.setFormatter(formatter3)
logger1.addHandler(sh)
logger1.addHandler(fh1)
logger1.addHandler(fh2)
#logger1.setLevel(30)
logger1.setLevel(10)
sh.setLevel(10)
fh1.setLevel(10)
fh2.setLevel(10)
```

测试

```python
logger1.debug('测试者玩')
logger1.info('运行还算正常')
logger1.warning('可能要有bug了')
logger1.error('不好了，真出bug了')
logger1.critical('推到重写')
```

结果：

`s1.log`

```
2022-03-22 20:26:58 PM : 测试者玩
2022-03-22 20:26:58 PM : 运行还算正常
2022-03-22 20:26:58 PM : 可能要有bug了
2022-03-22 20:26:58 PM : 不好了，真出bug了
2022-03-22 20:26:58 PM : 推到重写
```

`s2.log`

```
2022-03-22 20:26:58 PM : my_logging : 测试者玩
2022-03-22 20:26:58 PM : my_logging : 运行还算正常
2022-03-22 20:26:58 PM : my_logging : 可能要有bug了
2022-03-22 20:26:58 PM : my_logging : 不好了，真出bug了
2022-03-22 20:26:58 PM : my_logging : 推到重写
```

终端

```
2022-03-26 11:32:48 AM - 访问日志 - INFO - my_logging: 运行还算正常
2022-03-26 11:32:48 AM - 访问日志 - WARNING - my_logging: 可能要有bug了
2022-03-26 11:32:48 AM - 访问日志 - ERROR - my_logging: 不好了，真出bug了
2022-03-26 11:32:48 AM - 访问日志 - CRITICAL - my_logging: 推到重写
```

## 日志的继承关系（了解）

练习

```python
import logging
#1.Logger:产生日志
logger1=logging.getLogger('root')
logger2=logging.getLogger('root.child1')
logger3=logging.getLogger('root.child1.child2')

#2.Handler: 接收Logger传过来的日志，进行日志格式化，可以打印到终端，也可以打印到文件
sh=logging.StreamHandler() #打印到终端

#3.Formatter：日志格式
formatter1=logging.Formatter(
    fmt='%(asctime)s - %(name)s - %(levelname)s - %(module)s: %(message)s',
    datefmt='%Y-%m-%d %H:%M:%S %p'
)

#4.为Handler绑定日志格式
sh.setFormatter(formatter1)

#5.为logger 绑定handler
logger1.addHandler(sh)
logger2.addHandler(sh)
logger3.addHandler(sh)

#6.设置日志级别:logger 对象的日志级别应该<=handler的日志级别
#logger1.setLevel(30)
logger1.setLevel(10)
logger2.setLevel(10)
logger3.setLevel(10)
sh.setLevel(10)

#7.测试
logger1.debug('爷爷')
logger2.debug('爸爸')
logger3.debug('孙子')
```

结果

```
2022-03-22 21:00:30 PM - root - DEBUG - my_logging_child: 爷爷
2022-03-22 21:00:30 PM - root.child1 - DEBUG - my_logging_child: 爸爸
2022-03-22 21:00:30 PM - root.child1 - DEBUG - my_logging_child: 爸爸
2022-03-22 21:00:30 PM - root.child1.child2 - DEBUG - my_logging_child: 孙子
2022-03-22 21:00:30 PM - root.child1.child2 - DEBUG - my_logging_child: 孙子
2022-03-22 21:00:30 PM - root.child1.child2 - DEBUG - my_logging_child: 孙子
```

去注释版

```python
import logging
logger1=logging.getLogger('root')
logger2=logging.getLogger('root.child1')
logger3=logging.getLogger('root.child1.child2')
sh=logging.StreamHandler()

formatter1=logging.Formatter(
    fmt='%(asctime)s - %(name)s - %(levelname)s - %(module)s: %(message)s',
    datefmt='%Y-%m-%d %H:%M:%S %p'
)

sh.setFormatter(formatter1)

logger1.addHandler(sh)
logger2.addHandler(sh)
logger3.addHandler(sh)

#logger1.setLevel(30)
logger1.setLevel(10)
logger2.setLevel(10)
logger3.setLevel(10)
sh.setLevel(10)
```

测试

```
logger1.debug('爷爷')
logger2.debug('爸爸')
logger3.debug('孙子')
```

## 日志模块应用

模拟购物小程序，选择转账时记录日志。

### 整体项目结构

![购物小程序整体结构](/postimages/购物小程序整体结构.webp)

### 每个目录简单介绍

`bin`目录：存放用户交互程序

`conf`目录：设置相关

`core`：核心功能

`db`:用户认证相关

`lib`:常用功能

`log`：日志文件

`README`:项目介绍

### 相关源码

`start.py`

```python
import sys, os
#print(os.path.abspath(__file__))
#print(os.path.dirname(os.path.abspath(__file__)))
#print(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))

BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
sys.path.append(BASE_DIR)

from core import src
if __name__ == '__main__':
    src.run()
```

`settings.py`

```python
import os
import logging.config
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
LOG_PATH = os.path.join(BASE_DIR, 'log', 'access.log')
DB_PATH = os.path.join(BASE_DIR, 'db', 'user')
COLLECT_PATH = os.path.join(BASE_DIR, 'log', 'collect.log')
print(LOG_PATH)
print(DB_PATH)

# 定义三种日志输出格式 开始

standard_format = '[%(asctime)s][%(threadName)s:%(thread)d][task_id:%(name)s][%(filename)s:%(lineno)d]' \
                  '[%(levelname)s][%(message)s]' #其中name为getlogger指定的名字

simple_format = '[%(levelname)s][%(asctime)s][%(filename)s:%(lineno)d]%(message)s'

id_simple_format = '[%(levelname)s][%(asctime)s] %(message)s'


# log配置字典
LOGGING_DIC = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'standard': {
            'format': standard_format
        },
        'simple': {
            'format': simple_format
        },
        'id_simple': {
            'format': id_simple_format
        },
    },
    'filters': {},
    'handlers': {
        #打印到终端的日志
        'console': {
            'level': 'DEBUG',
            'class': 'logging.StreamHandler',  # 打印到屏幕
            'formatter': 'simple'
        },
        #打印到文件的日志,收集info及以上的日志
        'default': {
            'level': 'DEBUG',
            'class': 'logging.handlers.RotatingFileHandler',  # 保存到文件
            'formatter': 'standard',
            'filename': LOG_PATH,  # 日志文件
            'maxBytes': 1024*1024*5,  # 日志大小 5M
            'backupCount': 5,
            'encoding': 'utf-8',  # 日志文件的编码，再也不用担心中文log乱码了
        },
        'collect': {
            'level': 'DEBUG',
            'class': 'logging.handlers.RotatingFileHandler',  # 保存到文件
            'formatter': 'simple',
            'filename': COLLECT_PATH,  # 日志文件
            'maxBytes': 1024*1024*5,  # 日志大小 5M
            'backupCount': 5,
            'encoding': 'utf-8',  # 日志文件的编码，再也不用担心中文log乱码了
        },
    },
    'loggers': {
        '': {
            'handlers': ['default', 'console', 'collect'],  # 这里把上面定义的两个handler都加上，即log数据既写入文件又打印到屏幕
            'level': 'DEBUG',
            'propagate': False,  # 向上（更高level的logger）传递
        },
    },
}
```

`src.py`

```python
from lib import common
from lib import sql
def shop():
    print('购物')
def check_balance():
    print('查看余额....')
    #res = sql.execute('select balance from user where id=3')
    #print(res)
def transfer_account():
    print('转账....')
    #记录日志
    log_msg = 'jack give smith ten yuan'
    #调用日志功能
    loggers = common.logger_handle('transfer_account')
    loggers.debug(log_msg)
def run():
    msg = '''
    1.购物
    2.查看余额
    3.转账
    '''
    while True:
        print(msg)
        choice = input('>>: ').strip()
        if not choice:continue
        if choice == '1':
            shop()
        elif choice == '2':
            check_balance()
        elif choice == '3':
            transfer_account()
```

`user`

```python
alex,18,male,10000
egon,18,male,1000000
```

`common.py`

```python
from conf import settings
import logging.config
# def logger(msg):
#     with open(settings.LOG_PATH, 'a', encoding='utf-8') as f:
#         f.write('%s\n' %msg)

def logger_handle(log_name):
    logging.config.dictConfig(settings.LOGGING_DIC)  # 导入上面定义的logging配置
    logger = logging.getLogger(log_name)  # 生成一个log实例
    logger.info('It works!')  # 记录该文件的运行状态
```

`sql.py`

```python
def execute(sql):
    print('解析')
    print('执行sql')
```

`access.log`

```
jack给smith转账了10元
[2022-03-22 22:01:20,767][MainThread:2600][task_id:转账][common.py:10][INFO][It works!]
[2022-03-22 22:04:08,963][MainThread:14184][task_id:转账][common.py:10][INFO][It works!]
[2022-03-22 22:06:59,656][MainThread:15740][task_id:transfer_account][common.py:10][INFO][It works!]
[2022-03-26 11:41:33,154][MainThread:12820][task_id:transfer_account][common.py:10][INFO][It works!]
```

`collect.log`

```
[INFO][2022-03-22 22:01:20,767][common.py:10]It works!
[INFO][2022-03-22 22:04:08,963][common.py:10]It works!
[INFO][2022-03-22 22:06:59,656][common.py:10]It works!
[INFO][2022-03-26 11:41:33,154][common.py:10]It works!
```

## re模块-正则表达式

### 什么是正则？

正则就是用一些具有特殊含义的符号组合到一起（称为正则表达式）来描述字符或者字符串的方法。或者说：正则就是用来描述一类事物的规则。（在Python中）它内嵌在Python中，并通过 re 模块实现。正则表达式模式被编译成一系列的字节码，然后由用 C 编写的匹配引擎执行。

### 常用匹配模式(元字符)

```python
import re
print(re.findall('\w', 'jack 123 + _ - *'))
print(re.findall('\W', 'jack 123 + _ - *'))
print(re.findall('\s', 'jack\tn 12\n3 + _ - *'))
print(re.findall('\S', 'jack\tn 12\n3 + _ - *'))
print(re.findall('\d', 'jack\tn 12\n3 + _ - *'))
print(re.findall('\D', 'jack\tn 12\n3 + _ - *'))
print(re.findall('\n', 'jack\tn 12\n3 + _ - *'))
print(re.findall('\t', 'jack\tn 12\n3 + _ - *'))
print(re.findall('j', 'jack\tn 12\n3 + hello _ - *'))
print(re.findall('^j', 'jack\tn 12\n3 + hello _ - *'))
print(re.findall('o$', 'jack\tn 12\n3 + _ * hello'))
```

重复`./?/*/+/{m,n}/.*/.*?`

#### 点

`.`能匹配换行符(`\n)`以外的任意一个字符 (以`a`开头的都没匹配上，直到遇到`aab`符合`a.b`匹配规则，所以结果是`aab`)

```python
print(re.findall('a.b', 'a1b a b a-b aaaaaaaab')) # ['a1b' ,'a b' , 'a-b', 'aab']
print(re.findall('a.b', 'a1b a b a\nb aaaaaaaab')) # ['a1b','a b', 'aab']
                        #a.b
print(re.findall('a.b', 'a1b a b a\nb aaaaaaaab', re.DOTALL)) #['a1b','a b', 'a\nb','aab']
```

#### 问号

`?`代表`?`左边的字符出现0次或者1次

```python
print(re.findall('ab?', 'a ab abb abbbb a1b')) #['a' ,'ab' ,'ab','ab','a']
```

分析过程:

要匹配`ab?`。`b`可以出现1个也可以不出现。即`'a'`或者`'ab'.字母a开头`的只要能匹配就取出来。

#### 星

`*`代表`*`左边的字符出现0次或者无穷次

```python
print(re.findall('ab*', 'a ab abb abbbb a1b')) #['a', 'ab', 'abb','abbbb','a']
```

#### 加号

`+`代表`+`号左边的字符出现1次或者无穷次

```python
print(re.findall('ab+', 'a ab abb abbbbb a1b')) #['ab','abb','abbbbb']
```

分析过程:

要匹配`ab+`,`b`可以出现1个或者无穷次但不能不出现。即`'ab'或者'a无穷个b'`.需要以字母`ab`开头的才能匹配，注意和问号、星号做对比。

#### 大括号

`{m,n}`：代表左边的字符出现`m`次到`n`次

```python
print(re.findall('ab{0,1}', 'a ab abb abbbb a1b')) #等同于?效果。0次到1次
print(re.findall('ab{0,}', 'a ab abb abbbb a1b')) #等同于*效果。0次到无穷次
print(re.findall('ab{1,}', 'a ab abb abbbb a1b')) #等同于+效果。1次到无穷次
print(re.findall('ab{2,4}', 'a ab abb abbbb abbbbbbbbbbbb  a1b'))
#2次到4次，即匹配abb,abbb,abbbb ,如果出现4个以上的b只取到4个。结果为['abb','abbbb','abbbb']
```

#### 点星

`.*`代表要么不出现，要么出现无穷次。（贪婪匹配）通常匹配的不够精准。

```python
print(re.findall('a.*b', 'xxxxy123a123b456b')) #['a123b456b']
```

#### 点星问号

`.*?`非贪婪匹配和.*对应。（爬虫经常用！！！）

```python
print(re.findall('a.*?b', 'xxxxy123a123b456b')) #['a123b']
```

分析:有多个`b`时，匹配最近的一个`b`

#### 斜杠

`/`或者

#### 小括号

`()`分组，`findall()`只取分组里面的字符。`(?:)`代表分组外面的字符也保留。是固定写法！

需求:找出所有`compan`开头的英文字母

```python
print(re.findall('compan(y|ies)', 'Too many companies have gone bankrupt, and the next one is my company'))
#结果：['ies','y']
print(re.findall('compan(?:y|ies)', 'Too many companies have gone bankrupt, and the next one is my company'))
#结果：['companies','company']
```

应用取出网页中的链接

```python
print(re.findall('href="(.*?)"', '<a href="http://www.baidu.com">点击我</a>'))
```

结果:

`['http://www.baidu.com']`

`rawstring`

```python
print(re.findall(r'a\\c', 'a\\c a1c aBc')) #a\\c ->a\c ['a\\c']
```

#### 中括号

`[]`取中括号内任意一个

```python
print(re.findall('a[a-z]b', 'axb azb aAb a-b a+b')) #['axb','azb']
print(re.findall('a[A-Z]b', 'axb azb aAb a-b a+b')) #['aAb']
print(re.findall('a[a-zA-Z]b', 'axb azb aAb a-b a+b')) #['axb', 'azb', 'aAb']
print(re.findall('a[0-9]b', 'axb azb aAb a1b a-b a+b a9b')) #['a1b', 'a9b']
print(re.findall('a[-+*/]b', 'axb azb aAb a1b a-b a+b a9b')) #['a-b', 'a+b']
print(re.findall('a[^-+*/]b', 'axb azb aAb a1b a-b a+b a9b')) #['axb', 'azb', 'aAb', 'a1b', 'a9b']
```

### re模块的其他用法

`re.search`匹配成功一次就返回,取不到就返回`None`,加`.group()`返回结果。

```python
print(re.search('a[*]b', 'axb azb aAb a1b a-b a+b aaab')) #None
print(re.search('a[a-z]b', 'axb azb aAb a1b a-b a+b aaab').group()) # axb
```

`re.match`：从开头取,开头没有就为`None`,只取一次。

```python
print(re.match('a[0-9]b', 'axb azb aAb a1b a-b a2b a+b'))
print(re.match('a[0-9]b', 'a9b a8b  axb azb aAb a1b a-b a2b a+b').group())
print(re.search('^a[0-9]b', 'a9b a8b  axb azb aAb a1b a-b a2b a+b').group())
```

`re.split`:分割

```python
print(re.split(':', 'root:x:0:0::/root:/bin/bash',maxsplit=1))
#结果为：['root','x:0:0::/root:/bin/bash']
#'root:x:0:0::/root:/bin/bash'.split(':')
```

`re.sub:替换`

```python
print(re.sub('root', 'admin', 'root:x:0:0::/root:/bin/bash')) #都替换
print(re.sub('root', 'admin', 'root:x:0:0::/root:/bin/bash', 1)) #只换一次
```

需求：文本前后倒转

```python
print(re.sub('^([a-z]+)([^a-z]+)(.*?)([^a-z]+)([a-z]+)$', r'\5\2\3\4\1', 'root:x:0:0::/root:/bin/bash')) 
#只换一次
```

解析：

`^`以...开头 

`$`以...结尾 

`([a-z]+)` 分组匹配单词

 `([^a-z]+)`分组匹配非单词

`(.*?)`分组匹配任意字符

正则整体表示：以单词开头，并且以单词结尾。`r`表示显示字符原本含义不进行转义，`\5`表示第五个分组。`r'\5\2\3\4\1'` 整体表示第`5`个分组和第一个分组的内容调换
并且斜杠使用原本含义不做转义。

`re.compile` :它将正则表达式转化为对象

```python
obj = re.compile('a\d{2}b')
print(obj.findall('a12b a123b a12345b abbb')) #['a12b']
print(obj.search('a12b a123b a12345b abbb').group()) #a12b
```

常用正则表

![常用正则表](https://images-1316789231.cos.ap-shanghai.myqcloud.com/%E7%BC%96%E7%A8%8B/%E5%B8%B8%E7%94%A8%E6%AD%A3%E5%88%99%E8%A1%A8.png)

## 时间模块

```python
import time
print(time.time()) #时间戳格式：1648026287.9298368
print(time.localtime().tm_mday) #23
print(time.localtime()) #结构化时间
print(time.gmtime()) #结构化时间
#结果
#time.struct_time(tm_year=2022, tm_mon=3, tm_mday=23, tm_hour=17, tm_min=5, tm_sec=26, tm_wday=2, tm_yday=82, tm_isdst=0)
#time.struct_time(tm_year=2022, tm_mon=3, tm_mday=23, tm_hour=9, tm_min=5, tm_sec=26, tm_wday=2, tm_yday=82, tm_isdst=0)

print(time.strftime('%Y-%m-%d %H:%M:%S')) #2022-03-23 17:07:47
print(time.strftime('%Y-%m-%d %X')) #2022-03-23 17:07:47

#了解
print(time.localtime(1111111111))
print(time.localtime(time.time()))
print(time.gmtime(time.time()))
print(time.mktime(time.localtime()))
print(time.strftime('%Y-%m-%d', time.localtime()))
print(time.strptime('2020-03-01', '%Y-%m-%d'))
print(time.asctime()) #Wed Mar 23 17:15:40 2022
print(time.ctime()) #Wed Mar 23 17:15:40 2022

print(time.asctime(time.localtime())) #Wed Mar 23 17:16:32 2022
print(time.ctime(111111111)) #Tue Jul 10 08:11:51 1973
print(time.ctime(1648027065.7651727)) #Wed Mar 23 17:17:45 2022
print(time.time()) #时间戳格式：1648026287.9298368

#datetime
import datetime
print(datetime.datetime.now()) #当前时间：2022-03-23 17:20:11.964584
print(datetime.datetime.fromtimestamp(111111111)) #1973-07-10 08:11:51

print(datetime.datetime.now()+datetime.timedelta(days=3)) #当前时间开始的三天后。2022-03-26 17:22:35.581422
print(datetime.datetime.now()+datetime.timedelta(days=-3)) #当前时间开始的三天前。2022-03-20 17:22:35.581422
print(datetime.datetime.now()+datetime.timedelta(minutes=3)) #当前时间开始的三分钟后。2022-03-23 17:25:35.581422
print(datetime.datetime.now()+datetime.timedelta(seconds=3)) #当前时间开始的三秒后。2022-03-23 17:22:38.581422

print(datetime.datetime.now().replace(year=2000)) #将当前时间中年替换成2000. 2000-03-23 17:25:04.696554
```

## random模块

```python
import random
print(random.random()) #(0,1) --float 大于0且小于1之间的小数 0.8266764258093519
print(random.randint(1, 3)) #[1,3] 大于等于1且小于等于3之间的整数
print(random.randrange(1, 3)) #[1,3) 大于等于1且小于3之间的整数
print(random.choice([1, '23', [4,5]])) #1或者23或者[4,5]
print(random.sample([1,'23',[4,5]],2)) #列表元素任意2个组合 [[4, 5], '23']
print(random.uniform(1,3)) #大于1小于3的小数 2.3344194593026546

l=[1,3,4,2,5]
random.shuffle(l) #列表中元素随机打乱顺序。[5, 1, 3, 4, 2]
print(l)

#随机验证码。数字+字母
def make_code(n):
    res=''
    for i in range(n):
        s1=str(random.randint(0,9))
        s2=chr(random.randint(65,90))
        res+=random.choice([s1,s2])
    return res
print(make_code(6))
```

## os模块

```python
os.getcwd() 获取当前工作目录，即当前python脚本工作的目录路径
os.chdir("dirname")  改变当前脚本工作目录；相当于shell下cd
os.curdir  返回当前目录: ('.')
os.pardir  获取当前目录的父目录字符串名：('..')
os.makedirs('dirname1/dirname2')    可生成多层递归目录
os.removedirs('dirname1')    若目录为空，则删除，并递归到上一级目录，如若也为空，则删除，依此类推
os.mkdir('dirname')    生成单级目录；相当于shell中mkdir dirname
os.rmdir('dirname')    删除单级空目录，若目录不为空则无法删除，报错；相当于shell中rmdir dirname
os.listdir('dirname')    列出指定目录下的所有文件和子目录，包括隐藏文件，并以列表方式打印
os.remove()  删除一个文件
os.rename("oldname","newname")  重命名文件/目录
os.stat('path/filename')  获取文件/目录信息
os.sep    输出操作系统特定的路径分隔符，win下为"\\",Linux下为"/"
os.linesep    输出当前平台使用的行终止符，win下为"\t\n",Linux下为"\n"
os.pathsep    输出用于分割文件路径的字符串 win下为;,Linux下为:
os.name    输出字符串指示当前使用平台。win->'nt'; Linux->'posix'
os.system("bash command")  运行shell命令，直接显示
os.environ  获取系统环境变量
os.path.abspath(path)  返回path规范化的绝对路径
os.path.split(path)  将path分割成目录和文件名二元组返回
os.path.dirname(path)  返回path的目录。其实就是os.path.split(path)的第一个元素
os.path.basename(path)  返回path最后的文件名。如何path以／或\结尾，那么就会返回空值。即os.path.split(path)的第二个元素
os.path.exists(path)  如果path存在，返回True；如果path不存在，返回False
os.path.isabs(path)  如果path是绝对路径，返回True
os.path.isfile(path)  如果path是一个存在的文件，返回True。否则返回False
os.path.isdir(path)  如果path是一个存在的目录，则返回True。否则返回False
os.path.join(path1[, path2[, ...]])  将多个路径组合后返回，第一个绝对路径之前的参数将被忽略
os.path.getatime(path)  返回path所指向的文件或者目录的最后存取时间
os.path.getmtime(path)  返回path所指向的文件或者目录的最后修改时间
os.path.getsize(path) 返回path的大小
在Linux和Mac平台上，该函数会原样返回path，在windows平台上会将路径中所有字符转换为小写，并将所有斜杠转换为反斜杠。
>>> os.path.normcase('c:/windows\\system32\\')   
'c:\\windows\\system32\\'  
规范化路径，如..和/
>>> os.path.normpath('c://windows\\System32\\../Temp/')   
'c:\\windows\\Temp'   

>>> a='/Users/jieli/test1/\\\a1/\\\\aa.py/../..'
>>> print(os.path.normpath(a))
/Users/jieli/test1
```

举例

```python
print(os.getcwd())
print(os.stat(r'D:\pccharmcode\oracle.py'))
print(os.stat(r'D:\pccharmcode\oracle.py').st_size)
print(os.path.getsize(r'D:\pccharmcode\oracle.py'))
os.system('tasklist') #看系统进程,有编码问题。
print(os.path.split(r'D:\pccharmcode\oracle.py')) #('D:\\pccharmcode', 'oracle.py')
print(os.path.dirname(r'D:\pccharmcode\oracle.py')) #D:\pccharmcode
print(os.path.basename(r'D:\pccharmcode\oracle.py')) #oracle.py

print(os.path.isabs(r'C:\\a123123123')) #True
print(os.path.isabs(r'/root/123.txt')) #True
print(os.path.join('C:', 'D:\\', 'dir1', 'dir2', 'dir3')) #D:dir1\dir2\dir3
print(os.path.normcase('c:/windows\\SYstem32\\')) #c:\windows\system32\
print(os.path.normpath('c://windows\\System32\\../Temp/')) #c:\windows\Temp
```

路径：`D:\pccharmcode\api_key\__init__.py`
要求获取到根开始的第一级路径。

方法1：

```python
print(os.path.dirname(os.path.dirname(r'D:\pccharmcode\api_key\__init__.py')))
```

方法2：

```python
res = os.path.normpath(os.path.join(
    os.path.abspath(r'D:\pccharmcode\api_key\__init__.py'),
    os.pardir,
    os.pardir
))
print(res)
```

方法3：

先将原路径拼接成一个新路径(用`os.path.join()`)。 `D:\pccharmcode\api_key\__init__.py\..\..`

```python
BASE_DIR = os.path.normpath(
os.path.join(
    os.path.abspath(r'D:\pccharmcode\api_key\__init__.py'),
    '..',
    '..'
)
)
print(BASE_DIR)
```

## sys模块

主要掌握

```
sys.argv
sys.exit(0)
sys.path
```

其他

```
sys.argv           命令行参数List，第一个元素是程序本身路径
sys.exit(n)        退出程序，正常退出时exit(0)
sys.version        获取Python解释程序的版本信息
sys.maxint         最大的Int值
sys.path           返回模块的搜索路径，初始化时使用PYTHONPATH环境变量的值
sys.platform       返回操作系统平台名称
```

模拟下载进度条

```
print(('[%%-%ds]' %50) %('#'*1))
print(('[%%-%ds]' %50) %('#'*2))
print(('[%%-%ds]' %50) %('#'*3))
print(('[%%-%ds]' %50) %('#'*4))
print(('[%%-%ds]' %50) %('#'*5))
print(('[%%-%ds]' %50) %('#'*6))
print(('[%%-%ds]' %50) %('#'*7))
```

效果：

```
[#                                                 ]
[##                                                ]
[###                                               ]
[####                                              ]
[#####                                             ]
[######                                            ]
[#######                                           ]
```

```python
import time
import sys
def progress(percent,width=50):
    if percent >= 1:
        percent=1
    show_str = ('[%%-%ds]' % width) % ('#' * int(width*percent))
    print('\r%s %d%%' %(show_str,int(100*percent)),end='')
recv_size=0
total_size=10241
while recv_size < total_size:
    time.sleep(0.1) #模拟数据的传输延迟
    recv_size+=1024 #每次收1024
    progress(recv_size/total_size)
```

## shutil模块

高级的文件、文件夹、压缩包 处理模块。

格式：

`shutil.copyfileobj(fsrc, fdst[, length])`

将文件内容拷贝到另一个文件中

```python
import shutil
shutil.copyfileobj(open('old.xml','r'), open('new.xml', 'w'))
```

格式：

`shutil.copyfile(src, dst)`

拷贝文件

```python
shutil.copyfile('f1.log', 'f2.log') #目标文件无需存在
```

格式：

`shutil.copymode(src, dst)`

```python
仅拷贝权限。内容、组、用户均不变
shutil.copymode('f1.log', 'f2.log') #目标文件必须存在
```

格式：

`shutil.copystat(src, dst)`

```python
仅拷贝状态的信息，包括：mode bits, atime, mtime, flags
shutil.copystat('f1.log', 'f2.log') #目标文件必须存在
```

格式：

`shutil.copy(src, dst)`

```python
拷贝文件和权限
import shutil
shutil.copy('f1.log', 'f2.log')
```

格式：

`shutil.copy2(src, dst)`

```python
拷贝文件和状态信息
import shutil
shutil.copy2('f1.log', 'f2.log')
```

格式：

`shutil.ignore_patterns(*patterns)
shutil.copytree(src, dst, symlinks=False, ignore=None)`

```python
递归的去拷贝文件夹
import shutil
shutil.copytree('folder1', 'folder2', ignore=shutil.ignore_patterns('*.pyc', 'tmp*')) #目标目录不能存在，注意对folder2目录父级目录要有可写权限，ignore的意思是排除 
```

拷贝软连接:

```python
import shutil
shutil.copytree('f1', 'f2', symlinks=True, ignore=shutil.ignore_patterns('*.pyc', 'tmp*'))
```

通常的拷贝都把软连接拷贝成硬链接，即对待软连接来说，创建新的文件.

格式：

`shutil.rmtree(path[, ignore_errors[, onerror]])`

```python
递归的去删除文件
import shutil
shutil.rmtree('folder1')
```

格式：

`shutil.move(src, dst)`

```
递归的去移动文件，它类似mv命令，其实就是重命名。
shutil.move('folder1', 'folder3')
```

压缩与解压缩

格式：

`shutil.make_archive(base_name, format,...)`

创建压缩包并返回文件路径，例如：zip、tar.

```
base_name： 压缩包的文件名，也可以是压缩包的路径。只是文件名时，则保存至当前目录，否则保存至指定路径，
如 data_bak                       =>保存至当前路径
如：/tmp/data_bak =>保存至/tmp/
format： 压缩包种类，“zip”, “tar”, “bztar”，“gztar”
root_dir： 要压缩的文件夹路径（默认当前目录）
owner： 用户，默认当前用户
group： 组，默认当前组
logger： 用于记录日志，通常是logging.Logger对象
```

将 `/data` 下的文件打包放置当前程序目录

```python
import shutil
ret = shutil.make_archive("data_bak", 'gztar', root_dir='/data')
```

将 `/data`下的文件打包放置 `/tmp/`目录

```python
import shutil
ret = shutil.make_archive("/tmp/data_bak", 'gztar', root_dir='/data') 
```

`shutil `对压缩包的处理是调用 `ZipFile` 和 `TarFile` 两个模块来进行的.

详细：

压缩

```python
z = zipfile.ZipFile('laxi.zip', 'w')
z.write('a.log')
z.write('data.data')
z.close()
```

解压

```python
z = zipfile.ZipFile('laxi.zip', 'r')
z.extractall(path='.')
z.close()
```

使用`tarfile`压缩与解压缩

```
import tarfile
```

压缩

```python
t=tarfile.open('/tmp/egon.tar','w')
t.add('/test1/a.py',arcname='a.bak')
t.add('/test1/b.py',arcname='b.bak')
t.close()
```

解压缩

```python
t=tarfile.open('/tmp/egon.tar','r')
t.extractall('/egon')
t.close()
```

练习：

将`ATM`文件夹以`tar.gz`方式压缩，然后解压到`aaa`文件夹内。最后删除`aaa`文件夹。

```python
import shutil
import tarfile
#压缩
shutil.make_archive("backup", 'gztar', root_dir=r'D:\pccharmcode\ATM')
#解压
o = tarfile.open('backup.tar.gz', 'r')
o.extractall(r'D:\pccharmcode\aaa')
o.close()
#删除aaa文件夹
shutil.rmtree(r'D:\pccharmcode\aaa')
```

## xml模块

```
from xml.etree import ElementTree
tree = ElementTree.parse('a.xml')
root = tree.getroot()
# print(root.tag)
# print(root.attrib)
# print(root.text)
```

三种查找方式

从子节点中查找

显示一种

```python
print(root.find('country')) #<Element 'country' at 0x000001E26EDCC130>
#加.tag .attrib .text就可以显示内容
```

显示所有。以列表的形式显示。

```python
print(root.findall('country')) 
#[<Element 'country' at 0x0000027D6F00C0E0>, <Element 'country' at 0x0000027D6F00C2C0>, <Element 'country' at 0x0000027D6F00C450>]
```

注意：如果找不到就会显示` None`

从树形结构中查找

```python
root.iter()
print(list(root.iter('country')))
```

### 遍历文档

```python
from xml.etree import ElementTree
tree = ElementTree.parse('a.xml')
root = tree.getroot()
for country in root:
    print('==========>', country.tag, country.attrib, country.attrib['name'])
    for rank in country:
        print(rank.tag, rank.attrib, rank.text)
```

结果

```
==========> country {'name': 'Liechtenstein'} Liechtenstein
rank {'updated': 'yes'} 2
year {} 2008
gdppc {} 141100
neighbor {'name': 'Austria', 'direction': 'E'} None
neighbor {'name': 'Switzerland', 'direction': 'W'} None
==========> country {'name': 'Singapore'} Singapore
rank {'updated': 'yes'} 5
year {} 2011
gdppc {} 59900
neighbor {'name': 'Malaysia', 'direction': 'N'} None
==========> country {'name': 'Panama'} Panama
rank {'updated': 'yes'} 69
year {} 2011
gdppc {} 13600
neighbor {'name': 'Costa Rica', 'direction': 'W'} None
neighbor {'name': 'Colombia', 'direction': 'E'} None
```

#### 只遍历year节点

```python
for year in root.iter('year'):
    print(year.tag, year.text)
```

结果

```
year 2008
year 2011
year 2011
```

### 修改

```python
for c in root.iter('year'):
    new_year = int(c.text)+1
    c.text = str(new_year)
    c.set('update', 'yes')
    c.set('version', '1.0')
tree.write('b.xml')
```

### 删除

```python
for country in root.findall('country'):
    rank = int(country.find('rank').text)
    if rank > 50:
        root.remove(country)
tree.write('output.xml')
```

在`country`内添加`（append）`节点`year2`

```python
for country in root.findall('country'):
    for year in country.findall('year'):
        if int(year.text) > 2000:
            year2 = ElementTree.Element('year2')
            year2.text = 'happy-new-year'
            year2.attrib = {'update':'yes'}
            country.append(year2) #往country节点下添加子节点
tree.write('a.xml.swap')
```

## configerparser模块

`[mysqld]`叫做`section`

`port`、`basedir`等等叫做`option`

练习配置文件`my.ini`

```
[mysqld]
port = 3306
basedir = D:\\softnew\\MYSQL\\mysql-5.7.20-winx64
max_connections = 200
character-set-server = utf8
default-storage-engine = INNODB
skip-grant-table = True

[client]
user = root
password = 123
```

```python
import configparser
config = configparser.ConfigParser()
config.read('my.ini')
```

```python
print(config.sections()) #查看所有的标题
print(config.options('mysqld')) #查看标题mysqld下所有key=value的key
print(config.get('mysqld', 'port')) #查看标题mysqld下port的值=>字符串格式
```

判断有没有名字为`aaa`的`option`

```python
if config.has_option('mysqld', 'aaa'):
    print(config.get('mysqld', 'aaaa'))
```

查看值的类型

```python
print(type(config.get('mysqld', 'skip-grant-table'))) #<class 'str'>
```

转换成布尔类型

```python
print(config.getboolean('mysqld', 'skip-grant-table'))
print(config.getint('mysqld', 'port'))
print(config.getfloat('mysqld', 'port'))
```

向文件中添加section和option

```python
config.add_section('client1')
config.set('client1', 'name', 'jack')
config.set('client1', 'port', '2000')
config.write(open('my.ini', 'w', encoding='utf-8'))
```

`my.ini`文件

![image-20220326204239624](/postimages/image-20220326204239624.webp)

将`section（client1）`中`option(port)`对应的值`2000`改为`3000`

```python
config.set('client1', 'port', '3000')
config.write(open('my.ini', 'w', encoding='utf-8'))
```

查看`my.ini`文件，`port`对应的值已经改变。

## hashlib模块

### 什么叫hash?

hash是一种算法（`3.x`里代替了`md5模块`和`sha模块`，主要提供 `SHA1, SHA224, SHA256, SHA384, SHA512 ，MD5 算法`），该算法接受传入的内容，经过运算得到一串hash值。

### hash值的特点

只要传入的内容一样，得到的hash值必然一样=====>要用明文传输密码文件完整性校验。

不能由hash值返解成内容=======》把密码做成hash值，不应该在网络传输明文密码。

只要使用的hash算法不变，无论校验的内容有多大，得到的hash值长度是固定的。

hash算法就像一座工厂，工厂接收你送来的原材料（可以用`m.update()`为工厂运送原材料），经过加工返回的产品就是`hash值`。

![image-20220326213810288](/postimages/image-20220326213810288.webp)



练习

```python
import hashlib
m = hashlib.md5() # m=hashlib.sha256()
m.update('jack'.encode('utf-8'))
print(m.hexdigest()) #4ff9fc6e4e5d5f590c4f2134a8cc96d1

m.update('j'.encode('utf-8'))
print(m.hexdigest()) #636a4e0a176a6cbb43dd00aec17e64ab

m2 = hashlib.sha3_256()
m2.update('jack123'.encode('utf-8'))
print(m2.hexdigest()) #1183ac670862872ae3315c178a68dddd6166a004b0086bfa23f70e780fe7545c

m2.update('j'.encode('utf-8'))
print(m2.hexdigest()) #fe50db7bfb8a1e79934413625d2024114de0afd62bd17c3e9d97aee74f8553c6
```

注意：把一段很长的数据`update多次`，与一次`update`这段长数据，得到的结果一样。但是`update`多次为校验大文件提供了可能。

以上加密算法虽然依然非常厉害，但时候存在缺陷，即：通过撞库可以反解。所以，有必要对加密算法中添加自定义key再来做加密。

```python
import hashlib
hash = hashlib.sha256('898oaFs09f'.encode('utf-8'))
hash.update('alvin'.encode('utf-8'))
print(hash.hexdigest()) #e79e68f070cdedcfe63eaf1a2e92c83b4cfb1b5c6bc452d214c1b7e77cdfd1c7
```

模拟用户登录并把密码哈希

```python
name = input('user>>: ')
pwd = input('password>>: ')
m = hashlib.md5()
m.update(pwd.encode('utf-8'))
pwd = m.hexdigest()
print(name, pwd) # jack 1d6c1e168e362bc0092f247399003a88(jack123)
```

模拟撞库，先生成字典。

```python
cryt_pwd = '1d6c1e168e362bc0092f247399003a88'
pwds=[
    'jack1234',
    'jack',
    'jack456',
    'jack123'
]
def mack_dic(pwds):
    dic = {}
    for pwd in pwds:
        m = hashlib.md5(pwd.encode('utf-8'))
        dic[pwd] = m.hexdigest()
    return dic
dic = mack_dic(pwds)
for pwd in dic:
    if dic[pwd] == cryt_pwd:
        print(pwd)
```

`结果：jack123`

python 还有一个 `hmac 模块`，它内部对我们创建 `key` 和 内容 进行进一步的处理然后再加密

```python
import hmac
h1=hmac.new('hello'.encode('utf-8'),digestmod='md5')
h1.update('world'.encode('utf-8'))

print(h1.hexdigest())
```

```python
import hmac
m = hmac.new(key='加盐'.encode('utf-8'), msg='jack123'.encode('utf-8'), digestmod=hashlib.sha1)
print(m.hexdigest())
```

要想保证`hmac`最终结果一致，必须保证：
`hmac.new`括号内指定的初始key一样
无论`update`多少次，校验的内容累加到一起是一样的内容

```python
# 操作一
import hmac
h1=hmac.new('hello'.encode('utf-8'),digestmod='md5')
h1.update('world'.encode('utf-8'))

print(h1.hexdigest()) # 0e2564b7e100f034341ea477c23f283b

# 操作二
import hmac
h2=hmac.new('hello'.encode('utf-8'),digestmod='md5')
h2.update('w'.encode('utf-8'))
h2.update('orld'.encode('utf-8'))

print(h1.hexdigest()) # 0e2564b7e100f034341ea477c23f283b
```

## subprocess模块

在`linux`中使用`subporcess`模块可以实现shell命令效果。

在`windows`中使用可以实现`windows平台`下的命令。



举例：在`Linux`下查找`/root路径`下所有以`.txt`结尾的文件。

shell 实现

```shell
ls /root | grep txt$
```

`subprocess模块`实现

```python
import subprocess
res1=subprocess.Popen('ls /root', shell=True,stdout=subprocess.PIPE)
res=subprocess.Popen('grep txt$', shell=True, stdin=res1.stdout, stdout=subprocess.PIPE)
print(res.stdout.read().decode('utf-8'))
```

解析：

`subprocess.Popen()`执行命令`ls /root`并传入管道（`subprocess.PIPE`）并赋值给`res1`

`shell=True`指定用`shell`来执行。

`subprocess.Popen()`执行命令`grep txt$`先把`res1`传进来，然后再把整个命令作为输出，最后赋值给res。

读输出的内容，`linux平台`默认编码为`utf-8`。

结果：

```
a.txt
output.txt
url.txt
```

windows平台

查看当前系统所有任务

`shell`

```shell
tasklist
```

使用`subprocess`实现

```python
import subprocess
res=subprocess.Popen('tasklist', shell=True, stdout=subprocess.PIPE)
print(res.stdout.read().decode('gbk'))
```

注意：windows下默认编码为`gbk`。使用其他编码会出现乱码情况。

结果

```
映像名称                       PID 会话名              会话#       内存使用 
========================= ======== ================ =========== ============
System Idle Process              0 Services                   0          8 K
System                           4 Services                   0        148 K
Secure System                   88 Services                   0     73,304 K
Registry                       156 Services                   0    108,364 K
smss.exe                       532 Services                   0      1,232 K
csrss.exe                      868 Services                   0      5,128 K
Everything.exe               14296 Console                    1    110,268 K
LogiOptions.exe               2244 Console                    1     15,932 K
conhost.exe                   6812 Console                    1     11,480 K
python.exe                    9740 Console                    1     10,116 K
cmd.exe                       3740 Console                    1      4,852 K
tasklist.exe                 15428 Console                    1      9,824 K
```

官方文档[参考](https://docs.python.org/2/library/subprocess.html?highlight=subprocess#frequently-used-arguments)


