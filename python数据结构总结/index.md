# Python数据结构总结


## 可变和不可变类型

- **不可变类型**，内存中的数据不允许被修改：
  - 数字类型 `int`, `bool`, `float`, `complex`, `long(2.x)`
  - 字符串 `str`
  - 元组 `tuple`
- **可变类型**，内存中的数据可以被修改：
  - 列表 `list`
  - 字典 `dict`
  - 集合`set`

### 哈希 (hash)

Python 中内置有一个名字叫做 hash(o) 的函数
接收一个不可变类型的数据作为参数
返回结果是一个整数
哈希是一种算法,其作用就是提取数据的特征码（指纹）
相同的内容得到相同的结果
不同的内容得到不同的结果
在Python 中，设置字典的键值对时，会首先对key进行hash已决定如何在内存中保存字典的数据，以方便后续对字典的操作：增、删、改、查
键值对的key 必须是不可变类型数据
键值对的value 可以是任意类型的数据

### 可以hash的类型

#### 元组

```bash
#任意类型转换成元组类型
tuple()
#
tuple1 = (1,2,3)
#可以hash
print(hash(tuple1)) # 529344067295497451
```

#### 字符串

```bash
#任意类型转换成字符串类型
str()
str1 = "hello world"
print(hash(str1)) # 8722840457505038127
```

#### 整形

```bash
#任意类型转换成整形类型
int()
num = 10
print(hash(num))
```

#### 浮点型

```bash
#任意类型转换成浮点类型
float()
num1 = 10.2
print(hash(num1)) 
```

#### 布尔类型

```bash
#任意类型转换成布尔类型。True False
bool()
num1 = True
print(hash(num1))
```

### 不可以hash的类型

#### 列表

```bash
#任意类型转换成列表类型
list()
#
list1 = [1,2,3,4,5]
#不可以hash
hash(list1) # TypeError: unhashable type: 'list'
```

#### 字典

```bash
# 创建一个字典
dict()
#
my_dict = {'name':'smith', 'age':18}
```

#### 集合

```bash
# 其他类型转换成集合类型
set()
set1 = {1,2,3,4,5}
```

## 函数的返回值

- 在程序开发中，有时候，会希望 **一个函数执行结束后，告诉调用者一个结果**，以便调用者针对具体的结果做后续的处理
- **返回值** 是函数 **完成工作**后，**最后** 给调用者的 **一个结果**
- 在函数中使用 `return` 关键字可以返回结果
- 调用函数一方，可以 **使用变量** 来 **接收** 函数的返回结果

### 举例1

```python
def my_func():
    a = 10
    b = 20
    # print(a)
    return a
    print(b)


my_func()
# 结果为空。没有内容输出。print(b)语句在return 语句后面，不会被执行。
```

### 举例2

设置变量来接受函数的返回结果

```bash
def my_func():
    a = 10
    b = 20
    # print(a)
    return a
    print(b)


result = my_func()
print(result)
# 结果为 10 ,设置一个变量 result 来接收函数返回结果
```


