# python文件处理


## 文件处理流程

1.打开文件，得到文件句柄并赋值给一个变量

2.通过句柄对文件进行操作

3.关闭文件

### 两种文件操作方式

#### 直接操作（需要手动关闭文件）

```python
#1. 打开文件，得到文件句柄并赋值给一个变量
f=open('a.txt','r',encoding='utf-8') #默认打开模式就为r

#2. 通过句柄对文件进行操作
data=f.read()

#3. 关闭文件
f.close()
```

#### 使用`with`关键字（推荐！自动关闭文件无需手动操作）

```python
#可用用with同时打开多个文件，用逗号分隔开即可
with open('a.txt','r',encoding='utf-8') as read_f,open('b.txt','w',encoding='utf-8') as write_f:
    data=read_f.read()
    write_f.write(data)
```

注意：若要保证不乱码，文件以什么方式存的，就要以什么方式打开。

```python
f=open('a.txt','r',encoding='utf-8')
linux默认编码'utf-8'
windows默认编码 'gbk'
```

### python2中文件打开

```python
#首先在python3中操作文件只有一种选择，那就是open()

#而在python2中则有两种方式：file()与open()
两者都能够打开文件，对文件进行操作，也具有相似的用法和参数，但是，这两种文件打开方式有本质的区别，file为文件类，用file()来打开文件，相当于这是在构造文件类，而用open()打开文件，是用python的内建函数来操作，我们一般使用open()打开文件进行操作，而用file当做一个类型，比如type(f) is file。
```

### 打开文件的模式

#### 基础

打开文件的模式有(默认为文本模式)

```
r，只读模式【默认模式，文件必须存在，文件存在文件内指针直接跳到文件开头,不存在则抛出异常】
w，只写模式【不可读；不存在则创建；存在则清空内容,文件指针跑到文件开头.】
a，之追加写模式【不可读；不存在则创建；存在则只追加内容，文件存在会将文件指针直接移动到文件末尾】
```

#### 强调

在文件不关闭的情况下,连续的写入，后写的内容一定跟在前写内容的后面。

如果重新以w模式打开文件，则会清空文件内容。

#### `a`与`w`相同与异同

相同点：在打开的文件不关闭的情况下，连续的写入，新写的内容总会跟在前写的内容之后

不同点：以 a 模式重新打开文件，不会清空原文件内容，会将文件指针直接移动到文件末尾，新写的内容永远写在最后。

对于非文本文件，我们只能使用b模式，"b"表示以字节的方式操作（而所有文件也都是以字节的形式存储的，使用这种模式无需考虑文本文件的字符编码、图片文件的`jgp格式`、视频文件的`avi格式`）

```
rb 
wb
ab
注：以b方式打开时，读取到的内容是字节类型，写入时也需要提供字节类型，不能指定编码
```

在平时工作中，我们只单纯使用`r/w/a`，要么只读，要么只写，一般不用可读可写的模式。
大前提: `tb模式`均不能单独使用,必须与`r/w/a`之一结合使用。

```
t（默认的）：文本模式
    1. 读写文件都是以字符串为单位的
    2. 只能针对文本文件
    3. 必须指定encoding参数
b：二进制模式:
   1.读写文件都是以bytes/二进制为单位的
   2. 可以针对所有文件
   3. 一定不能指定encoding参数
```

强调：`b模式对比t模式`
1、在操作纯文本文件方面t模式帮我们省去了编码与解码的环节，b模式则需要手动编码与解码，所以此时t模式更为方便。
2、针对非文本文件（如图片、视频、音频等）只能使用b模式。

#### 拓展

```
"+" 表示可以同时读写某个文件
r+， 读写【可读，可写】
w+，写读【可读，可写】
a+， 写读【可读，可写】
x， 只写模式【不可读；不存在则创建，存在则报错】
x+ ，写读【可读，可写】
```

回车与换行

```
\n 换行
\r 回车
```

### 操作文件的方法

#### 基础

```python
f.read() #读取所有内容,光标移动到文件末尾。
f.readline() #读取一行内容,光标移动到第二行首部。
f.readlines() #读取每一行内容,存放于列表中。
f.write('11111\n222\n') #针对文本模式的写,需要自己写换行符。
f.writelines('11111\n222\n'.encode('utf-8')) #针对b模式的写,需要自己写换行符。
f.writelines(['333\n','444\n']) #文件模式
f.writelines([bytes('333\n',encoding='utf-8'),'444\n'.encode('utf-8')]) #b模式
```

#### 拓展

```
f.readable() #文件是否可读
f.writeable() #文件是否可写
f.closed #文件是否关闭
f.encoding() #如果文件打开模式为b,则没有该属性。
f.flush() #立刻将文件内容从内存刷到硬盘
f.name #显示文件名
f.tell() #显示当前光标位置
f.seek(0) #重置光标位置,光标会跳到第一行行首。
```

### 文件内光标移动

`read(3)`

```
文件打开方式为文本模式时，代表读取3个字符。
文件打开方式为b模式时，代表读取3个字节。
```

其余的文件内光标移动都是以字节为单位如`seek，tell，truncate`。

注意

`seek`有三种移动方式`0，1，2`，其中1和2必须在`b模式`下进行，但无论哪种模式，都是以`bytes`为单位移动的.

`truncate`是截断文件，所以文件的打开方式必须可写，但是不能用`w`或`w+`等方式打开，因为那样直接清空文件了，所以`truncate`要在`r+或a或a+`等模式下测试效果。

之前文件内指针的移动都是由读/写操作而被动触发的，若想读取文件某一特定位置的数据，则需要用`f.seek方法`主动控制文件内指针的移动，详细用法如下：
`f.seek`(指针移动的字节数,模式控制): 
模式控制:
0: 默认的模式,该模式代表指针移动的字节数是以文件开头为参照的
1: 该模式代表指针移动的字节数是以当前所在的位置为参照的
2: 该模式代表指针移动的字节数是以文件末尾的位置为参照的
强调:其中0模式可以在t或者b模式使用,而1跟2模式只能在b模式下用

`0模式`

```python
# a.txt用utf-8编码，内容如下（abc各占1个字节，中文“你好”各占3个字节）
abc你好

# 0模式的使用
with open('a.txt',mode='rt',encoding='utf-8') as f:
    f.seek(3,0)     # 参照文件开头移动了3个字节
    print(f.tell()) # 查看当前文件指针距离文件开头的位置，输出结果为3
    print(f.read()) # 从第3个字节的位置读到文件末尾，输出结果为：你好
    # 注意：由于在t模式下，会将读取的内容自动解码，所以必须保证读取的内容是一个完整中文数据，否则解码失败

with open('a.txt',mode='rb') as f:
    f.seek(6,0)
    print(f.read().decode('utf-8')) #输出结果为: 好
```

`1模式`

```python
1模式
with open('a.txt',mode='rb') as f:
    f.seek(3,1) # 从当前位置往后移动3个字节，而此时的当前位置就是文件开头
    print(f.tell()) # 输出结果为：3
    f.seek(4,1)     # 从当前位置往后移动4个字节，而此时的当前位置为3
    print(f.tell()) # 输出结果为：7
```



练习：使用`seek()`实现`shell中tail -f （文件日志追加功能）`

```python
import time
with open('access.log','rb') as f:
    f.seek(0, 2)
    while True:
        line = f.readline()
        if line:
            print(line.decode('utf-8'))
        else:
            time.sleep(0.2)
```

练习编写文件`copy`工具

方法1

```python
src_file=input('源文件路径: ').strip()
dst_file=input('目标文件路径: ').strip()
with open(r'%s' %src_file,mode='rb') as read_f,open(r'%s' %dst_file,mode='wb') as write_f:
    for line in read_f:
        # print(line)
        write_f.write(line)
```

方法2

```python
#任意文件拷贝
import sys #引入sys模块
_, src_file, dst_file = sys.argv #传参，_表示占位符。
with open(src_file, 'rb') as read_f,\
        open(dst_file, 'wb') as write_f:
    for line in read_f:
        write_f.write(line)
#print(sys.argv)
#使用方式 python D:\pccharmcode\begin.py 源文件路径 目的文件路径
```

### 文件的修改

文件的数据是存放于硬盘上的，因而只存在覆盖、不存在修改这么一说，我们平时看到的修改文件，都是模拟出来的效果，具体的说有两种实现方式：

方式一：将硬盘存放的该文件的内容全部加载到内存，在内存中是可以修改的，修改完毕后，再由内存覆盖到硬盘（word，vim，nodpad++等编辑器）

```python
import os

with open('a.txt') as read_f,open('.a.txt.swap','w') as write_f:
    data=read_f.read() #全部读入内存,如果文件很大,会很卡
    data=data.replace('alex','SB') #在内存中完成修改

    write_f.write(data) #一次性写入新文件

os.remove('a.txt')
os.rename('.a.txt.swap','a.txt') 
```

方式二：将硬盘存放的该文件的内容一行一行地读入内存，修改完毕就写入新文件，最后用新文件覆盖源文件

```python
import os

with open('a.txt') as read_f,open('.a.txt.swap','w') as write_f:
    for line in read_f: #读
        line=line.replace('alex','SB') #替换
        write_f.write(line)	#写入
os.remove('a.txt')
os.rename('.a.txt.swap','a.txt') 
```

### 文件的逐行读取并打印行号

```python
f = open("test.txt","r")
content = f.readlines()
i = 1 
for temp in content:
    print("%d:%s"%(i,temp))
    i += 1
```

输出

```python
1:hello world!

2:hello world!

3:hello world!

4:hello world!

5:hello world!
```








