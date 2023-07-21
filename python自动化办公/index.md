# python自动化办公之文件读写



读写文件（上）--文件与文件路径

主要是`os.path()`第三方库

1.`Windows`系统中的`\`和Linux系统中的`/`

以下代码在Terminal中执行更直观

```python
# D:\\code2022\\auto-work
# /code2022/auto-work

# 解决 \ 和 / 的问题，可以使用os.path.join()
#  os.path.join('D:\\','code2022','auto-work')
```

2.获取当前的目录

```python
os.getcwd()
# 切换到指定路径下
os.chdir('D:\\code2022')

>>> os.getcwd()
'D:\\code2022\\auto-work'
>>> os.chdir('D:\\code2022')
>>> os.getcwd()
'D:\\code2022'
```



3.绝对路径和相对路径

**绝对路径总是从根文件夹开始**

**相对路径是相对程序的当前工作目录**

```python
test1.py 的绝对路径
# D:\code2022\auto-work\test1.py
# D:\code2022\auto-work\test2.py
test2.py相对于test1.py的路径
# '.' 当前文件夹
# '..' 父文件夹
# 相对于test1.py 的路径 ../test2.py
# 相对于auto-work/test2.py 的路径 ../../test2.py 
```

```python
# 返回绝对路径
os.path.abspath('.') # 'D:\\code2022\\auto-work'
os.path.abspath('test1.py') # 'D:\\code2022\\auto-work\\test1.py'
# 返回相对路径
os.path.relpath(path[, start])
# 返回最后一个斜杠前的所有内容--目录名
os.path.dirname('D:\\code2022\\auto-work\\test1.py') # 'D:\\code2022\\auto-work'
# 返回最后一个斜杠后的所有内容--文件名
os.path.basename('D:\\code2022\\auto-work\\test1.py') # 'test1.py'
# 以元组的方式同时返回目录名和文件名
os.path.split('D:\\code2022\\auto-work\\test1.py')
# 结果：('D:\\code2022\\auto-work', 'test1.py')
# 按照斜杠裁剪,os.path.sep 会根据系统取不同的值
'D:\\code2022\\auto-work\\test1.py'.split(os.path.sep)
# 结果：['D:', 'code2022', 'auto-work', 'test1.py']
```

4.创建新文件夹（`os.mkdir()`）

在`auto-work`文件夹下创建`test3`文件夹

重复创建会报错

```python
os.mkdir('D:\\code2022\\auto-work\\test3')
```

5.查看文件大小和文件夹内容(单位是字节)

```python
os.path.getsize('test2.py') # 28

os.listdir('.') # ['test1.py', 'test2.py', 'test3']
```

这里是`os.listdir()`而不是`os.path.list()`

6.检查路径是否有效

如果提供的路径不存在，会使程序崩溃，以下函数可以检查路径是否存在

```python
>>> os.path.exists('test1.py')
True
>>> os.path.isfile('test1.py')
True
>>> os.path.isdir('test3')
True
>>>
```

7.课后练习

把`test3`文件夹下的文件按照文件类型放到不同的文件夹下

把`xlsx`文件放到`test3\xlsx`文件夹下

把`jpeg`文件放到`test3\jpeg`文件夹下

把`pptx`文件放到`test3\pptx`文件夹下

把`docx`文件放到`test3\doc`文件夹下

把`txt`文件放到`test3\txt`文件夹下

**`shutil`模块支持文件的复制和删除**

**windows和linux都能使用**

`.py`文件放到整理的文件夹内执行

```python
import os,shutil

def cleanDir():
    BaseDir = os.getcwd()
    xlsxPath = os.path.join(BaseDir,'xlsx')
    imgPath = os.path.join(BaseDir,'img')
    pptPath = os.path.join(BaseDir,'ppt')
    docPath = os.path.join(BaseDir,'doc')
    txtPath = os.path.join(BaseDir,'txt') # D:\\code2022\\auto-work\\test3\\txt
    if not os.path.exists(xlsxPath):
        os.mkdir(xlsxPath)
    if not os.path.exists(imgPath):
        os.mkdir(imgPath)
    if not os.path.exists(pptPath):
        os.mkdir(pptPath)
    if not os.path.exists(docPath):
        os.mkdir(docPath)
    if not os.path.exists(txtPath):
        os.mkdir(txtPath)

    for file in os.listdir(BaseDir):
        baseName = os.path.basename(file) # 9.txt
        filePath = os.path.join(BaseDir,file) # D:\\code2022\\auto-work\\test3\\9.txt
        if os.path.isfile(filePath):
            ex = baseName.split('.')[1] # .txt
            if ex == 'xlsx':
                shutil.move(filePath,os.path.join(xlsxPath,baseName))
            elif ex == 'jpeg':
                shutil.move(filePath,os.path.join(imgPath,baseName))
            elif ex == 'pptx':
                shutil.move(filePath,os.path.join(pptPath,baseName))
            elif ex == 'docx':
                shutil.move(filePath,os.path.join(docPath,baseName))
            elif ex == 'txt':
                shutil.move(filePath,os.path.join(txtPath,baseName))
                # 把D:\\code2022\\auto-work\\test3\\9.txt 剪切到D:\\code2022\\auto-work\\test3\\txt

cleanDir()
```

### 纯文本文件和二进制文件的区别

纯文本文件就是只包含基本文本字符，不包含字体、大小和颜色信息的，例如txt文件

二进制文件只是除了纯文本文件外的其他文件，例如pdf，图像,excel等。

#### 用程序读纯文本文件三步骤：

`open()`打开，返回`File`对象

调用`File`对象的`read()`和`write`函数

调用`File`对象的`close()`函数，关闭该文件。

#### 打开文件

```python
filePath = r'D:\code2022\auto-work\test3\txt\9.txt'
f = open(filePath,encoding='utf-8')
```

#### 读取文件内容

```python
# read() 读取所有
content = f.read()
print('txt的内容:')
print(content)
f.close()
# 按行读取
f = open(filePath)
rowIndex = 1
for line in f.readlines():
    print('txt第{0}行内容:'.format(rowIndex))
    print(line)
    rowIndex += 1
f.close()
# 规定读取字节数
f = open(filePath)
print('第一行内容：')
print(f.readline())

print('读取5个字节：')
print(f.readline(5))
f.close()
```

#### 写入文件

**写模式和添加模式**

**写模式会覆盖原来的内容**

```python
newFilePath = r'D:\code2022\auto-work\test3\txt\9.txt'
f = open(newFilePath,'w',encoding='utf-8')
f.write("你好\n")
f.close()

f = open(newFilePath, 'a',encoding='utf-8')
f.write("这是我用添加模式增加的内容\n")
f.close()
```

练习：

将`D:\code2022\auto-work\test3\txt\10.txt`中的数字转换成中文数字并且保存到源文件中

文本内容如下：

```python
我还记得我初中3年都是在4班的，成绩一直马马虎虎，在班级前5名。
然后中考就进入了县里的重点高中。
高1的时候是在8班，换了6位同桌，其中2位是女生
到高2高3，又机缘巧合分到4班，然后就一直到了毕业。
```

代码实现：

```python
def convertNum():
    filePath = r'D:\code2022\auto-work\test3\txt\10.txt'
    f = open(filePath,'r',encoding='utf-8')
    newlines = []
    for line in f.readlines():
        newline = ''
        for i in range(len(line)):
            c = line[i]
            if c == '0':
                c = '零'
            elif c == '1':
                c = '一'
            elif c == '2':
                c = '二'
            elif c == '3':
                c = '三'
            elif c == '4':
                c = '四'
            elif c == '5':
                c = '五'
            elif c == '6':
                c = '六'
            elif c == '7':
                c = '七'
            elif c == '8':
                c = '八'
            elif c == '9':
                c = '九'
            newline = newline + c 
        newlines.append(newline)
    f.close()
    f = open(r'D:\code2022\auto-work\test3\txt\11.txt','w',encoding='utf-8')
    f.writelines(newlines)
    f.close()

convertNum()
```

解读代码：

用`utf-8`编码打开文件，最外层`for`循环打印出每行形式，每一行都是一个列表。内层`for`循环，循环每一个列表中每一个字，如果碰到（0-9）就转换成相对应的汉字。保存到newlines列表中。关闭文件。创建一个新文件`11.txt`把处理后的列表写入`11.txt`中。

### shelve模块

windows 保存成三个新文件 `.bak .dat .dir`

mac/Linux 保存成一个新文件 `.db`

```python
import shelve
shelfFile = shelve.open(r'D:\code2022\auto-work\test3\txt\shelve.txt')
datas = {'username': 'smith','password':'123456'}
shelfFile['userInfo'] = datas
shelfFile.close()

shelfFile = shelve.open(r'D:\code2022\auto-work\test3\txt\shelve.txt')
print(list(shelfFile.keys()))
print(shelfFile['userInfo'])
```



### 读写文件总结

学习`os.path`的相关内容，绝对路径和相对路径，判断是否存在该路径

是否是文件或者目录，新建目录，得到该目录下所有文件等。

`File`对象，纯文本文件的读取方式，`open()`打开，`read()/write()`读写，`close()`关闭

`shelve`模块将数据保存到硬盘。

### 使用python实现大批量文件简繁体转换

需要先安装`zhconv`和`python-docx`

`ToChs`是繁体转换成简体，`ToCht`是简体转换成繁体

`ToChs`会把该文件夹下所有的docx文件全部处理成简体，保存为新文件，同理如`ToCht`.

**无论是繁体转换成简体还是繁体转换成简体，都要把程序放在和文件的同级目录内。支持表格内的文字转换**

原文章：`文章.docx`

转换后的文章：`文章繁体版.docx`

简体版同理

**简体转繁体**

```python
# 简体转繁体
import zhconv
from docx import Document
import os 
def convert(text):
    rule = 'zh-hant'
    return zhconv.convert(text,rule)

def ProcessDocx(docxName):
    print('正在处理'+docxName)
    obj = Document(docxName)
    for p in obj.paragraphs:
        p.text = convert(p.text)
    
    for t in obj.tables:
        for r in t.rows:
            for c in r.cells:
                c.text = convert(c.text)
    # 文章.docx
    # 文章繁体版.docx
    fileName = docxName.split('.')
    newDocxName = fileName[0] + "繁体版" + '.' + fileName[1]
    obj.save(newDocxName)
    print(docxName+'已经处理完毕')


if __name__ == '__main__':
    for file in os.listdir('.'):
        if file.split('.')[1] == 'docx':
            ProcessDocx(file)
```

**繁体转简体**

```python
# 繁体转换成简体
import zhconv
from docx import Document
import os
def convert(text):
    rule = 'zh-hans'
    return zhconv.convert(text,rule)
def ProcessDocx(docxName):
    print('正在处理'+docxName)
    obj = Document(docxName)
    for p in obj.paragraphs:
        p.text = convert(p.text)
    
    for t in obj.tables:
        for r in t.rows:
            for c in r.cells:
                c.text = convert(c.text)
    # 文章.docx
    # 文章简体版.docx
    fileName = docxName.split('.')
    newDocxName = fileName[0] + "简体版" + '.' + fileName[1]
    obj.save(newDocxName)
    print(docxName+'已经处理完毕')


if __name__ == '__main__':
    for file in os.listdir('.'):
        if file.split('.')[1] == 'docx':
            ProcessDocx(file)
```








