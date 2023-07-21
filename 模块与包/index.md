# 模块与包


## 什么是模块？

最常见的场景，一个模块就是包含了一组功能的python文件，例如`module.py`,模块名是module。可以使用`import module`。

四个通用类别：

使用python编写的`.py`文件

已被编译为共享库或DLL的c或c++扩展

把一系列模块组织到一起的文件夹（注意：文件夹下有一个`__init__.py`文件，该文件夹称之为包）

使用C编写并连接到python解释器的内置模块

## 为什么要使用模块？

从文件级别组织程序，更方便管理。

拿来主义，提升开发效率。

## 如何使用模块？

`import 模块名`

调用模块里面的函数 `模块名.函数名`

第一次导入模块，会发生3件事，重复导入只会引用之前加载好的结果。

1.产生一个新的名称空间。

2.运行`spam.py`源文件代码，产生的名字都存放于1的名称空间中,运行过程中`global关键字`指向的就是该名称空间。

3.在当前名称空间拿到一个名字`spam`，该名字指向1的名称空间。引用`spam.py`中名字的方式：`spam.名字`

强调：被导入的模块在执行过程中使用自己的独立的名称空间作为全局名称空间。

### 起别名

`import spam as sm` 

### 一行导入多个模块

`import time,sys,spam`

### 另一种使用模块的方法

`from 模块名 import 名字`

优点：引用时不用加前缀，简单
缺点：容易与当名称空间的名字冲突

```python
from spam import money as m
from spam import money,read1,read2
from spam import * 					#	*包含除了下划线开头以外所有的名字。
```

### python文件的两种用途

1.当作脚本执行

`__name__ == '__main__'`

2.当作模块被导入使用

`__name__ == '模块名'`

```python
if __name__ == '__main__':
	pass
```

### 模块搜索路径顺序

内存-》内置模块--》`sys.path`(当前执行文件路径)

## 什么是包

包就是一个包含了`__init__.py`文件的文件夹（可以往该文件夹下放一堆子模块）

## 包的使用

注意：但凡是在导入时，出现`.`，这是导入包才有的语法，`.`的左边必须是一个包，使用的时候没有这种限制。

#把所有要导入的`.py`模块到放到一个包中，然后只需要导入包名就可以使用了。

`import package1`

练习：

在根目录下创建python package 名字为`package1`，默认会自动创建`__init__.py`
在和`package1`同级的目录下`import.py`中导入`package1`,可以使用`package1`中定义的模块.
在`package1`中创建模块`func.py`。

`func.py`内容如下：

```python
def f1():
    print('Welcome to my f1')
print('from func.py')
```

`__init__.py`内容如下：

```python
print('__init__.py yeah')
```

使用`package1`包中模块`func.py`中的`f1()`函数（在`import.py`中导入）

方法1：

```python
import package1.func
package1.func.f1()
```

方法2：

```python
from package1 import func
func.f1()
```

结果:

`__init__.py` yeah
from func.py
Welcome to my f1

分析：路径查找顺序

`__init__.py`->`func.py`->`f1()`

### 如何在pycharm中创建包？

new->Python Package

练习：

层级结构如下
/root/import.py
/root/package1
├── __init__.py
├── func.py
└── package2
    ├── __init__.py
    └── myfunc.py

`/root/package1/func.py`中的内容

```python
def f1():
    print('Welcome to my f1')
```

`/root/package1/package2/myfunc.py` 模块中内容

```python
def max1():
    print('This is a test!')
```

需求：
1.在`/root/import.py`使用`/root/package1/package2/myfunc.py `模块中`max1()` 函数
2.在`/root/import.py`使用`/root/package1/func.py `模块中`f1() `函数

解决方法：

思路：`/root/package1/__init__.py`中先导入`max1() `函数,再导入`f1() `函数

```python
from package1.package2.myfunc import max1
from package1.func import f1
```

然后在执行文件`/root/import.py`中只需要导入包`package1` 就可以直接使用`max1()`和`f1()`了。

```python
import package1 

package1.max1()
package1.f1()
```

结果如下

This is a test!
Welcome to my f1

拓展：
层级目录结构如下：
/root/import.py
/root/package1
├── __init__.py
├── func.py
├── package2
│   ├── __init__.py
│   └── myfunc.py
└── package3
    ├── __init__.py
    └── myfunc1.py

`/root/package1/package3/myfunc1.py `中的内容

```python
def f3():
    print('from package3 myfunc1.py')
```

需求：在`/root/import.py`中使用`package3`中`myfunc1`模块中的`f3`函数（使用相对路径导入）

解决：

1.在`/root/package1/__init__.py` 中先导入`f3`函数(点表示在当前路径下)

```python
from .package3.myfunc1 import f3
```

2.在`/root/import.py`中导入`package1`包即可直接使用`f3`函数

```python
import package1
package1.f3()
```

### 新下载的模块`aaa`如何导入？

```python
import sys
sys.path.append(r'aaa绝对路径')
```

### 如何连接两个或者多个路径名？

使用`os.path.join()`函数

```python
import os
print(os.path.join(r'C:', '\\', 'a', 'b', 'c.txt'))
```

结果

C:\a\b\c.txt


