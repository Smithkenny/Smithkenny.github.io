# 异常


## 异常

### 什么是异常？

异常是错误发生的信号，一旦程序出错，就会产生一个异常，应用程序未处理该异常，异常便会抛出，程序随之终止。

### 常见异常类型

#### 语法错误应该在程序运行之前就修正

```python
if 1>2
    print("xxxxxx")
```

#### 逻辑错误

```python
x #NameError
l=[10000] #IndexError
class foo:
    pass
Foo.x #AttributeError:
k={'x':1}
k['y'] #KeyError

res1=1/0
res2=1+'str' #ZeroDivisionError:无法完成计算
for i in 3:
    pass #TypeError:int类型不可迭代

num=input(">>: ") #输入hello
int(num) #ValueError
```

### 如何处理异常

```python
#基本语法为
try:
    被检测的代码块
except 异常类型：
    try中一旦检测到异常，就执行这个位置的逻辑
#举例
try:
    f=open('a.txt')
    g=(line.strip() for line in f)
    print(next(g))
    print(next(g))
    print(next(g))
    print(next(g))
    print(next(g))
except StopIteration:
    f.close()
```

异常类只能用来处理指定的异常情况，如果非指定异常则无法处理。

```python
s1 = 'hello'
try:
    int(s1)
except IndexError as e: # 未捕获到异常，程序直接报错
    print e
```

#### 多分支

```python
s1 = 'hello'
try:
    int(s1)
except IndexError as e:
    print(e)
except KeyError as e:
    print(e)
except ValueError as e:
    print(e)
```

**万能异常Exception**

```python
s1 = 'hello'
try:
    int(s1)
except Exception as e:
    print(e)
```

#### 多分支异常与万能异常

如果你想要的效果是，无论出现什么异常，我们统一丢弃，或者使用同一段代码逻辑去处理他们。

如果你想要的效果是，对于不同的异常我们需要定制不同的处理逻辑，那就需要用到多分支了。

```python
s1 = 'hello'
try:
    int(s1)
except IndexError as e:
    print(e)
except KeyError as e:
    print(e)
except ValueError as e:
    print(e)
except Exception as e:
    print(e)
```

#### 异常的其他结构

```python
s1 = 'hello'
try:
    int(s1)
except IndexError as e:
    print(e)
except KeyError as e:
    print(e)
except ValueError as e:
    print(e)
#except Exception as e:
#    print(e)
else:
    print('try内代码块没有异常则执行我')
finally:
    print('无论异常与否,都会执行该模块,通常是进行清理工作')
```

主动触发异常

```python
try:
    raise TypeError('类型错误')
except Exception as e:
    print(e)
```

自定义异常

```python
class EgonException(BaseException):
    def __init__(self,msg):
        self.msg=msg
    def __str__(self):
        return self.msg

try:
    raise EgonException('类型错误')
except EgonException as e:
    print(e)
```

断言:assert 条件

```python
assert 1 == 1  
assert 1 == 2
```

总结`try..except`

1：把错误处理和真正的工作分开来
2：代码更易组织，更清晰，复杂的工作任务更容易实现
3：毫无疑问，更安全了，不至于由于一些小的疏忽而使程序意外崩溃了

#### 多分支异常处理

功能：判断打开的文件是否存在，不存在则捕获异常，存在则每2秒逐行读取文件内容。

```python
import time

try:
    f = open("123.txt",'r')
    try:
        while True:
            content = f.readline()
            if len(content) == 0:
                break
        time.sleep(2)
        print(content)
    finally:
        f.close()
        print("文件关闭")
except Exception as result:
    print("发生异常。。。。")
```


