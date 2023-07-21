# python语法


### python 语法

#### if、elif、else 语句

嘿，如果发生这种情况，请执行一些操作。否则，如果发生其他情况，请执行其他操作。否则，如果上述情况均未发生，请执行此操作。

```
if case1:
    perform action1
elif case2:
    perform action2
else: 
    perform action3
```

例如

```
x = False

if x:
    print('x was True!')
else:
    print('I will be printed in any case where x is not true')
#I will be printed in any case where x is not true
```

#### 多分支结构

```
loc = 'Bank'

if loc == 'Auto Shop':
    print('Welcome to the Auto Shop!')
elif loc == 'Bank':
    print('Welcome to the bank!')
else:
    print('Where are you?')

#Welcome to the bank!
```

#### for 循环

for 循环在 Python 中充当迭代器；它遍历序列中的项目或任何其他可迭代项目。我们了解的可以迭代的对象包括字符串、列表、元组，甚至是字典的内置可迭代对象，例如键或值。

这是 Python 中 for 循环的一般格式：

```
for item in object:
    statements to do stuff
```

##### 遍历列表

```
list1 = [1,2,3,4,5,6,7,8,9,10]
for num in list1:
    print(num)
```

结果：

```
1
2
3
4
5
6
7
8
9
10
```

取余

用 % 来获取余数

```
# 3 Remainder 1
10 % 3
# 1
# 2 Remainder 4
18 % 7
#4
# 2 no remainder
4 % 2
#0
```

请注意，如果一个数可以完全整除且没有余数，则模调用的结果为 0。我们可以使用它来测试偶数，因为如果一个数模 2 等于 0，则意味着它是偶数！

##### 打印出list1中所有的偶数

```
for num in list1:
    if num % 2 == 0:
        print(num)
```

结果：

```
2
4
6
8
10
```

也可以加上else语句

```
for num in list1:
    if num % 2 == 0:
        print(num)
    else:
        print('Odd number')
```

结果：

```
Odd number
2
Odd number
4
Odd number
6
Odd number
8
Odd number
10
```

例如

```
# Start sum at zero
list_sum = 0 

for num in list1:
    list_sum = list_sum + num

print(list_sum)
```

结果：

```
55
```

另一种写法

```
# Start sum at zero
list_sum = 0 

for num in list1:
    list_sum += num

print(list_sum)
```

结果：

```
55
```

我们已经在列表中使用了 for 循环，那么使用字符串呢？请记住字符串是一个序列，因此当我们遍历它们时，我们将访问该字符串中的每个项目。

```
for letter in 'This is a string.':
    print(letter)
```

结果：

```
T
h
i
s
 
i
s
 
a
 
s
t
r
i
n
g
.
```

##### 现在让我们看看如何将 for 循环与元组一起使用.

```
tup = (1,2,3,4,5)

for t in tup:
    print(t)
```

结果：

```
1
2
3
4
5
```

当涉及到 for 循环时，元组具有特殊的品质。如果您正在遍历包含元组的序列，则该项目实际上可以是元组本身，这是元组解包的示例。在 for 循环中，我们将解压缩序列中的元组，我们可以访问该元组中的各个项目！

```
list2 = [(2,4),(6,8),(10,12)]
for tup in list2:
    print(tup)
```

结果：

```
(2, 4)
(6, 8)
(10, 12)
```

拆包

```
for (t1,t2) in list2:
    print(t1)
```

结果

```
2
6
10
```

使用序列中的元组，我们可以通过解包访问其中的项目！这是因为许多对象将通过元组传递它们的迭代。

##### 在字典中使用for循环

```
d = {'k1':1,'k2':2,'k3':3}
for item in d:
    print(item)
```

结果

```
k1
k2
k3
```

##### 关于字典的新方法 **.keys()**, **.values()** and **.items()** 的使用

```
# Create a dictionary view object
d.items()
dict_items([('k1', 1), ('k2', 2), ('k3', 3)])	
```

由于 .items() 方法支持迭代，我们可以执行字典解包以分离键和值。

```
# 字典拆包
for k,v in d.items():
    print(k)
    print(v) 
```

结果：

```
k1
1
k2
2
k3
3
```

如果你想获得一个真正的键、值或键/值元组的列表，你可以将视图转换为一个列表。

```
list(d.keys())
```

结果

```
['k1', 'k2', 'k3']
```

请记住，字典是无序的，键和值以任意顺序返回。您可以使用 sorted() 获取排序列表。

```
sorted(d.values())
```

结果

```
[1, 2, 3]
```

更多内容[参考](https://www.tutorialspoint.com/python/python_for_loop.htm)

#### while 循环

Python 中的 while 语句是执行迭代的最常用方法之一。只要条件为真，while 语句就会重复执行单个语句或一组语句。之所以称为“循环”，是因为代码语句一遍又一遍地循环，直到不再满足条件。

##### 常用格式

```
while test:
    code statements
else:
    final code statements
```

例如

```
x = 0

while x < 10:
    print('x is currently: ',x)
    print(' x is still less than 10, adding 1 to x')
    x+=1
    
else:
    print('All Done!')
```

结果

```
x is currently:  0
 x is still less than 10, adding 1 to x
x is currently:  1
 x is still less than 10, adding 1 to x
x is currently:  2
 x is still less than 10, adding 1 to x
x is currently:  3
 x is still less than 10, adding 1 to x
x is currently:  4
 x is still less than 10, adding 1 to x
x is currently:  5
 x is still less than 10, adding 1 to x
x is currently:  6
 x is still less than 10, adding 1 to x
x is currently:  7
 x is still less than 10, adding 1 to x
x is currently:  8
 x is still less than 10, adding 1 to x
x is currently:  9
 x is still less than 10, adding 1 to x
All Done!
```

注意一共执行10次循环，当x=10时，while后条件为假，跳出循环，执行else语句。

##### 关键字break, continue,pass

我们可以在循环中使用 break、continue 和 pass 语句来为各种情况添加额外的功能。这三个语句定义为

break：跳出当前最近的封闭循环。
continue：转到最近的封闭循环的顶部。
pass：什么都不做。

考虑 break 和 continue 语句，while 循环的一般格式如下所示

```
while test: 
    code statement
    if test: 
        break
    if test: 
        continue 
else:
```

##### break 和 continue 语句可以出现在循环体内的任何位置，但我们通常会将它们与 if 语句一起进一步嵌套，以根据某些条件执行操作。

```
x = 0

while x < 10:
    print('x is currently: ',x)
    print(' x is still less than 10, adding 1 to x')
    x+=1
    if x==3:
        print('x==3')
    else:
        print('continuing...')
        continue
```

结果

```
x is currently:  0
 x is still less than 10, adding 1 to x
continuing...
x is currently:  1
 x is still less than 10, adding 1 to x
continuing...
x is currently:  2
 x is still less than 10, adding 1 to x
x==3
x is currently:  3
 x is still less than 10, adding 1 to x
continuing...
x is currently:  4
 x is still less than 10, adding 1 to x
continuing...
x is currently:  5
 x is still less than 10, adding 1 to x
continuing...
x is currently:  6
 x is still less than 10, adding 1 to x
continuing...
x is currently:  7
 x is still less than 10, adding 1 to x
continuing...
x is currently:  8
 x is still less than 10, adding 1 to x
continuing...
x is currently:  9
 x is still less than 10, adding 1 to x
continuing...
```

请注意当 x==3 时我们如何打印语句，并且当我们继续通过外部 while 循环时打印出 continue。让我们在 x ==3 时暂停一下。

```
x = 0

while x < 10:
    print('x is currently: ',x)
    print(' x is still less than 10, adding 1 to x')
    x+=1
    if x==3:
        print('Breaking because x==3')
        break
    else:
        print('continuing...')
        continue
```

结果

```
x is currently:  0
 x is still less than 10, adding 1 to x
continuing...
x is currently:  1
 x is still less than 10, adding 1 to x
continuing...
x is currently:  2
 x is still less than 10, adding 1 to x
Breaking because x==3
```

请注意，当条件成立时，遇到break后会立刻跳出当前循环，即使后面有任何语句也不会执行。

##### 不过要注意一点！可以使用 while 语句创建一个无限运行的循环。例如

```
# DO NOT RUN THIS CODE!!!! 
while True:
    print("I'm stuck in an infinite loop!")
```

不要执行这个语句，不然只能重启机器。

#### 一些有用的操作

##### range

range 函数可以让你快速生成一个整数列表，这个很方便，所以要注意如何使用它！您可以传递 3 个参数：开始、停止和步长。

```
range(0,11)
```

结果

```
range(0, 11)
```

请注意，这是一个生成器函数，因此要实际从中获取列表，我们需要将其转换为具有list（）的列表。什么是生成器？它是一种特殊类型的函数，可以生成信息，而无需将其保存到内存中。

```
# 从0到11 但不包含11，和切片一样。
list(range(0,11))
```

结果

```
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

```
# 第三个参数是步长，它表示从开始的数字到下一个数字有多长。
list(range(0,11,2))
```

结果

```
[0, 2, 4, 6, 8, 10]
```

```
list(range(0,101,10))
```

结果

```
[0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100]
```

##### 枚举函数

枚举函数是一个与循环一起使用的非常有用的函数

```
index_count = 0

for letter in 'abcde':
    print("At index {} the letter is {}".format(index_count,letter))
    index_count += 1
```

结果

```
At index 0 the letter is a
At index 1 the letter is b
At index 2 the letter is c
At index 3 the letter is d
At index 4 the letter is e
```

用枚举函数来追踪有多少循环是很常见的，这样就不用担心创建和更新index_count或letter变量了。

```
# Notice the tuple unpacking!

for i,letter in enumerate('abcde'):
    print("At index {} the letter is {}".format(i,letter))
```

结果

```
At index 0 the letter is a
At index 1 the letter is b
At index 2 the letter is c
At index 3 the letter is d
At index 4 the letter is e
```

##### zip函数

注意enumerate实际返回的格式，让我们通过将其转换为list()来看一下。

```
list(enumerate('abcde'))
```

结果

```
[(0, 'a'), (1, 'b'), (2, 'c'), (3, 'd'), (4, 'e')]
```

它是一个元组的列表，意味着我们可以在for循环中使用元组解包。这种数据结构实际上在 Python 中非常常见，特别是在与外部库一起工作时。你可以使用 zip() 函数，通过将两个列表 "压缩 "起来，快速创建一个元组列表。

```
mylist1 = [1,2,3,4,5]
mylist2 = ['a','b','c','d','e']
```

```
# 这个也是一个生成器! 我们稍后将对此进行解释，但现在让我们把它转化为一个列表。
zip(mylist1,mylist2)
```

结果

```
<zip at 0x1d205086f08>
```

```
list(zip(mylist1,mylist2))
```

结果

```
[(1, 'a'), (2, 'b'), (3, 'c'), (4, 'd'), (5, 'e')]
```

为了使用生成器，我们可以只使用一个for循环。

```
for item1, item2 in zip(mylist1,mylist2):
    print('For this tuple, first item was {} and second item was {}'.format(item1,item2))
```

结果

```
For this tuple, first item was 1 and second item was a
For this tuple, first item was 2 and second item was b
For this tuple, first item was 3 and second item was c
For this tuple, first item was 4 and second item was d
For this tuple, first item was 5 and second item was e
```

##### in 操作符

我们已经在for循环中看到了in关键字，但我们也可以用它来快速检查一个对象是否在一个列表中。

```
'x' in ['x','y','z']
```

结果

```
True
```

```
'x' in [1,2,3]
```

结果

```
False
```

##### not in

我们可以将 in 与 not 运算符结合使用，以检查列表中是否不存在某个对象或变量。

```
'x' not in ['x','y','z']
```

结果

```
False
```

```
'x' not in [1,2,3]
```

结果

```
True
```

##### min 和 max

用这些函数快速检查一个列表的最小值或最大值。

```
mylist = [10,20,30,40,100]
min(mylist)
```

结果

```
10
```

```
max(mylist)
# 100
```

##### random

Python有一个内置的随机库。这个随机库中包括很多函数，所以我们现在只向你展示两个有用的函数。

```
from random import shuffle
```

```
# 随机打乱顺序
shuffle(mylist)
mylist
```

结果

```
[40, 10, 100, 30, 20]
```

```
from random import randint
randint(0,100)
```

返回 [a, b] 范围内的随机整数，包括两个端点。

结果

```
25
```

##### input

交互

```
input('Enter Something into this box: ')
```

结果

```
Enter Something into this box: great job!
'great job!'
```

#### 列表推导

列表推导允许我们使用不同的符号构建列表。您可以将其本质上视为在括号内构建的单行 for 循环。举个简单的例子：

```
# 抓取字符串中每一个字母
list = [x for x in 'word']
list
```

结果

```
['w', 'o', 'r', 'd']
```

##### for 前面可以是表达式

```
# 在范围内的平方数并变成列表
lst = [x**2 for x in range(0,11)]
lst
```

结果

```
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

##### 另一种写法如下

```sh
list = []
for x in range(0,11):
	list.append(x**2)
```

```
list
```

结果

```
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

##### 在if 语句中使用

```
# 检查范围内的偶数
lst = [x for x in range(11) if x % 2 == 0]
lst
```

结果

```
[0, 2, 4, 6, 8, 10]
```

摄氏度和华氏度转换

```
# 摄氏度转华氏度
celsius = [0,10,20.1,34.5]

fahrenheit = [((9/5)*temp + 32) for temp in celsius ]

fahrenheit
```

结果

```
[32.0, 50.0, 68.18, 94.1]
```

##### 在嵌套中用列表推导

```
lst = [ x**2 for x in [x**2 for x in range(11)]]
lst
```

结果

```sh
[0, 1, 16, 81, 256, 625, 1296, 2401, 4096, 6561, 10000]
```

使用列表推导创建语句可读性不是很好，可以使用另一种写法

```
list1 = []
for x in range(11):
    list1.append((x**2)**2)
list1
```

##### 总结

for 前面可以是表达式，后面是要遍历的对象。in后面跟要遍历的语句。

