# python方法和函数


### 方法

#### 方法的格式

```python
object.method(arg1,arg2,etc...)
```

让我们快速看一下列表的各种方法的例子。

```python
# Create a simple list
lst = [1,2,3,4,5]
```

通过iPython和Jupyter笔记本，我们可以使用tab键快速查看所有可能的方法。列表中的方法有

- append
- count
- extend
- insert
- pop
- remove
- reverse
- sort

##### append() 允许我们将元素添加到列表的末尾：

```python
lst.append(6)
lst
```

结果

```python
[1, 2, 3, 4, 5, 6]
```

##### count()方法将计算一个元素在列表中出现的次数。

```python
# 查找2在列表中出现的次数
lst.count(2)
```

结果

```python
1
```

你可以随时在Jupyter笔记本中使用Shift+Tab来获得更多关于该方法的帮助。在一般的Python中，你可以使用help()函数。

```python
help(lst.count)
Help on built-in function count:

count(...) method of builtins.list instance
    L.count(value) -> integer -- return number of occurrences of value
```

### 函数

从形式上看，函数是一个有用的工具，它将一组语句组合在一起，以便它们可以被多次运行。它们还可以让我们指定参数，作为函数的输入。
在一个更基本的层面上，函数使我们不必一次又一次地重复编写相同的代码。如果你还记得关于字符串和列表的课程，记得我们用函数len()来获取一个字符串的长度。由于检查一个序列的长度是一项常见的任务，你会想写一个能在命令下反复进行的函数。
函数将是在 Python 中重复使用代码的最基本层次之一，它也将使我们开始思考程序设计（当我们学习面向对象编程时，我们将更深入地研究设计的想法）。

简单地说，当你打算多次使用一个代码块时，你应该使用函数。函数将允许你调用同一个代码块，而不需要多次编写它。这反过来又允许你创建更复杂的 Python 脚本。不过要真正理解这一点，我们应该实际写出我们自己的函数!

#### 定义关键字

让我们看看如何在Python中构建出一个函数的语法。它有如下形式。

```python
def name_of_function(arg1,arg2):
    '''
    This is where the function's Document String (docstring) goes.
    When you call help() on your function it will be printed out.
    '''
    # Do stuff here
    # Return desired result
```

解释

我们以def开头，然后是一个空格，接着是函数的名称。尽量保持名字的相关性，例如 用len()来表示 length() 函数。同样要注意名字的使用，不要和Python中内置函数（如len）同名。[内置关键字](https://docs.python.org/3/library/functions.html)

接下来是一对圆括号，里面有一些用逗号分隔的参数。这些参数是你的函数的输入。你将能够在你的函数中使用这些输入并引用它们。在这之后你要加一个冒号。

这是最重要的一步，您必须缩进才能正确开始函数内的代码。 Python 利用空格来组织代码。许多其他编程语言不这样做，所以请记住这一点。

接下来您将看到文档字符串，这是您编写函数基本描述的地方。使用 Jupyter 和 Jupyter Notebooks，您可以通过在函数名称后按 Shift+Tab 来阅读这些文档字符串。简单函数不需要文档字符串，但最好将它们放入其中，以便您或其他人可以轻松理解您编写的代码。

最后，你开始写你想执行的代码。
学习函数的最好方法是通过实例来学习。因此，让我们试着通过与我们之前学习的各种对象和数据结构相关的例子。

##### 简单的函数

```python
def say_hello():
    print('hello')
```

用函数名和括号来调用函数

```python
say_hello()
```

结果

```
hello
```

如果你忘记了小括号（），它将简单地显示 say_hello 是一个函数的事实。稍后我们将学习我们实际上可以将函数传入其他函数中！但现在，只需记住用（）调用函数就可以了。

```python
say_hello
```

结果

```python
<function __main__.say_hello>
```

##### 接受参数

```python
def greeting(name):
    print(f'Hello {name}')
```

传入参数

```python
greeting('Jose')
```

结果

```python
Hello Jose
```

注意这里如果不传入参数会报错。

```python
greeting()
```

报错如下，提示name 需要一个参数

```python
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-5-3ffcd7d44b66> in <module>
----> 1 greeting()

TypeError: greeting() missing 1 required positional argument: 'name'
```

如果用如下写法，则可以不用传入参数。因为给name 指定了默认的参数。

```python
def greeting(name=‘smith’):
    print(f'Hello {name}')
```

测试

```python
greeting()
```

结果

```python
Hello smith
```

##### 使用return关键字

到目前为止，我们只看到了 print() 的使用，但如果我们真的想保存结果变量，我们需要使用 return 关键字。让我们看一些使用 return 语句的示例。 return 允许函数返回一个结果，然后可以将其存储为变量，或以用户想要的任何方式使用。

```python
def add_num(num1,num2):
    return num1+num2
```

```python
add_num(4,5)
```

结果

```python
9
```

也可以把返回值作为变量存起来

```python
result = add_num(4,5)
```

```python
print(result)
```

结果

```python
9
```

如果输入两个字符串会发生什么？

```python
add_num('one','two')
```

```python
'onetwo'
```

##### 常见问题：print和return 有什么区别？

return关键字允许你将一个函数的输出结果实际保存为一个变量。print()函数只是向你显示输出结果，但并不为将来的使用而保存它。让我们更详细地探讨一下这个问题。

```python
def print_result(a,b):
    print(a+b)
```

```python
def return_result(a,b):
    return a+b
```

```python
print_result(10,5)
```

结果

```
15
```

如果您在 .py 脚本中运行它，您将看不到任何输出

```python
return_result(10,5)
```

结果

```
15
```

将这个结果保存在变量中后

```python
my_print = print_result(20,20)
my_print
```

结果

```
40
```

查看变量类型

```python
type(my_print)
```

```python
NoneType
```

请注意! 注意print_result()并没有让你把结果保存到一个变量中去！它只是把它打印出来，而print()在赋值时返回None! 它只是把它打印出来，print()对赋值返回无。

```python
my_result = return_result(20,20)
```

```python
my_result
```

结果

```
40
```

查看变量类型

```python
type(my_result)
```

结果是

```
int
```

变量可以重复使用

```python
my_result1 + my_result1
```

结果

```
80
```

#### 向内部函数中添加逻辑

##### 验证数字是否是偶数

如果一个数字是偶数，那么 mod 2 (% 2) 应该为零。

```python
2 % 2
```

```
0
```

```python
21 % 2
```

```
1
```

```python
20 % 2 == 0
```

```
True
```

```python
21 % 2 == 0
```

```
False
```

让我们用它来构造一个函数。是偶数返回True,不是则返回False

```python
def even_check(number):
    return number % 2 == 0
```

```python
even_check(20)
```

是偶数

```
True
```

```python
even_check(21)
```

不是偶数

```
False
```

##### 验证列表中的数字是否是偶数

让我们返回一个布尔值，指示列表中的任何数字是否为偶数。注意这里 return 如何跳出循环并退出函数。

```python
def check_even_list(num_list):
    # Go through each number
    for number in num_list:
        # Once we get a "hit" on an even number, we return True
        if number % 2 == 0:
            return True
        # Otherwise we don't do anything
        else:
            pass
```

如果都是奇数，那么没返回值了。

```python
check_even_list([1,2,3])
```

```
True
```

```python
check_even_list([1,1,1])
```

非常常见的错误，注意以下代码是错误的

```python
def check_even_list(num_list):
    # Go through each number
    for number in num_list:
        # Once we get a "hit" on an even number, we return True
        if number % 2 == 0:
            return True
        # This is WRONG! This returns False at the very first odd number!
        # It doesn't end up checking the other numbers in the list!
        else:
            return False
```

```python
# 只会检查列表中第一个位置的数字，如果第一个数字是奇数，返回False然后直接跳出循环了，不会再验证后面的数字。
check_even_list([1,2,3])
```

```
False
```

正确的做法是我们需要在运行完整个循环之后发起一个返回 False。注意缩进。

```python
def check_even_list(num_list):
    # Go through each number
    for number in num_list:
        # Once we get a "hit" on an even number, we return True
        if number % 2 == 0:
            return True
        # Don't do anything if its not even
        else:
            pass
    # Notice the indentation! This ensures we run through the entire for loop    
    return False
```

```python
check_even_list([1,2,3])
```

结果

```
True
```

```python
check_even_list([1,3,5])
```

结果

```
False
```

##### 返回列表中所有的偶数

返回列表中所有的偶数，如果没有偶数则返回空列表

```python
def check_even_list(num_list):
    
    even_numbers = []
    
    # Go through each number
    for number in num_list:
        # Once we get a "hit" on an even number, we append the even number
        if number % 2 == 0:
            even_numbers.append(number)
        # Don't do anything if its not even
        else:
            pass
    # Notice the indentation! This ensures we run through the entire for loop    
    return even_numbers
```

```python
check_even_list([1,2,3,4,5,6])
```

结果

```
[2, 4, 6]
```

```python
check_even_list([1,3,5])
```

结果

```
[]
```

##### 返回元组以进行解包

回想一下，我们可以遍历一个元组列表并“解包”其中的值

```python
stock_prices = [('AAPL',200),('GOOG',300),('MSFT',400)]
```

```python
for item in stock_prices:
    print(item)
```

结果

('AAPL', 200)
('GOOG', 300)
('MSFT', 400)

```python
for stock,price in stock_prices:
    print(stock)
```

结果

AAPL
GOOG
MSFT

```python
for stock,price in stock_prices:
    print(price)
```

结果

200
300
400

使用函数可以轻松返回多个结果

```python
work_hours = [('Abby',100),('Billy',400),('Cassie',800)]
```

自定义函数实现工作时间最长的员工姓名和工作时长（根据工作小时数来判断）

```python
def employee_check(work_hours):
    
    # Set some max value to intially beat, like zero hours
    current_max = 0
    # Set some empty value before the loop
    employee_of_month = ''
    
    for employee,hours in work_hours:
        if hours > current_max:
            current_max = hours
            employee_of_month = employee
        else:
            pass
    
    # Notice the indentation here
    return (employee_of_month,current_max)
```

```python
employee_check(work_hours)
```

结果

```
('Cassie', 800)
```

还可以分别查看姓名和工作时长

```python
name,hours = employee_check(work_hours)
```

```python
name
```

结果

'Cassie'

```python
hours
```

800

#### 函数之间交互

函数经常使用其他函数的结果，我们通过猜谜游戏来看一个简单的例子。列表中将有 3 个位置，其中一个是“O”，一个函数将对列表进行洗牌，另一个将接受玩家的猜测，最后另一个将检查它是否正确。这是基于经典的嘉年华游戏，即猜一个红球在哪个杯子下面。

##### 如何在python中打乱列表

```python
example = [1,2,3,4,5]
```

```python
from random import shuffle
```

```python
# Note shuffle is in-place
shuffle(example)
```

```python
example
```

结果

```
[3, 1, 4, 5, 2]
```

##### 下面来创建个简单的游戏

```python
mylist = [' ','O',' ']
```

```python
def shuffle_list(mylist):
    # Take in list, and returned shuffle versioned
    shuffle(mylist)
    
    return mylist
```

```python
mylist 
```

结果

```
[' ', 'O', ' ']
```

```python
shuffle_list(mylist)
```

```
[' ', ' ', 'O']
```

```python
def player_guess():
    
    guess = ''
    
    while guess not in ['0','1','2']:
        
        # Recall input() returns a string
        guess = input("Pick a number: 0, 1, or 2:  ")
    
    return int(guess)    
```

```python
player_guess()
Pick a number: 0, 1, or 2:  1
```

结果

```
1
```

现在我们将检查用户的猜测。请注意，我们只在此处打印，因为我们不需要保存用户的猜测或打乱的列表。

```python
def check_guess(mylist,guess):
    if mylist[guess] == 'O':
        print('Correct Guess!')
    else:
        print('Wrong! Better luck next time')
        print(mylist)
```

现在我们创建一个小的设置逻辑来运行所有的功能。注意它们是如何相互作用的！

```python
# Initial List
mylist = [' ','O',' ']

# Shuffle It
mixedup_list = shuffle_list(mylist)

# Get User's Guess
guess = player_guess()

# Check User's Guess
#------------------------
# Notice how this function takes in the input 
# based on the output of other functions!
check_guess(mixedup_list,guess)
```

结果

```shell
Pick a number: 0, 1, or 2:  1
Wrong! Better luck next time
[' ', ' ', 'O']
```


