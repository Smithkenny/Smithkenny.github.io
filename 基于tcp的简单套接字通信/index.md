# 基于tcp的简单套接字通信


## 简单套接字通信

### 服务端

```python
import socket
#买手机
phone = socket.socket(socket.AF_INET, socket.SOCK_STREAM) #tcp协议

#绑定手机
# phone.setsockopt(socket.SOL_SOCKET,socket.SO_REUSEADDR,1)
phone.bind(('127.0.0.1', 9000)) #0-65535

#开机
phone.listen(5)

#等待电话连接
print('staring....')
conn, client_addr=phone.accept() #(conn,client_addr)  .accept()会阻塞，等待建立连接。
print(conn, client_addr)

#收、发消息
data = conn.recv(1024) #1024bytes? .recv()也会阻塞。等着接收数据。
conn.send(data.upper()) #.send()不会阻塞，只是发给缓存就结束了。
#挂电话连接
conn.close()

#关机
phone.close()
```

### 客户端

```python
import socket

#买手机
phone=socket.socket(socket.AF_INET, socket.SOCK_STREAM)

#拨电话
phone.connect(('127.0.0.1', 9000))

#发收消息
phone.send("hello".encode('utf-8'))

data = phone.recv(1024)
print(data)

#挂电话
phone.close()

```

结果：

```python
#服务端
staring....
<socket.socket fd=360, family=AddressFamily.AF_INET, type=SocketKind.SOCK_STREAM, proto=0, laddr=('127.0.0.1', 9000), raddr=('127.0.0.1', 18455)> ('127.0.0.1', 18455)
#客户端
b'HELLO'
```

优化：加上通信循环

服务端

```python
import socket
phone = socket.socket(socket.AF_INET, socket.SOCK_STREAM) #tcp协议
# phone.setsockopt(socket.SOL_SOCKET,socket.SO_REUSEADDR,1)
phone.bind(('127.0.0.1', 9000)) #0-65535
phone.listen(5)
print('staring....')
conn, client_addr=phone.accept() #(conn,client_addr)
print(conn, client_addr)
while True:
    data = conn.recv(1024) #1024bytes?
    print('客户端信息', data)
    conn.send(data.upper())

conn.close()
phone.close()
```

客户端

```python
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
```

防止服务端死掉，添加链接循环。并捕捉异常信号，防止客户端突然断掉导致服务端死掉。相当于服务端释放无效链接。

服务端

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

客户端

```python
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
```

效果，客户端突然死掉后，服务端会释放链接。当再次有新客户端来连接时，服务端会再开新链接，释放旧链接。

```python
#服务端
staring....
('127.0.0.1', 18939)
('127.0.0.1', 18940)
('127.0.0.1', 18941)
('127.0.0.1', 18944)
#客户端
>>: 
Process finished with exit code -1
#windows会查到链接
 TCP    127.0.0.1:9000         127.0.0.1:18944        ESTABLISHED
 TCP    127.0.0.1:18944        127.0.0.1:9000         ESTABLISHED
```


