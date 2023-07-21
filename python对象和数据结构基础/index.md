# python对象和数据结构基础


### 运算

#### 算数运算

| 运算符 |                      描述                       |                      实例                       |
| :----: | :---------------------------------------------: | :---------------------------------------------: |
|   +    |                加 - 两个对象相加                |                a + b 输出结果 30                |
|   -    |       减 - 得到负数或是一个数减去另一个数       |               a - b 输出结果 -10                |
|   *    | 乘 - 两个数相乘或是返回一个被重复若干次的字符串 |               a * b 输出结果 200                |
|   /    |                   除 - x除以y                   |                b / a 输出结果 2                 |
|   %    |              取模 - 返回除法的余数              |                b % a 输出结果 0                 |
|   **   |                幂 - 返回x的y次幂                | a**b为10的20次方，输出结果100000000000000000000 |
|   //   |            取整除一返回商的整数部分             |     9//2 输出结果 4 , 9.0//2.0 输出结果 4.0     |

#### 比较运算

| 运算符 |                             描述                             |                  实例                   |
| :----: | :----------------------------------------------------------: | :-------------------------------------: |
|   ==   |                   等于 - 比较对象是否相等                    |           (a == b)返回True。            |
|   !=   |               不等于 -  比较两个对象是否不相等               |           (a != b) 返回 True.           |
|   <>   |               不等于 -  比较两个对象是否不相等               | (a <> b) 返回 True.这个运算符类似 != 。 |
|   >    |                    大于 - 返回a是否大于b                     |           (a > b) 返回 True。           |
|   <    | 小于 - 返回a是否小于b。所有比较运算符返回1表示真，返回0表示假。这分别与特殊的变量True和False等价。注意，这些变量名的大写。 |           (a < b) 返回 True。           |
|   >=   |               大于等于 - 返回a是否大于等于b。                |          (a >= b) 返回 True。           |
|   <=   |               小于等于 - 返回x是否小于等于y。                |          (a <= b) 返回 True。           |

#### 逻辑运算

| 运算符 |                             描述                             |           实例           |
| :----: | :----------------------------------------------------------: | :----------------------: |
|  and   | 布尔"与" - 如果a为False，a and b返回False，否则它返回b的计算值。 |  (a and b) 返回 False。  |
|   or   |  布尔"或" - 如果a是True，它返回True，否则它返回b的计算值。   |   (a or b) 返回True。    |
|  not   | 布尔"非" - 如果a为True，返回False。如果b为False，它返回True。 | not(a and b) 返回 Ture。 |

#### 成员计算

| 运算符 |                         描述                         |                      实例                       |
| :----: | :--------------------------------------------------: | :---------------------------------------------: |
|   in   |   如果在指定的序列中找到值返回True，否则返回False.   |    x 在 y 序列中，如果 x 在 y 序列中返回True    |
| not in | 如果在指定的序列中没有找到值返回True，否则返回False. | x 不在 y 序列中，如果 x 不在 y 序列中返回True。 |

Python运算符优先级，以下表格列出了从最高到最低优先级的所有运算符：

|          运算符          |                        **描述**                        |
| :----------------------: | :----------------------------------------------------: |
|            **            |                   指数 (最高优先级)                    |
|          ~ + -           | 按位翻转, 一元加号和减号 (最后两个的方法名为 +@ 和 -@) |
|        \* / % //         |                  乘，除，取模和取整除                  |
|           \+ -           |                        加法减法                        |
|          \>> <<          |                    右移，左移运算符                    |
|            &             |                        位 'AND'                        |
|           ^ \|           |                        位运算符                        |
|        <= < > >=         |                       比较运算符                       |
|         <> == !=         |                       等于运算符                       |
| = %= /= //= -= += *= **= |                       赋值运算符                       |
|        is is not         |                       身份运算符                       |
|        in not in         |                       成员运算符                       |
|        not and or        |                       逻辑运算符                       |

#### 变量名

变量名的命名规则

```
名称不能以数字开头。
名称中不能有空格，用_代替。
不能使用这些符号：'",<>/?|\()!@#$%^&*~-+
名称小写被认为是最佳实践 (PEP8)。
避免使用字符 'l'（小写字母 el）、'O'（大写字母 oh）或 'I'（大写字母眼睛）作为单字符变量名称。
避免使用在 Python 中具有特殊含义的词，如“list”和“str”
```

动态类型

例如1

```python
my_dog = 2
print(my_dog)
# 结果是 2
```

例如2

```python
my_dogs = ['a','b']
print(my_dog)
# 结果是['a','b']
```

用name = object 来表示变量，一个等号代表赋值运算。

例如1

```python
a = 1
print(a)
# 结果是1
```

也可以重新赋值

```python
a = a + a 
print(a)
# 结果是2
```

捷径。+=，-=，*=，/=

例如

```python
a += 10 相当于a = a + 10
```

用type()可以确定变量的类型。type()是内置函数。常见的类型有

```python
int (for integer) #整型
float			#浮点型（小数）
str (for string)	#字符串
list				#列表
tuple				#元组
dict (for dictionary)	#字典
set					#集合
bool (for Boolean True/False)	#布尔型
```

例如

```python
a = (1,2)
type(a)
#结果：tuple
c = {1,2}
type(c)
#结果：set
```

#### 字符串

在python中用字符串记录文本信息例如名称。 Python 中的字符串实际上是一个序列，这基本上意味着 Python 将字符串中的每个元素作为一个序列进行跟踪。例如，Python 将字符串“hello”理解为按特定顺序排列的字母序列。这意味着我们将能够使用索引来获取特定的字母（如第一个字母或最后一个字母）。

要在 Python 中创建字符串，您需要使用单引号或双引号。

```python
'hello'
"hello"
#结果是相同的。hello
```

当内容需要输出单引号时，用双引号括起来

```python
"I'm ready to use the single quotes inside a string!"
```

字符串长度检查len()

```python
len('Hello World')
#结果 11，空格也算长度
```

字符串索引index

例如

```python
s = 'Hello World'
```

```python
print(s)
# 结果 Hello World
print(s[0])
#结果H
print(s[1])
#结果e
print(s[2])
#结果l
print(s[1:])
#结果ello World
print(s[:3])
#结果Hel
print(s[:])
#结果Hello World
print(s[-1])
#结果d
print(s[:-1])
#结果 Hello Worl
print(s[::1])
#结果 Hello World
print(s[::2])
#结果 HloWrd
print(s[::-1])
#结果 dlroW olleH
```

解析

```python
H  e  l  l  o     W  o  r  l  d
0  1  2  3  4  5  6  7  8  9  10     #正序从0开始
-11 -10 -9 -8 -7 -6 -5 -4 -3 -2 -1		# 倒序 从-1开始
# 规则前包括，后不包括
# 例如
s = 'hello'
print(s[1:3])
包括位置1对应的内容，不包含位置3对应的内容。所以结果为el
倒序查找也是一样的。例如
print(s[-3:-1]) #结果为ll
两个冒号后面的数字是步长。
print(s[::2]) #结果为hlo 
```

字符串一旦被创建，就不能更改或替换其中的元素了。更改会报错。

例如

```python
s = 'hello world'
s[0] = 'x'
#报错内容TypeError: 'str' object does not support item assignment
```

虽然不能更改元素内容，但字符串可以做拼接。

例如

```python
s = 'hello'
s = s + 'world'
print(s)
#结果 helloworld
```

它也支持乘法

例如

```python
letter = 'z'
print(letter**10)
#结果 zzzzzzzzzz
```

#### 内置函数调用

格式：

object.method(parameters)

对象.方法(参数)

例如

```python
s = 'hello world'
s.upper() #大写
# 结果 'HELLO WORLD'
s.lower() #小写
#结果 'hello world'
s.split('w') #分割，不包含被分割的元素
#结果 'hello ','orld'

```

打印格式化

我们可以使用 .format() 方法将格式化的对象添加到打印的字符串语句中。

例如：

```python
'Insert another string with curly brackets: {}'.format('The inserted string')
# 结果'Insert another string with curly brackets: The inserted string'
```

#### 字符串格式化

字符串格式化的三种实现方式

```
旧的方式是使用 % 充当占位符
改进的方式是使用 .format()来实现
Python 3.6 引入的最新方法使用格式化的字符串文字，称为 f-strings。
```

举例1

```python
print("I'm going to inject %s text here, and %s text here." %('some','more'))
#结果：I'm going to inject some text here, and more text here

x, y = 'some', 'more'
print("I'm going to inject %s text here, and %s text here."%(x,y))
# 结果：I'm going to inject some text here, and more text here.
```

##### 格式转换方法

比较%s 和%r使用时的不同，%s 使用的是str() 函数，%r 使用的是repr()函数。%r 能够重新创建它所代表的对象。

```python
print('I have a %r.'%'pen')
# 结果： I have a 'pen'.
print('I have a %s.'%'pen')
# 结果：I have a pen.
```

使用%s 和%d 的区别，%s 运算符将它看到的任何内容转换为字符串，包括整数和浮点数。 %d 运算符首先将数字转换为整数，而不进行四舍五入。

```python
print('I have %d yuan'%10.7)
#结果：I have 10 yuan
print('I have %s yuan'%10.7)
# 结果：I have 10.7 yuan
```

在字符串中可以使用\t

```python
print('I once caught a fish %s.' %'this \tbig')
# 结果：I once caught a fish this 	big.
print('I once caught a fish %r.' %'this \tbig')
# 结果：I once caught a fish 'this \tbig'.
```

##### 浮点数的精度

浮点数使用格式 %5.2f。这里，5 是字符串应该包含的最小字符数；如果整个数字没有这么多数字，这些可能会用空格填充。在此旁边，.2f 代表小数点后要显示多少个数字。

```python
print('Floating point numbers: %5.2f' %(13.144))
# 结果：Floating point numbers: 13.14
print('Floating point numbers: %1.0f' %(13.144))
# 结果：Floating point numbers: 13
print('Floating point numbers: %1.5f' %(13.144))
# 结果：Floating point numbers: 13.14400
print('Floating point numbers: %10.2f' %(13.144))
# 结果：Floating point numbers:      13.14
```

更多格式[参考](https://docs.python.org/3/library/stdtypes.html#old-string-formatting)

多格式转换

```python
print('First: %s, Second: %5.2f, Third: %r' %('hi!',3.1415,'bye!'))
# 结果：First: hi!, Second:  3.14, Third: 'bye!'
```

##### 使用.format()方法

和%s 方法对比有以下几个优点：

1.插入位置可以根据索引位置调用

2.可以为插入的对象分配关键字

3.插入的对象可以重复使用，避免重复

```python
print('I want to play {1},{2} and play {0}'.format('basketball','soccer','ping-pong'))
# 结果：I want to play soccer,ping-pong and play basketball
print('I have {a} years old and My grandfather  have {b} years old!'.format(a='20',b='80'))
# 结果:I have 20 years old and My grandfather  have 80 years old!
print('A %s saved is a %s earned.' %('penny','penny'))
# vs.
print('A {p} saved is a {p} earned.'.format(p='penny'))
# 结果：
A penny saved is a penny earned.
A penny saved is a penny earned.
```

使用 .format() 进行对齐、填充和精度

在花括号内可以设定字段长度、左对齐、右对齐等参数

```python
print('{0:8} | {1:9}'.format('Fruit', 'Quantity'))
print('{0:8} | {1:9}'.format('Apples', 3.))
print('{0:8} | {1:9}'.format('Oranges', 10))
# Fruit    | Quantity 
# Apples   |       3.0
# Oranges  |        10
```

默认情况下， .format() 将文本左对齐，数字右对齐。您可以传递可选的 <、^ 或 > 来设置左对齐、居中对齐或右对齐.

```python
print('{0:<8} | {1:^8} | {2:>8}'.format('Left','Center','Right'))
print('{0:<8} | {1:^8} | {2:>8}'.format(11,22,33))
# Left     |  Center  |    Right
# 11       |    22    |       33
```

也可以在对齐运算符之前添加一个填充字符

```python
print('{0:=<8} | {1:-^8} | {2:.>8}'.format('Left','Center','Right'))
print('{0:=<8} | {1:-^8} | {2:.>8}'.format(11,22,33))
Left==== | -Center- | ...Right
11====== | ---22--- | ......33
```

字段宽度和浮点精度的处理方式类似于占位符。以下两个打印语句是等价的

```python
print('This is my ten-character, two-decimal number:%10.2f' %13.579)
print('This is my ten-character, two-decimal number:{0:10.2f}'.format(13.579))
#This is my ten-character, two-decimal number:     13.58
#This is my ten-character, two-decimal number:     13.58
```

解析：要求输出10个字符并且保留小数点后2位。13.58占5个字符，剩下的5个字符用空格填充。

更多.format()使用方法[参考](https://docs.python.org/3/library/string.html#formatstrings)

##### 使用f-strings 

在 Python 3.6 中引入的 f-strings 与上述较旧的 .format() 字符串方法相比有几个好处。一方面，您可以将外部变量立即带入字符串，而不是通过 .format(var) 将它们作为参数传递。

```python
name = 'Fred'

print(f"He said his name is {name}.")
# He said his name is Fred.
```

通过 !r 获取字符串表示

```python
print(f"He said his name is {name!r}")
# He said his name is 'Fred'
```

浮点数使用以下格式 `"result: {value:{width}.{precision}}"`

```python
num = 23.45678
print("My 10 character, four decimal number is:{0:10.4f}".format(num))
print(f"My 10 character, four decimal number is:{num:{10}.{6}}")
```

```python
# My 10 character, four decimal number is:   23.4568
# My 10 character, four decimal number is:   23.4568
```

请注意，对于 f-strings，精度是指总位数，而不仅仅是小数点后的位数。这更符合科学记数法和统计分析。不幸的是，即使精度允许，f-strings也不会填充到小数点的右侧.

```python
num = 23.45
print("My 10 character, four decimal number is:{0:10.4f}".format(num))
print(f"My 10 character, four decimal number is:{num:{10}.{6}}")
```

```python
#My 10 character, four decimal number is:   23.4500
#My 10 character, four decimal number is:     23.45
```

可以在f-strings 中使用.format()的语法。

```python
num = 23.45
print("My 10 character, four decimal number is:{0:10.4f}".format(num))
print(f"My 10 character, four decimal number is:{num:10.4f}")
```

```python
#My 10 character, four decimal number is:   23.4500
#My 10 character, four decimal number is:   23.4500
```

更多用法[参考](https://docs.python.org/3/reference/lexical_analysis.html#f-strings)

#### 列表

##### 与字符串不同，它们是可变的，这意味着列表中的元素可以更改！

```python
#将列表分配给名为my_list的变量
my_list = [1,2,3]
```

##### 列表可以保存不同类型的对象

```python
my_list = ['A string',23,100.232,'o']
```

##### 可以使用len()函数统计列表长度

```python
len(my_list)
# 4
```

##### 索引和切片

```python
my_list = ['one','two','three',4,5]
# 索引从0开始
my_list[0]
# 'one'
#抓取索引 1 和它之后的所有内容
my_list[1:]
# ['two', 'three', 4, 5]
# 抓取索引 3 之前的所有内容
my_list[:3]
# ['one', 'two', 'three']
```

和字符串一样我们用+来连接列表

```python
my_list + ['new item']
# ['one', 'two', 'three', 4, 5, 'new item']
```

##### 并没有改变初始的列表

```python
my_list
```

```python
['one', 'two', 'three', 4, 5]
```

##### 必须重新分配列表才能使更改永久化

```python
my_list = my_list + ['add new item permanently']
my_list
# ['one', 'two', 'three', 4, 5, 'add new item permanently']
```

我们还可以将 * 用于类似于字符串的复制方法

```python
my_list * 2
```

结果：

```python
['one',
 'two',
 'three',
 4,
 5,
 'add new item permanently',
 'one',
 'two',
 'three',
 4,
 5,
 'add new item permanently']
```

##### 字符串复制只是临时的，不是永久生效的。

```python
my_list
#['one', 'two', 'three', 4, 5, 'add new item permanently']
```

##### python中的列表有以下两个优点

###### 没有固定大小（不用指定列表的大小）

###### 没有固定的类型约束

##### append

使用append方法将一个项目永久添加到列表的末尾

```python
list1 = [1,2,3]
list1.append('append me!')
# Show
list1
# [1, 2, 3, 'append me!']
```

##### pop

使用 pop 从列表中“弹出”一个项目。默认情况下，pop 删除最后一个索引，但您也可以指定要弹出的索引。

```python
list1.pop(0)
# 1
list1
# [2, 3, 'append me!']
```

默认的弹出索引是-1

```python
popped_item = list1.pop()
popped_item
# 'append me!'
list1
# [2, 3]
```

如果该索引处没有元素，则列表索引将返回错误。

```python
list1[100]

---------------------------------------------------------------------------
IndexError                                Traceback (most recent call last)
<ipython-input-22-af6d2015fa1f> in <module>()
----> 1 list1[100]

IndexError: list index out of range
```

##### sort and reverse

使用sort排序（在这种情况下按字母顺序，但对于数字它将升序），使用reverse反转顺序（永久性的）。

```python
new_list = ['a','e','x','b','c']
new_list
# ['a', 'e', 'x', 'b', 'c']
```

```
new_list.reverse()
new_list
# ['c', 'b', 'x', 'e', 'a']
```

```python
new_list.sort()
new_list
# ['a', 'b', 'c', 'e', 'x']
```

##### 嵌套列表

Python 数据结构的一大特点是它们支持嵌套。这意味着我们可以在数据结构中拥有数据结构。例如：列表中的列表。

```python
# Let's make three lists
lst_1=[1,2,3]
lst_2=[4,5,6]
lst_3=[7,8,9]

# Make a list of lists to form a matrix
matrix = [lst_1,lst_2,lst_3]
```

```python
matrix
# [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
```

```python
# 抓取matrix列表对象中的第一项
matrix[0]
# [1, 2, 3]
```

```python
# 抓取matrix列表对象中的第一项中的第一项
matrix[0][0]
# 1
```

通过解构 [] 中的 for 循环来构建列表理解

```python
# Build a list comprehension by deconstructing a for loop within a []
first_col = [row[0] for row in matrix]
first_col
```

```python
# [1, 4, 7]
```

#### 字典

映射是由键存储的对象的集合，与按相对位置存储对象的序列不同。这是一个重要的区别，因为映射不会保留顺序，因为它们具有由键定义的对象。

Python 字典由一个键和一个关联的值组成。该值几乎可以是任何 Python 对象。

用 {} 和 : 创建一个字典来表示一个键和一个值.

```python
my_dict = {'key1':'value1','key2':'value2'}
```

##### 通过键来调用值

```python
my_dict['key2']
# 'value2'
```

字典可以保存任何类型的数据

```python
my_dict = {'key1':123,'key2':[12,23,33],'key3':['item0','item1','item2']}
```

```python
my_dict['key3']
# ['item0', 'item1', 'item2']
```

##### 通过索引来调用值

```python
my_dict['key3'][0]
# 'item0'
```

##### 甚至可以调用该值的方法

```python
my_dict['key3'][0].upper()
# 'ITEM0'
```

我们也可以影响键的值。

```python
my_dict['key1']
# 123
my_dict['key1'] = my_dict['key1'] - 123
my_dict['key1']
# 0
```

pyhton 有内置函数 -= +=

```python
# 设置对象等于自身减去123
my_dict['key1'] -= 123
my_dict['key1']
# -123
```

我们也可以通过赋值来创建键。例如，如果我们从一个空字典开始，我们可以不断地添加。

```python
d = {}
d['animal'] = 'Dog'
d['answer'] = 42
d
# {'animal': 'Dog', 'answer': 42}
```

##### 字典中的嵌套

```python
# 嵌套在字典中的字典	
d = {'key1':{'nestkey':{'subnestkey':'value'}}}
```

```python
# 通过键来调用值
d['key1']['nestkey']['subnestkey']
# 'value'
```

##### 在字典上调用方法

```python
#创建字典
d = {'key1':1,'key2':2,'key3':3}
#列出所有键
d.keys()
#dict_keys(['key1', 'key2', 'key3'])
# 获取所有值的方法
d.values()
#dict_values([1, 2, 3])
# 返回所有项目的元组的方法
d.items()
#dict_items([('key1', 1), ('key2', 2), ('key3', 3)])
```

#### 元组

在 Python 中，**元组**与列表非常相似，但是，与列表不同，它们是**不可变**的，这意味着它们不能更改。您将使用元组来表示不应更改的内容，例如一周中的几天或日历上的日期。

##### 元组的构造使用 () 和以逗号分隔的元素

```python
# 创建一个元组
t = (1,2,3)
# 和列表一样检查长度
len(t)
# 3
```

```python
# 也可以混合对象类型
t = ('one',2)

# 查看
t
#('one', 2)
```

```python
# 和列表一样可以用索引
t[0]
#'one'
# 像列表一样切片
t[-1]
#2
```

##### 元组有内置方法，但没有列表那么多。让我们看看其中的两个.

```python
# 使用 .index 输入一个值并返回索引
t.index('one')
#0
# 使用 .count 计算一个值出现的次数
t.count('one')
#1
```

##### 不变性

```python
t[0]= 'change'
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-8-1257c0aa9edd> in <module>()
----> 1 t[0]= 'change'

TypeError: 'tuple' object does not support item assignment
```

由于这种不变性，元组不能增长。一旦创建了一个元组，我们就不能添加它。

```python
t.append('nope')
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-9-b75f5b09ac19> in <module>()
----> 1 t.append('nope')

AttributeError: 'tuple' object has no attribute 'append'
```

##### 何时使用元组

您可能想知道，“当元组可用的方法较少时，为什么还要使用元组呢？”老实说，元组在编程中不像列表那样经常使用，而是在需要不变性时使用。如果在您的程序中您正在传递一个对象并且需要确保它不会被更改，那么元组将成为您的解决方案。它提供了一个方便的数据完整性来源。

#### 集合和布尔值

集合是唯一元素的无序集合。我们可以使用 set() 函数来构造它们。让我们继续做一个设置，看看它是如何工作的。

##### 集合

```python
x = set()
# 我们使用 add() 方法添加到集合
x.add(1)
#Show
x
#{1}
```

注意大括号。这并不表示字典！尽管您可以将类比绘制为只有键的字典。

我们知道一个集合只有唯一的条目。那么当我们尝试添加已经在集合中的东西时会发生什么？

```python
# 新增一个不同的元素
x.add(2)
#Show
x
# {1, 2}
# 尝试新增一个相同的元素
x.add(1)
#Show
x
# {1, 2}
```

注意它不会在那里放置另一个 1 。那是因为一个集合只关心独特的元素！我们可以将具有多个重复元素的列表转换为一个集合以获取唯一元素。

```python
# Create a list with repeats
list1 = [1,1,2,2,3,4,5,6,1,1]
# Cast as set to get unique values
set(list1)
#{1, 2, 3, 4, 5, 6}
```

##### 布尔值

Python 带有布尔值（预定义的 True 和 False 显示基本上只是整数 1 和 0）。它还有一个名为 None 的占位符对象。

```python
# Set object to be a boolean
a = True
#Show
a
#True
```

我们还可以使用比较运算符来创建布尔值。

```python
# Output is boolean
1 > 2
#False
```

我们可以使用 None 作为我们不想重新分配的对象的占位符

```python
# None placeholder
b = None
# Show
print(b)
#None
```

#### 文件

Python 有一个内置的 open 函数，它允许我们打开和播放基本的文件类型。首先，我们需要一个文件。

用Ipython创建一个文件

```python
%%writefile test.txt
Hello, this is a quick test file.
```

在jupyter notebooks中查看当前工作路径

```python
pwd
#'/home/jovyan'
```

也可以通过文件所在路径来打开文件。

##### windows中使用2个斜杠\\\来表示路径

```python
myfile = open("C:\\Users\\YourUserName\\Home\\Folder\\myfile.txt")
```

##### linux中使用1个反斜杠/ 来表示路径

```python
myfile = open("/Users/YouUserName/Folder/myfile.txt")
```

打开之前创建的文件text.txt，并读取内容。

```python
my_file = open('test.txt')
my_file.read()
# 'Hello, this is a quick test file.'
```

再次读取内容，发现读不到内容

```python
my_file.read()
#''
```

因为第一次内容读取之后光标位置在文件末尾并且不会还原到初始位置，所以第二次读取后，光标所在位置后面没内容，所以读不到内容。这时可以重置光标位置。

```python
# Seek to the start of file (index 0)
my_file.seek(0)
#0
```

再次读取内容就可以读到了。

```python
# Now read again
my_file.read()
# 'Hello, this is a quick test file.'
```

##### 使用 readlines 方法逐行读取文件。

小心处理大文件，因为所有内容都将保存在内存中。

Readlines 返回文件中的行列表。

```python
my_file.seek(0)
my_file.readlines()
#['Hello, this is a quick test file.']
```

使用完文件后，最好关闭它。

```python
my_file.close()
```

##### 写入文件

默认情况下，open() 函数只允许我们读取文件。

##### 我们需要传递参数“w”来覆盖文件。

```python
#向函数添加第二个参数“w”，它代表写入。传递 'w+' 让我们读取和写入文件
my_file = open('test.txt','w+')
```

**打开带有 'w' 或 'w+' 的文件会截断原始文件，这意味着原始文件中的所有内容都将被删除！**

```python
# Write to the file
my_file.write('This is a new line')
```

```python
# Read the file
my_file.seek(0)
my_file.read()
#'This is a new line'
my_file.close()  # 当不再使用文件时记得关闭文件
```

传递参数 'a' 会打开文件并将指针放在末尾，因此写入的任何内容都会被附加。像 'w+' 一样，'a+' 让我们可以读取和写入文件。如果该文件不存在，将创建一个。

```python
my_file = open('test.txt','a+')
my_file.write('\nThis is text being appended to test.txt')
my_file.write('\nAnd another line here.')
# 23
```

```python
my_file.seek(0)
print(my_file.read())

This is a new line
This is text being appended to test.txt
And another line here.

my_file.close()
```

##### 用`%%writefile` 方式来附加内容

```python
%%writefile -a test.txt

This is text being appended to test.txt
And another line here.
#Appending to test.txt
```

如果您希望第一行从自己的行开始，请添加一个空格，因为 Jupyter 无法识别转义序列，如 \n。

##### 遍历文件

让我们通过遍历文本文件来快速预览 for 循环。首先让我们用一些 IPython Magic 创建一个新的文本文件

```python
%%writefile test.txt
First Line
Second Line
#Overwriting test.txt
```

现在用for循环来告诉程序每一行需要做什么

```python
for line in open('test.txt'):
    print(line)
```

```python
First Line

Second Line
```

注意

```python
我们可以将“line”对象称为任何东西（参见下面的示例）。
不使用.read()方法读取文件，就不会将整个文件存储在内存中
注意第二行打印的缩进。这个空格在 Python 中是必需的。
```

```python
# Pertaining to the first point above
for asdf in open('test.txt'):
    print(asdf)
```

```python
First Line

Second Line
```

