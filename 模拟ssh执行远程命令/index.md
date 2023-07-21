# 模拟ssh执行远程命令


## 模拟ssh执行远程命令

### 服务端

```python
from socket import *
import subprocess

server = socket(AF_INET, SOCK_STREAM)
server.bind(('127.0.0.1', 8090))
server.listen(5)

while True:
    conn, client_addr = server.accept()
    print(client_addr)

    while True:
        try:
            cmd = conn.recv(1024)
            if not cmd:break
            obj = subprocess.Popen(cmd.decode('utf-8'), shell=True,
                                   stdout=subprocess.PIPE,
                                   stderr=subprocess.PIPE
                                   )
            stdout = obj.stdout.read()
            stderr = obj.stderr.read()
            cmd_res = stdout+stderr
            print(len(cmd_res))
            conn.send(cmd_res)
        except ConnectionResetError:
            break

    conn.close()
server.close()

```

### 客户端

```python
from socket import *

client = socket(AF_INET, SOCK_STREAM)
client.connect(('127.0.0.1', 8090))

while True:
    cmd = input('>>: ').strip()
    if not cmd:continue
    client.send(cmd.encode('utf-8'))
    data = client.recv(1024)
    print(data.decode('gbk')) #环境时windows收包后要转成gbk格式。

client.close()
```

## 粘包现象

### 什么是粘包？

须知：只有TCP有粘包现象，UDP永远不会粘包。

首先需要掌握一个socket收发消息的原理

![image-20220412220522130](/postimages/image-20220412220522130.webp)

发送端可以是一K一K地发送数据，而接收端的应用程序可以两K两K地提走数据，当然也有可能一次提走3K或6K数据，或者一次只提走几个字节的数据，也就是说，应用程序所看到的数据是一个整体，或说是一个流（stream），一条消息有多少字节对应用程序是不可见的，因此`TCP协议`是面向流的协议，这也是容易出现粘包问题的原因。而`UDP`是面向消息的协议，每个`UDP段`都是一条消息，应用程序必须以消息为单位提取数据，不能一次提取任意字节的数据，这一点和`TCP`是很不同的。

怎样定义消息呢？可以认为对方一次性`write/send`的数据为一个消息，需要明白的是当对方`send`一条信息的时候，无论底层怎样分段分片，`TCP协议层`会把构成整条消息的数据段排序完成后才呈现在内核缓冲区。

例如基于`TCP`的套接字客户端往服务端上传文件，发送时文件内容是按照一段一段的字节流发送的，在接收方看了，根本不知道该文件的字节流从何处开始，在何处结束。

所谓粘包问题主要还是因为接收方不知道消息之间的界限，不知道一次性提取多少字节的数据所造成的。

此外，发送方引起的粘包是由`TCP协议`本身造成的，`TCP`为提高传输效率，发送方往往要收集到足够多的数据后才发送一个`TCP`段。若连续几次需要`send`的数据都很少，通常`TCP`会根据优化算法把这些数据合成一个`TCP段`后一次发送出去，这样接收方就收到了粘包数据。

### `TCP`与`UDP`

1. `TCP`（transport control protocol，传输控制协议）是面向连接的，面向流的，提供高可靠性服务。收发两端（客户端和服务器端）都要有一一成对的socket，因此，发送端为了将多个发往接收端的包，更有效的发到对方，使用了优化方法（`Nagle算法`），将多次间隔较小且数据量小的数据，合并成一个大的数据块，然后进行封包。这样，接收端，就难于分辨出来了，必须提供科学的拆包机制。 即面向流的通信是无消息保护边界的。
2. `UDP`（user datagram protocol，用户数据报协议）是无连接的，面向消息的，提供高效率服务。不会使用块的合并优化算法，, 由于`UDP`支持的是一对多的模式，所以接收端的`skbuff`(套接字缓冲区）采用了链式结构来记录每一个到达的`UDP包`，在每个`UDP包`中就有了消息头（消息来源地址，端口等信息），这样，对于接收端来说，就容易进行区分处理了。 **即面向消息的通信是有消息保护边界的。**
3. **`tcp`是基于数据流的，于是收发的消息不能为空，这就需要在客户端和服务端都添加空消息的处理机制，防止程序卡住，而`udp`是基于数据报的，即便是你输入的是空内容（直接回车），那也不是空消息，`udp协议`会帮你封装上消息头。**

`udp`的`recvfrom`是阻塞的，一个`recvfrom(x)`必须对唯一一个`sendinto(y)`,收完了`x`个字节的数据就算完成,若是`y>x`数据就丢失，这意味着`udp`根本不会粘包，但是会丢数据，不可靠。

`TCP`的协议数据不会丢，没有收完包，下次接收，会继续上次继续接收，己端总是在收到`ack`时才会清除缓冲区内容。数据是可靠的，但是会粘包。

### 两种情况下会发生粘包

发送端需要等缓冲区满才发送出去，造成粘包（发送数据时间间隔很短，数据了很小，会合到一起，产生粘包）

运行上面的服务端和客户端代码看TCP粘包现象。

结果：

```python
#服务端
('127.0.0.1', 3221)
21944 #一共21944个字节数据，只收了1024个字节数据。剩下的就会粘包。
393
393


#客户端 执行tasklist 相当于打印windows的任务管理器,然后再执行dir查看有哪些文件。
>>: tasklist

映像名称                       PID 会话名              会话#       内存使用 
========================= ======== ================ =========== ============
System Idle Process              0 Services                   0          8 K
System                           4 Services                   0        144 K
Secure System                   88 Services                   0     73,304 K
Registry                       156 Services                   0     93,616 K
smss.exe                       528 Services                   0      1,256 K
csrss.exe                      880 Services                   0      5,200 K
wininit.exe                    980 Services                   0      6,700 K
csrss.exe                      988 Console                    1      7,392 K
services.exe                   784 Services                   0     12,104 K
LsaIso.exe                     816 Services                   0      3,352 K
lsass.exe                      808 Services                   0     24,360 K
winlogon
>>: dir
.exe                  1064 Console                    1     13,052 K
svchost.exe                   1192 Services                   0     35,916 K
fontdrvhost.exe               1228 Console                    1     14,864 K
fontdrvhost.exe               1236 Services                   0      4,444 K
WUDFHost.exe                  1244 Services                   0     18,332 K
svchost.exe                   1360 Services                   0     18,016 K
svchost.exe                   1408 Services                   0      9,484 K
dwm.exe                       1512 Console                    1    154,792 K
svchost.exe                   1644 Services                   0      9,572 K
svchost.exe                   1656 Services                   0     13,208 K
svchost.exe                   1664 Services                   0     14,728 K
svchost.exe                   1680 Services                   0     11,348 K
svchost.exe                   1708 Services                   0      5,540 K
svchost.exe       
```

发现`dir`显示的不是当前目录下的内容，仿佛是上一次执行`tasklist`后内容没显示全。

接收方不及时接收缓冲区的包，造成多个包接收（客户端发送了一段数据，服务端只收了一小部分，服务端下次再收的时候还是从缓冲区拿上次遗留的数据，产生粘包） 。

### **拆包的发生情况**

当发送端缓冲区的长度大于网卡的`MTU`时，`TCP`会将这次发送的数据拆成几个数据包发送出去。

**补充问题一：为何TCP是可靠传输，UDP是不可靠传输**

`TCP`在数据传输时，发送端先把数据发送到自己的缓存中，然后协议控制将缓存中的数据发往对端，对端返回一个`ack=1`，发送端则清理缓存中的数据，对端返回`ack=0`，则重新发送数据，所以`TCP`是可靠的。

而`udp`发送数据，对端是不会返回确认信息的，因此不可靠。

**补充问题二：send(字节流)和recv(1024)及sendall**

`recv`里指定的1024意思是从缓存里一次拿出1024个字节的数据

`send`的字节流是先放入己端缓存，然后由协议控制将缓存内容发往对端，如果待发送的字节流大小大于缓存剩余空间，那么数据丢失，用`sendall`就会循环调用`send`，数据不会丢失。




