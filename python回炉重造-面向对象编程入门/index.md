# python回炉重造-面向对象编程入门


## 面向对象编程入门

程序是指令的集合

函数： 把相互独立且经常重复使用的代码放置到函数中，在需要使用这些代码的时候调用函数即可。

在面向对象编程的世界里，程序中的数据和操作数据的函数是一个逻辑上的整体，我们称之为**对象**。对象可以接收消息。解决问题的方法是创建对象并向对象发出各种各样的消息。通过消息的传递，程序中的多个对象可以协同工作，这样就能构造出复杂的系统并解决现实中的问题。

### 类和对象

面向对象编程：把一组数据和处理数据的方法组成**对象**，把行为相同的对象归纳为**类**，通过**封装**隐藏对象的内部细节，通过**继承**实现类的特化和泛化，通过**多态**实现基于对象类型的动态分派。



**对象（object）**

**类（class）**

**封装（encapsulation）**

**继承（inheritance）**

**多态（polymorphism）**



在面向对象编程中，**类是一个抽象的概念，对象是一个具体的概念。**我们把同一类对象的共同特征抽出来就是一个类。比如 人类 就是一个抽象概念，我们每个人就是人类的这个抽象概念下的实实在在的存在，也就是一个对象。简而言之，**类是对象的蓝图和模板，对象是类的实例，是可以接收消息的实体。**

## 类属性和类方法

**术语--实例**

1.使用面向对象开发， 先设计类

2.使用 **类名()**创建对象，**创建对象** 的动作有两步

​	1.在内存空间中为对象 **分配空间**

	2. 调用初始化方法`__init__`为对象初始化

3.对象创建后， **内存**中就有了一个对象 的**实实在在 的存在** -- 实例

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/8592/20220705213123.png"  />



因此，通常也会把：

创建出来的 **对象** 叫做 **类的实例**

创建对象的 **动作** 叫做 **实例化**

**对象的属性** 叫做 **实例属性**

**对象调用的方法** 叫做 **实例方法**

在程序执行时：

1.对象各自拥有自己的 实例属性

2.调用对象方法，可以通过`self.`

访问自己的属性

调用自己的方法

**结论**

**每一个对象** 都有自己 **独立的内存空间**，**保存各自不同的属性**

**多个对象的方法**，**在内存中只有一份**，在调用方法时，**需要把对象的引用** 传递到方法内部

### 类是一个特殊的对象

python中 **一切皆对象**

`class AAA: `定义的类属于 **类对象**

`obj1 = AAA()`属于 **实例对象**



在程序运行时， 类 同样 会被加载到内存

在 `python`中， 类是一个特殊的对象 -- 类对象

在程序运行时， 类对象 在内存中 只有一份， 使用 一个类 可以创建出 很多个对象实例

除了封装 实例的 属性 和方法外，类对象 哈可以拥有自己的 属性和方法

类属性

类方法

通过 `类名.`的方式 可以访问类的属性 或者 调用类的方法

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/8592/20220705215156.png"  />

## 定义类

在python中，用`class`关键字加上类名来定义类，通过缩进确定类的代码块。类的代码块中写函数，这些函数是对**一类对象共同的动态特征的提取**。写在类里面的函数我们称之为**方法**。方法是对象的行为，也就是对象可以接收的消息。方法的第一个参数是`self`，它代表接收这个消息的对象本身。

```python
"""
类名首字母要大写，Student类中定义了 study 和 play 两个方法。
两个方法第一个参数`self`代表接收消息的学生对象，`study`方法的第二个参数是学习的课程名称。
"""
class Student:

    def study(self, course_name):
        print(f'学生正在学习{course_name}.')

    def play(self):
        print(f'学生正在玩游戏.')
```

### 创建和使用对象

```python
# 当前的stu1 是Student这个类的对象 是一个具体的实例

stu1 = Student()
stu2 = Student()

# 当前输出的值为这个具体实例在内存中的地址 十六进制的值
print(stu1) # <__main__.Student object at 0x7f4c9b62af70>
print(stu2) # <__main__.Student object at 0x7f4c9b5ffb50>

print(hex(id(stu1)), hex(id(stu2))) # 0x7f4c9b62af70 0x7f4c9b5ffb50
```

定义变量其实是保存 一个对象在内存中的地址（十六进制形式），和用`id`函数查看的值是相同的。

通过这个逻辑地址，就能在内存中找到这个对象。

常见报错，类没有实例化。

```python
Student.study('python') # TypeError: study() missing 1 required positional argument: 'course_name'
```

正确写法，在调用类中方法时先 **进行类的实例化**。**类名后加小括号就是类的实例化。**

```python
Student().study('java')
# 学生正在学习java.
```

也可以将类实例化后赋给一个变量，像`stu1 = Student()`。后面调用类中的方法时就可以用`变量名.方法名`来调用方法。例如`stu1.play()`。

## 初始化方法

上面创建的对象只有行为没有属性，使用`__init__`方法来初始化属性。

修改`Student`类，给学生对象添加`name`（姓名）和`age`（年龄）两个属性。

```python
class Student:
    """初始化方法 给当前对象创建属性"""
    def __init__(self,name, age):
        # 给当前类添加属性
        self.name = name
        self.age = age

    def study(self, course_name):
       # 属性使用self 进行调用 参数是直接调用
        print(f'{self.name}正在学习{course_name}.')

    def play(self):
        """玩耍"""
        print(f'{self.name}正在玩游戏.')

```

由于初始化方法除了self之外还有两个参数，所以调用Student 类的构造器创建的对象时要传入这两个参数。

```python
stu1 = Student('john', 20)
stu1.study('python') # john正在学习python.

stu2 = Student('smith', 30)
stu2.play() # smith正在玩游戏.
```

## 打印对象

在python中，以两个下划线`__`、开头和结尾的方法通常都是有特殊用途和意义的方法，我们一般称之为 **魔术方法或魔法方法**。

如果我们在打印对象时候不希望看到对象的地址二十看到我们自定义的信息。可以通过在类中使用`__repr__`魔术方法来做到。该方法返回的字符串就是用`print`函数打印对象的时候会显示的内容。

```python
# 使用类中内置魔术方法来实现
def __repr__(self):
    return f'{self.name}: {self.age}'
# 类实例化    
stu1 = Student('john', 20)
# 打印当前对象
print(stu1) # john: 20
```

## 面向对象的支柱

封装： 隐藏一切可以隐藏的实现细节，只向外界暴露简单的调用接口。

在类型中定义的对象其实是一种封装。在创建对象后，只需要给对象发送一个消息就可以执行方法中的代码，也就是说我们在只知道方法名字和参数（方法外部视图），不知道方法内部实现细节（方法内部视图）的情况下就完成了对方法的使用。

根据 **职责**将**属性**和**方法**封装到一个抽象的**类**中

继承：**实现代码的重用**，相同的代码不需要重复编写

多态：不同的对象调用相同的方法，产生不同的执行结果，**增加代码的灵活度**。

## 继承

### 单继承

**继承的概念**： **子类** 拥有 **父类**的所有**方法**和**属性**

案例1：不使用继承创建两个类`动物类`和`狗类`

```python
"""
开发两个类
动物类
狗类

动物类具有四个特性
吃
喝
跑
睡

狗的特性
吃
喝
跑
睡
汪汪叫
"""

class Animal:
    
    def eat(self):
        print('吃')
    
    def drink(self):
        print('喝')
    
    def run(self):
        print('跑')
    
    def sleep(self):
        print('睡')


class Dog:

    def eat(self):
        print('吃')
    
    def drink(self):
        print('喝')
    
    def run(self):
        print('跑')
    
    def sleep(self):
        print('睡')
    
    def bark(self):
        print('汪汪叫')


laoha = Dog()

laoha.eat()
laoha.drink()
laoha.run()
laoha.sleep()
laoha.bark()

"""
当前动物类和狗类的四个方法已经重复了

如何解决这样的情况

继承
"""
```

使用继承特性较少重复代码。

```python
class Animal:
    
    def eat(self):
        print('吃')
    
    def drink(self):
        print('喝')
    
    def run(self):
        print('跑')
    
    def sleep(self):
        print('睡')


class Dog(Animal):
    
    def bark(self):
        print('汪汪叫')

# 类实例化
laoha = Dog()
# 调用类中方法
laoha.eat()
laoha.drink()
laoha.run()
laoha.sleep()
laoha.bark()
```

### 继承语法

```python
class 类名(父类名):
    pass
```

**子类** 继承自 **父类**，可以直接**享受** 父类中 已经封装好的方法，不需要再次开发。

**子类** 中应该根据 职责，封装 子类特有的属性和方法

#### 专业术语

`Dog` 类是`Animal`类的子集，`Animal`类是`Dog`类的父类，`Dog`类从 `Animal`类继承

`Dog`类是 `Animal`类的派生类，`Animal`类是`Dog`类的基类，`Dog`类从 `Animal`类派生

### 继承的传递性

`C`类从`B`类继承，`B`类又从`A`类继承

那么`C`类就具有`B`类和`A`类的所有属性和方法

**子类** 拥有 **父类** 以及 **父类的父类** 中封装的所有**属性**和**方法**。

#### 应用场景

当 父类 的方法实现不能满足子类需求时，可以对方法进行重写。

`Dog`是`XiaoTianQuan`的父类，

` Animal`又是`Dog`的父类。

所以`XiaoTianQuan`可以继承`Dog`和`Animal`中定义的所有方法。

```python
class Animal:
    
    def eat(self):
        print('吃')
    
    def drink(self):
        print('喝')
    
    def run(self):
        print('跑')
    
    def sleep(self):
        print('睡')


class Dog(Animal):
    
    def bark(self):
        print('汪汪叫')

class XiaoTianQuan(Dog):
    def fly(self):
        print("飞")

# 类实例化
wangwang = XiaoTianQuan()
# 可以调用自身定义的方法
wangwang.fly()
# 也可以调用父类以及父父类的方法
wangwang.bark()
wangwang.run()
```

### 方法的重写

子类 拥有 父类的 所有方法 和属性

子类 继承自 父类，可以直接 享受 父类中已经封装好的方法， 不需要再次开发。

重写 父类方法有两种情况：

1.覆盖 父类的方法

如何在开发中， **父类的方法实现** 和 **子类的方法实现**，完全不同。

就可以使用 **覆盖** 的方法，在子类中 **重新编写** 父类的方法实现

具体的实现方式，就相当于在 子类中定义了一个 **和父类同名的方法**并且实现。

```python
class Animal:
    
    def eat(self):
        print('吃')
    
    def drink(self):
        print('喝')
    
    def run(self):
        print('跑')
    
    def sleep(self):
        print('睡')

class Dog(Animal):
    
    def bark(self):
        print('汪汪叫')

class XiaoTianQuan(Dog):
    def fly(self):
        print("飞")
    # bark方法的重写，规则：和继承的方法名称一样。
    def bark(self):
        print("叫的和神一样....")

wangwang = XiaoTianQuan()

wangwang.bark() # 叫的和神一样....

"""
哮天犬是神犬和普通狗叫声不一样,
XiaoTianQuan 类中定义自己的方法，由于继承了父类（Dog）类的bark 方法。这里 覆盖父类的bark方法，创建一个和父类同名的方法。来区分神犬的叫声。
"""
```

#### 小结：

在子类中重写了父类的方法后，在进行方法调用时会执行子类中重写的方法。

2.对父类方法进行扩展

如果在开发中， **子类的方法实现**中 包含 **父类的方法实现**

父类原本封装的方法实现 是 **子类方法的一部分**

就可以使用 **扩展** 的方式

1.在子类中 重写 父类的方法

2.在需要的位置使用`super().父类方法`来调用父类方法的执行

3.代码其他的位置针对子类的需求，编写 子类特有的代码实现

关于 `super`

在python中`super`是一个 **特殊的类**

`super()`就是使用`super`类创建出来的对象

最常 使用的场景是 在**重写父类方法时**，调用 **在父类中封装的方法实现**。

```python
class Animal:
    
    def eat(self):
        print('吃')
    
    def drink(self):
        print('喝')
    
    def run(self):
        print('跑')
    
    def sleep(self):
        print('睡')

class Dog(Animal):
    
    def bark(self):
        print('汪汪叫')

class XiaoTianQuan(Dog):
    def fly(self):
        print("飞")


    def bark(self):
        # 在当前方法中去调用父类方法
        # 使用super方法

        print("叫的和神一样....")
        # super() 也是一个对象
        super().bark()
        # 当前的子类方法进行扩展
        print('这是一个测试...')

wangwang = XiaoTianQuan()

wangwang.bark() 
# 叫的和神一样....
# 汪汪叫
# 这是一个测试...

"""
XiaoTianQuan类不仅要调用自身的bark方法，也要调用它所继承父类（Dog）的bark方法。
使用super().方法 来实现
"""
```



调用父类方法的另外一种方式

在 `python2.x` 中，如果需要调用父类的方法，还可以使用以下方式：

```python
父类名.方法(self)
```

这种方式，目前在`python 3.x`还支持这种方式

这种方式 **不推荐使用**，因为一旦 **父类发生变化**，方法调用位置的 **类名** 同样需要修改

#### 提示

在开发中，`父类名`和`super()`两种方式不要混用

如果使用 **当前子类名** 调用方法，会形成递归调用，**出现死循环**。

### 父类的 私有属性 和私有方法

1.**子类对象 不能**在自己的方法内部，**直接**访问父类的 **私有属性**或**私有方法**

2.**子类对象** 可以通过 **父类**的**公有方法**间接访问到 **私有属性或私有方法**

私有属性、方法是对象的隐私，不对外公开，**外界**以及**子类**都不能直接访问。

示例

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/8592/20220704203316.png"  />



**代码理解父类的私有属性和私有方法**

```python
class A:
    # 创建类属性
    def __init__(self):
        # 公共属性
        self.num_1 = 100
        # 私有属性
        self.__num_2 = 200

    # 创建私有方法

    def __test(self):
        print(f'私有属性与公共属性的值：{self.num_1}, {self.__num_2}')

class B(A):
    def demo(self):
        # 父类方法
        super().__test()

"""
B 具有父类的所有属性和方法
如果是私有属性和私有方法的情况下 不可以直接去使用。
"""

b = B()

# 使用子类去打印父类的公共属性
print(b.num_1)

# 打印父类中的私有属性 我们不可以直接打印弗莱的私有属性
print(b.__num_2) # AttributeError: 'B' object has no attribute '__num_2'

# 调用父类中的私有方法 不允许子类直接去调用父类的私有方法
b.demo() # AttributeError: 'super' object has no attribute '_B__test'
```

子类访问父类的私有属性和私有方法

```python
class A:
    def __init__(self):
        self.num_1 = 100
        self.__num_2 = 200

    # 创建私有方法
    def __test(self):
        print(f'私有属性与公共属性的值：{self.num_1}, {self.__num_2}')
    
    # 创建公有方法
    def test(self):
        print(f'父类中的公共方法输出私有属性: {self.__num_2}')
        # 在公有方法中调用私有方法
        self.__test()

class B(A):
    # 公有方法
    def demo(self):
        # 1. 在子类方法中访问父类的公共属性
        print(f'子类方法输出父类中的公有属性:{self.num_1}')
        # 2. 在子类中调用父类的公有方法输出私有属性
        self.test()

b = B()
b.demo()
"""
子类方法输出父类中的公有属性:100
父类中的公共方法输出私有属性: 200
私有属性与公共属性的值：100, 200
"""
```



### 多继承

#### 概念

子类 可以拥有 多个父类，并且具有 所有父类的属性 和方法

例如： 孩子 会继承自己 父亲和母亲的特性

**语法**

```python
class 子类名(父类名1, 父类名2...):
    pass
```

举例

```python
class A:
    def test(self):
        print("test 方法")
class B:
    def demo(self):
        print("demo 方法")

class C(A, B):
    pass

# 创建C类对象

c = C()
c.test()
c.demo()

"""
在python中 面向对象是支持多个类进行继承的
子类同时具有父类中的所有方法和所有属性
"""
```

### 多继承的使用注意事项

如果 不同的父类 中存在 同名的方法，子类对象 在调用方法时，会调用 哪一个父类中的方法呢？

提示：开发时，应该尽量避免这种容易产生混淆的情况！ —— 如果 父类之间存在同名的属性或者方法，应该尽量避免使用多继承。

### python 中的MRO——方法搜索顺序

python中针对 类 提供了一个 内置属性 `__mro__`可以查看 方法 搜索顺序

MOR是 `method resolution order`,主要用于 在多继承时判断方法、属性的调用路径

```python
print(C.__mro__)
```

输出结果

```python
(<class '__main__.C'>, <class '__main__.A'>, <class '__main__.B'>, <class 'object'>)
```

在搜索方法时，是按照`__mro__`的输出结果 **从左到右**的顺序查找的

如果在当前类中**找到方法，就直接执行，不再搜索。**

如果 **没有找到，就查找下一个类** 中是否有对应的方法，如果找到，就直接执行，不再搜索。

如果找到最后一个类，还没有找到方法，程序报错。

### 新式类和旧式类（经典类）

`object`是python为所有对象提供的 **基类**，提供有一些内置的属性和方法，可以使用`dir`函数查看。

新式类： 以`object`为基类，推荐使用

经典类：  不以`object`为基类，不推荐使用

在`python 3.x`中定义类时，如果没有指定父类，会默认使用`object`作为该类的 基类——`python3.x`中定义的类都是 新式类

在`python 2.x`中定义类时，如果没有指定父类，不会以`object`作为基类。

新式类  和 经典类 在多继承时—会影响到方法的搜索顺序

为了保证编写的代码能够同时在`Python2.x`和`Python3.x`运行！今后在定义类时，如果没有父类，建议统一继承自`object`。

**在python3中如果不指定继承关系那么默认继承object。**

```python
class A(object):
    pass

dir(A)

['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__']
```

## 可见性和属性的装饰器

对象的方法通常是公开（public）的，因为公开的方法是对象能够接收的消息，也是对象暴露给外界的调用接口。可以用`__name`表示一个私有属性，`_name`表示一个受保护属性。

```python
class Student:
    # 设置两个私有属性 name 和 age
    def __init__(self,name, age):
        self.__name = name
        self.__age = age

    def study(self, course_name):
        print(f'{self.name}正在学习{course_name}.')

    def play(self):
        print(f'{self.name}正在玩游戏.')
# 类实例化， __init__函数中定义了2个参数，这里要传入两个参数。
stu1 = Student("john", 18)

# 尝试调用私有属性 name。抛出异常。
stu1.study("python") # AttributeError: 'Student' object has no attribute '__name'

```

由此可见，以`__`开头的属性`__name`是私有的，在类的外面无法直接访问，但是在类里面的`study`方法中可以通过`self.__name`访问该属性。

那么有没有方法在类的外面也能访问私有属性呢？

可以，需要更换名字规则。`对象._类名__方法名`这种形式来读取私有属性。或者使用下面的装饰器来实现。

```python
stu1 = Student("john", 18)
print(stu1._Student__name, stu1._Student__age) # john 18 
```

### 装饰器

python中可以通过`property`装饰器为“私有”属性提供读取和修改的方法。装饰器通常会放在类、函数或方法的声明之前，通过一个`@`符号表示将装饰器应用于类、函数或方法。

```python
class Student:
    def __init__(self,name, age):
        self.__name = name
        self.__age = age
    # 私有属性读取（getter方法）- 获取__name属性
    @property
    def name(self):
        return self.__name
    # 私有属性修改器（setter方法）- 修改__name属性
    @name.setter
    def name(self, name):
        # 如果name 参数不为空就赋值给对象的__name属性
        # 否则将__name属性赋值为'无名氏',有两种写法
        # self.__name = name if name else '无名氏'
        self.__name = name or '无名氏'
    # 获取私有属性
    @property
    def age(self):
        return self.__age

    def study(self, course_name):
        print(f'{self.name}正在学习{course_name}.')

    def play(self):
        print(f'{self.name}正在玩游戏.')

stu1 = Student('smith', 20)
print(stu1.age, stu1.name) # 20 smith

# 修改私有属性name的值，设置为空会触发私有属性修改器@name.setter。
stu1.name = ''
print(stu1.name) # 无名氏
```

## 动态属性

在python中，我们可以动态为对象添加属性。python是动态类型语言。

对象的方法本质上也是对象的属性，如果给对象发送一个无法接收的信息，引发的异常仍然是`AttributeError`。

```python
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
stu = Student('john', 18)
# 想要在不修改当前类的代码的情况下，去添加一个sex属性
stu.sex = "女"

print(stu.name, stu.age, stu.sex) # john 18 女
```

如果不希望再使用对象时动态的为对象添加属性，可以使用python的`__slots__`魔法方法。对于`Student`类来说，可以在类中指定`__slots__ = ('name', 'age')`，这样`Student`类的对象只能有name 和 age 属性。如果想动态添加其他属性会引发异常。

注意添加位置，要在`__init__`方法之前添加。

```python
class Student:
    __slots__ = ("name", "age")
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
stu = Student('john', 18)
# 想要在不修改当前类的代码的情况下，去添加一个属性

stu.sex = "女"
print(stu.name, stu.age, stu.sex) # AttributeError: 'Student' object has no attribute 'sex'
```

## 静态方法和类方法

在类中定义的方法都是对象方法，这些方法都是对象可以接收消息。

静态方法和类方法是发送给类对象的消息。

举例：

定义一个三角形类，通过传入三条边的长度来构造三角形，并提供计算周长和面积的方法。计算周长和面积肯定是三角形对象的方法，这一点毫无疑问。但是在创建三角形对象时，传入的三条边长未必能构造出三角形，为此我们可以先写一个方法来验证给定的三条边长是否可以构成三角形，这种方法很显然就不是对象方法，因为在调用这个方法时三角形对象还没有创建出来。我们可以把这类方法设计为静态方法或类方法，也就是说这类方法不是发送给三角形对象的消息，而是发送给三角形类的消息，代码如下所示。

```python
class Triangle(object):
    """三角形类"""

    def __init__(self, a, b, c):
        """初始化方法"""
        self.a = a
        self.b = b
        self.c = c

    @staticmethod
    def is_valid(a,b,c):
        """判断三条边长能否构成三角形（静态方法）"""
        return a + b > c and b + c > b

    # @classmethod
    # def is_valid(cls, a, b, c):
    #     """判断三条边长能否构成三角形（类方法）"""
    #     return  a + b > c and b + c > b

    def perimeter(self):
        """计算周长"""
        return self.a + self.a + self.c
    
    def area(self):
        """计算面积"""
        p = self.perimeter() / 2
        return (p * (p - self.a) * (p - self.b) * (p - self.c)) ** 0.5
# 正常调用，需要先进行类的实例化。
triangle = Triangle(10 ,20 ,30)
print(triangle.perimeter()) # 50

# 调用静态方法中定义的属性，不需要对当前类进行实例化，可以直接调用。类方法也一样。
print(Triangle.is_valid(10,20,30)) # False

```

### 小结

在调用静态方法时，不需要对当前类进行实例化，可以直接使用`类名.方法`。

静态方法和类方法的区别

1.静态方法传入的参数都是普通参数

2.类方法的参数第一位必须是cls

class简写，代表当前的方法是属于当前的类

静态方法是不和当前类进行绑定的，可以看成一个单独的函数。

## 多态

面向对象三大特性

1.**封装** 根据 **职责** 将**属性** 和 **方法** 封装到一个抽象的**类**中

定义类的准则

2.**继承 实现代码的重用**，相同的代码不需要重复的编写

设计类的技巧

子类针对自己特有的需求，编写特有的代码

3.**多态** 不同的**子类对象** 调用相同的**父类方法**，产生不同的执行结果

**多态** 可以 **增加代码的灵活度**

以 **继承** 和 **重写父类方法** 为前提

是调用方法的技巧，不会影响到内部设计.

```python
class A:
    def work(self):
        print('人类需要工作')

# 程序员
class B(A):
    def work(self):
        print('程序员在工作--代码')

# 设计师
class C(A):
    def work(self):
        print('设计师在工作--图纸')

b = B()
c = C()

c.work()
b.work()
```

### 多态案例演练

**需求**

1.在`Dog`类中封装方法`game`

普通狗只是简单的玩耍

2.定义`XiaoTianDog`继承自`Dog`，并且重写`game`方法

哮天犬需要在天上玩耍

3.定义`Person`类，并且封装一个 和狗玩 的方法

在方法内部，直接让狗对象 调用 `game`方法

**案例小结**

`Person`类中只需要让 狗对象 调用`game`方法，而不关心具体是什么狗

`game`方法是在`Dog`父类中定义的

在程序执行时，传入不同的 狗对象 实参，就会产生不同的执行效果

多态更容易编写出通用的代码，做出通用的编程，以适应需求的不断变化。

```python
class Dog:
    # 定义类属性
    def __init__(self, name):
        self.name = name
    
    # 方法
    def game(self):
        print('%s 在蹦蹦跳跳的玩耍...' % self.name)

class XiaoTianDog(Dog):

    # 当前的哮天犬和普通的狗不一样
    """
    子类继承父类的时候 具有父类的所有属性和方法
    """
    def game(self):
        print('%s 飞到天上去玩耍...' % self.name)

class Person:
    def __init__(self, name):
        self.name  = name

    def game_with_dog(self, dog):
        # 让狗玩耍
        dog.game()
        # 让狗与人一起玩耍
        print('%s 和 %s 快乐的玩耍...' % (self.name, dog.name))    

# 创建一个普通狗对象
# wangcai = Dog('旺财')

wangcai = XiaoTianDog('旺财')
# 创建一个人的对象
xiaoming = Person('小明')

xiaoming.game_with_dog(wangcai)

"""
旺财 在蹦蹦跳跳的玩耍...
小明 和 旺财 快乐的玩耍...

旺财 飞到天上去玩耍...
小明 和 旺财 快乐的玩耍...
"""

"""
多态的运行情况
    我们在调用子类中的同名方法时 输出的值不一样

    继承和重写的
"""
```






