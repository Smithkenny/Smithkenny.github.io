# 生成器-可迭代对象-迭代器-迭代


## 可迭代对象（Iterable）

python中任意对象，只要它定义了可以返回一个迭代器的`__iter__`方法，或者定义了可以支持下标索引的`__getitem__`方法，那么他就是一个可迭代对象。可迭代对象就是能够提供迭代器的任意对象。

```
str1 = "hello"
print(str1.__iter__()) # <str_iterator object at 0x0000022BDAF5A040>
```

## 迭代器(Iterator)

任意对象，只要定义了`next`（python2）或者`__next__`方法他就是一个迭代器。

## 迭代(Iteration)

从某个地方（比如一个列表）取出一个元素的过程。当我们使用一个循环来遍历某个东西时，这个过程本身叫迭代。

## 生成器(Generators)

**生成器也是一种迭代器，但是你只能对其迭代一次**。这是因为他们并没有把所有的值都存在内存中，而是在运行时生成值。大多数时候生成器是以函数是实现的。然而，它们并不返回一个值，而是`yield`(暂且译作“生出”)一个值。

Python内置函数：`next()`。它允许我们获取一个序列的下一个元素。

```python
def generator_function():
    for i in range(3):
        yield i

gen = generator_function()
print(next(gen))
# Output: 0
print(next(gen))
# Output: 1
print(next(gen))
# Output: 2
print(next(gen))
# Output: Traceback (most recent call last):
#            File "<stdin>", line 1, in <module>
#         StopIteration
```

在`yield`掉所有的值后，`next()`触发了一个`StopIteration`的异常。基本上这个异常告诉我们，所有的值都已经被`yield`完了。

为什么我们在使用`for`循环时没有这个异常呢?`for`循环会自动捕捉到这个异常并停止调用`next()`。

Python中一些内置数据类型也支持迭代。

```python
my_string = "Yasoob"
next(my_string)
# Output: Traceback (most recent call last):
#      File "<stdin>", line 1, in <module>
#    TypeError: str object is not an iterator
```

这个异常说那个`str`对象不是一个迭代器。它是一个可迭代对象，而不是一个迭代器。这意味着它支持迭代，但我们不能直接对其进行迭代操作。怎样才能对它实施迭代呢？

内置函数，`iter`。它将根据一个可迭代对象返回一个迭代器对象。

```python
my_string = "Yasoob"
my_iter = iter(my_string)
print(next(my_iter))
# Output: 'Y'
print(type(my_iter)) # <class 'str_iterator'>
```

## 装饰器

装饰器(Decorators)是Python的一个重要部分。简单地说：他们是修改其他函数的功能的函数。



## 嵌套函数解析

### 从函数中返回函数

```python
def hi(name="yasoob"):
    def greet():
        return "now you are in the greet() function"

    def welcome():
        return "now you are in the welcome() function"

    if name == "yasoob":
        return greet
    else:
        return welcome

a = hi()
print(a)
#outputs: <function greet at 0x7f2143c01500>

#上面清晰地展示了`a`现在指向到hi()函数中的greet()函数
#现在试试这个

print(a())
#outputs: now you are in the greet() function
```

解析：

调用`hi`函数中创建的`greet`函数和`welcome`函数，作为结果输出出来。

问题：

在`if/else`语句中我们返回`greet`和`welcome`，而不是`greet()`和`welcome()`。为什么那样？这是因为当你把一对小括号放在后面，这个函数就会执行；然而如果你不放括号在它后面，那它可以被到处传递，并且可以赋值给别的变量而不去执行它。

当我们写下`a = hi()`，`hi()`会被执行，而由于`name`参数默认是*yasoob*，所以函数`greet`被返回了。如果我们把语句改为`a = hi(name = "ali")`，那么`welcome`函数将被返回。我们还可以打印出`hi()()`，这会输出*now you are in the greet() function*。

### 将函数作为参数传递给另一个参数

```python
def hi():
    return "come from hi()"

def hello(func):
    print("I am doing some boring work before executing hi()")
    print(func())

hello(hi)
    
"""
I am doing some boring work before executing hi()
come from hi()
"""
```

### 文件格式判断

常见的文件头字节码信息

```python
http://doc.chacuo.net/filehead
```

```python
import io
with open('blue.jpg', 'rb') as f:
    jpgdata = f.read()
if jpgdata.startswith(b'\xff\xd8'):
    text = u'This is a jpg file(%d bytes long)\n'
else:
    text = u'This is not a jpg file(%d bytes long)\n'

with io.open('summary.txt','w',encoding='utf-8') as outf:
    outf.write(text % len(jpgdata))
```

判断`blue.jpg`文件是不是`jpg`格式的文件。将判断结果保存到`summary.txt`.

提示：`.jpg`格式的文件头字节码为`ff d8`，注意读取`blue.jpg`文件时，要以字节方式读取。

`u`以`unicode`形式存储字符串。

## socket 模块

### udp套接字

应用层的一种编程方法

创建套接字：`socket()`

绑定IP端口: `bind()`

发送消息： `sendto()`

接受消息： `recvfrom()`

关闭套接字： `close()`

#### udp 服务端代码：

```python
# 模块导入
from socket import *

# 声明服务器IP, 端口
ADDR = ('0.0.0.0',8888)

# 创建udp套接字 SOCK_DGRAM 表示选择的时UDP套接字
udp_socket = socket(AF_INET, SOCK_DGRAM)

# 调用bind 绑定地址端口
udp_socket.bind(ADDR)

# 循环接受消息
while True:
    # 接收消息 1024 一次能接收的最大字节数
    msg, addr = udp_socket.recvfrom(1024)

    # 打印消息跟地址 decode() 解码
    print('Recv:', addr,msg.decode())
    # 回应消息
    udp_socket.sendto('收到！'.encode(),addr)
    # 约定断开通信的
    if msg == b'bye':
        break

# 关闭套接字
udp_socket.close()
```

#### udp客户端代码：

```python
# 模块导入
from socket import *

# 确定服务器的地址 127.0.0.1 跟localhost 默认时本机IP

ADDR = ('127.0.0.1',8888)

# 创建套接字
udp_socket = socket(AF_INET, SOCK_DGRAM)

# 接受消息
while True:

    msg = input('>>:')

    # 发送给服务器
    udp_socket.sendto(msg.encode(),ADDR)

    # 接受服务器消息
    data, addr = udp_socket.recvfrom(1024)
    print('来自服务器的消息:',data.decode())
    # 终止客户端循环
    if msg == b'bye':
        break

udp_socket.close()
```

### TCP编程流程

创建套接字：`socket`

绑定地址：`bind`

设置监听：`listen`

等待处理连接：`accept`

发送/接收消息：`send/recv`

关闭连接：`close`

**粘包问题**

**产生原因**

为了解决数据传输中的速率不协调问题，操作系统设置了缓冲区

实际网路工作过程比较复杂，导致消息收发不一致

tcp以字节流方式进行数据传输，在接收的时候不区分消息边界

**带来问题**

如果发送的消息每次都是独立的，需要接收端去独立解析消息时回带来消息误解问题

**解决方式**

人工设置消息边界

减缓消息发送速度

**tcp服务端代码**

```python
import socket
phone = socket.socket(socket.AF_INET, socket.SOCK_STREAM) #tcp协议
# phone.setsockopt(socket.SOL_SOCKET,socket.SO_REUSEADDR,1)
phone.bind(('127.0.0.1', 9000)) #0-65535
phone.listen(5)
print('staring....')
while True: #链接循环
    conn, client_addr=phone.accept() #(conn,client_addr)
    print(client_addr)
    while True: #通信循环
        try:
            data = conn.recv(1024) #1024bytes?
            if not data:break #针对的是Linux系统，因为linux不会抛异常。windows会抛异常。
            print('客户端信息', data)
            conn.send(data.upper())
        except ConnectionResetError:
            break
    conn.close()
phone.close()
```



**tcp客户端代码**

````python
import socket

phone=socket.socket(socket.AF_INET, socket.SOCK_STREAM)
phone.connect(('127.0.0.1', 9000))
while True: #通信循环
    msg=input('>>: ').strip()
    if not msg: continue  #防止发送空信息
    phone.send(msg.encode('utf-8'))
    data = phone.recv(1024)
    print(data)

phone.close()
````

**vscode 先运行服务端，再运行客户端。服务端和客户端分两个目录，这样可以同时运行。**

### 文件传输

#### 文件传输服务端

1.建立TCP套接字

2.等待客户端内的连接

3.接收图片内容

4.保存图片

5.终止连接

```python
# 导入模块
from socket import *
import time

# 创建套接字
socket = socket()

# 绑定IP

socket.bind(('0.0.0.0',8888))

# 设置监听
socket.listen(5)


# 创建connfd

connfd, addr = socket.accept()

# 接收客户端数据
data = connfd.recv(1024*1024)

file_name = '%d-%d-%d.jpg'%time.localtime()[:3]

f = open(file_name, 'wb')
f.write(data)
f.close()

# 关闭连接
connfd.close()
socket.close()
```

### 文件传输客户端

```python
"""
1.创建套接字
2.连接服务端
3.读取文件内容
4.发送文件内容
5.关闭连接
"""


# 导入模块
from socket import *

# 创建套接字
socket = socket()

# 连接服务端
socket.connect(('127.0.0.1',8888))

# 读入文件内容
f = open('2.jpg', 'rb')
data = f.read()

# 发送内容
socket.send(data)

f.close()

socket.close()
```




