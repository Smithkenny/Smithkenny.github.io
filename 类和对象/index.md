# 类和对象


## 面向过程与面向对象

### 面向过程

核心就是过程二字，过程即解决问题的步骤，就是先干什么再干什么
基于该思想写程序就好比在设计一条流水线，是一种机械式的思维方式
优点：复杂的过程流程化，进而简单化
缺点：扩展性差

### 面向对象

核心是对象二字，对象是特征与技能的结合体
基于该思想编写程序就好比在创造一个世界，世界是由一个个对象组成的，是一种“上帝式” 的思维方式
优点：可拓展性强
缺点：编程复杂高，容易出现过度设计

### 类

对象是特征与技能的结合体，类就是一系列对象相似的特征与技能的结合体。

在现实世界中：一定是现有的一个个具体存在的对象，后总结出的类。

在程序中：一定保证先定义类，后产生对象。

类体代码在类的定义阶段就会立刻执行。



在程序中特征用变量标识，技能用函数标识。

因而类中最常见的无非是：变量和函数的定义

`Student`是类名，`school`是特征，`def`定义的函数都是技能。

```python
class Student:
    school = 'taiyang'

    def learn(self):
        print('is learning')
    def sleep(self):
        print('is sleeping')
    def choose_course(self):
        print('choose course')
   print(Student.__dict__)
```

#### 查看

```python
print(Student.school) #数据属性是所有对象共享的
print(Student.learn)  #函数属性是绑定给对象用的
```

#### 新增

```python
Student.country = 'china'
print(Student.country)
```

#### 删除

```python
del Student.country
print(Student.country)
```

#### 注意

类中可以有任意python代码，这些代码在类定义阶段便会执行

因而会产生新的名称空间，用来存放类的变量名与函数名，可以通过`Student.__dict__`查看

对于经典类来说我们可以通过该字典操作类名称空间的名字（新式类有限制），但`python`为我们提供专门的`.`语法。

点是访问属性的语法，类中定义的名字，都是类的属性。

#### 程序中类的用法

`.`专门用来访问属性，本质操作的就是`__dict__`

`Student.school` 等于经典类的操作`Student.__dict__['school']`

`Student.school='taiyang'`等于经典类的操作`Student.__dict__['school']=‘taiyang’`

`Student.x=1`等于经典的类的操作`Student.__dict__['x']=1`

`del Student.x`等于经典的类的操作`Student.__dict__.pop('x')`

#### python中类内置的特殊属性

`类名.__name__`# 类的名字(字符串)
`类名.__doc__`# 类的文档字符串
`类名.__base__`# 类的第一个父类(在讲继承时会讲)
`类名.__bases__`# 类所有父类构成的元组(在讲继承时会讲)
`类名.__dict__`# 类的字典属性
`类名.__module__`# 类定义所在的模块
`类名.__class__`# 实例对应的类(仅新式类中)

## 如何绑定到对象独有特征？

调用类的过程又称为实例化

得到一个返回值，即对象，该对象是一个空对象。

示例：

改写：

```python
class Student:
    school = 'taiyang'
    def __init__(self, name, sex, age): #在调用类时会自动触发执行。
        self.Name = name
        self.Sex = sex
        self.Age = age
    def learn(self):
        print('is learning')
    def sleep(self):
        print('is sleeping')
    def choose_course(self):
        print('choose course')
#实例化三个对象
stu1 = Student('jack', '男', 18)
stu2 = Student('smith', '男', 28)
stu3 = Student('john', '女', 30)

#调用对象特有属性。
print(stu1.Name) #jack
print(stu2.Name) #smith
```

## 属性查找与绑定方法

```python
class Student:
    school = 'taiyang'

    def learn(self, x, y):
        print('%s is learning' %self.Name)
        print(x, y)
    def sleep(self):
        print('is sleeping')
    def choose_course(self):
        print('choose course')
    def __init__(self, name, sex, age):
        self.Name = name
        self.Sex = sex
        self.Age = age
#1.查找一个对象的属性顺序是：先找对象自己的__dict__，再找类的__dict__。
stu1 = Student('jack', '男', 18)
stu2 = Student('smith', '男', 28)
stu3 = Student('john', '女', 30)
print(stu1.__dict__) #{'Name': 'jack', 'Sex': '男', 'Age': 18}
print(stu1.Name, stu1.Age, stu1.Sex) #jack 18 男

#2.类的数据属性是所有对象共享，所有对象都指向同一个内存地址。
print(Student.school, id(Student.school)) #taiyang 2061253245552
print(stu1.school, id(stu1.school)) #taiyang 2061253245552
print(stu2.school, id(stu2.school)) #taiyang 2061253245552
print(stu3.school, id(stu3.school)) #taiyang 2061253245552

#3.类中定义的函数是绑定给对象使用的。
# 不同对象就是不同绑定方法。
# 绑定给谁，就应该由谁来调用,谁来调用就会把谁当作第一个参数传给对应的函数。
print(Student.learn)  #<function Student.learn at 0x0000026B2F04F9D0>
print(stu1.learn) #<bound method Student.learn of <__main__.Student object at 0x0000026B2F05EFD0>>
print(stu2.learn) #<bound method Student.learn of <__main__.Student object at 0x00000227AB11EF10>>
print(stu3.learn) #<bound method Student.learn of <__main__.Student object at 0x00000227AB11ED00>>

stu1.learn(1, 2) #等于Student.learn(stu1, 1, 2)
stu2.learn(3, 4)
stu3.learn(5, 6)
```

解析：

```
stu1.learn #stu1对象绑定公共函数属性learn
Student.learn #Student类访问公共函数属性learn
它们都可以加括号访问公共函数属性learn
```

python中一切皆为对象，且`python3`中类与类型是一个概念，类型就是类。

```python
#类型dict就是类dict
>>> list
<class 'list'>

#实例化的到3个对象l1,l2,l3
>>> l1=list()
>>> l2=list()
>>> l3=list()

#三个对象都有绑定方法append,是相同的功能,但内存地址不同
>>> l1.append
<built-in method append of list object at 0x10b482b48>
>>> l2.append
<built-in method append of list object at 0x10b482b88>
>>> l3.append
<built-in method append of list object at 0x10b482bc8>

#操作绑定方法l1.append(3),就是在往l1添加3,绝对不会将3添加到l2或l3
>>> l1.append(3)
>>> l1
[3]
>>> l2
[]
>>> l3
[]
#调用类list.append(l3,111)等同于l3.append(111)
>>> list.append(l3,111) #l3.append(111)
>>> l3
[111] 
```

## 对象之间的交互

小练习:

```python
class Garen: #定义英雄盖伦的类，不同的玩家可以用它实例出自己英雄;
    camp = 'demacia' #所有玩家的英雄(盖伦)的阵营都是Demacia;
    def __init__(self,nickname,life_value,aggresivity):
        self.nickname=nickname #为自己的盖伦起个别名;
        self.life_value=life_value #英雄都有自己的生命值;
        self.aggresivity=aggresivity #英雄都有自己的攻击力;
    def attack(self,enemy):
        enemy.life_value-=self.aggresivity #根据自己的攻击力，攻击敌人就减掉敌人的生命值。

class Riven:
    camp = 'Noxus' #所有玩家的英雄(锐雯)的阵营都是Noxus;
    def __init__(self,nickname,life_value,aggresivity):
        self.nickname=nickname #为自己的锐雯起个别名;
        self.life_value=life_value #英雄都有自己的生命值;
        self.aggresivity=aggresivity #英雄都有自己的攻击力;
    def attack(self,enemy): #普通攻击技能，enemy是敌人;
        enemy.life_value-=self.aggresivity #根据自己的攻击力，攻击敌人就减掉敌人的生命值。
    def fire(self,enemy):
        enemy.life_value-=100
#实例出俩英雄
g1=Garen('草丛猥琐男', 1000,100)
r1=Riven('猛男雯雯',200,500)
#交互
print(r1.life_value) #攻击之前锐雯血量 200
g1.attack(r1) #盖伦攻击锐雯
print(r1.life_value) #攻击之后锐雯剩余血量 100
```

## 总结

1.站的角度不同，定义出的类是截然不同的

2.现实中的类并不完全等于程序中的类，比如现实中的公司类，在程序中有时需要拆分成部门类，业务类。

3.有时为了编程需求，程序中也可能会定义现实中不存在的类，比如策略类，现实中并不存在，但是在程序中却是一个很常见的类。

## 从代码级别看面向对象

```python
#在没有学习类这个概念时，数据与功能是分离的
def exc1(host,port,db,charset):
    conn=connect(host,port,db,charset)
    conn.execute(sql)
    return xxx


def exc2(host,port,db,charset,proc_name)
    conn=connect(host,port,db,charset)
    conn.call_proc(sql)
    return xxx
#每次调用都需要重复传入一堆参数
exc1('127.0.0.1',3306,'db1','utf8','select * from tb1;')
exc2('127.0.0.1',3306,'db1','utf8','存储过程的名字')
```

```python
#我们能想到的解决方法是，把这些变量都定义成全局变量.
HOST=‘127.0.0.1’
PORT=3306
DB=‘db1’
CHARSET=‘utf8’

def exc1(host,port,db,charset):
    conn=connect(host,port,db,charset)
    conn.execute(sql)
    return xxx

def exc2(host,port,db,charset,proc_name)
    conn=connect(host,port,db,charset)
    conn.call_proc(sql)
    return xxx

exc1(HOST,PORT,DB,CHARSET,'select * from tb1;')
exc2(HOST,PORT,DB,CHARSET,'存储过程的名字')
```

```python
但是2的解决方法也是有问题的，按照2的思路，我们将会定义一大堆全局变量，这些全局变量并没有做任何区分，即能够被所有功能使用，然而事实上只有HOST，PORT，DB，CHARSET是给exc1和exc2这两个功能用的。言外之意：我们必须找出一种能够将数据与操作数据的方法组合到一起的解决方法，这就是我们说的类了.
class MySQLHandler:
    def __init__(self,host,port,db,charset='utf8'):
        self.host=host
        self.port=port
        self.db=db
        self.charset=charset
    def exc1(self,sql):
        conn=connect(self.host,self.port,self.db,self.charset)
        res=conn.execute(sql)
        return res

    def exc2(self,sql):
        conn=connect(self.host,self.port,self.db,self.charset)
        res=conn.call_proc(sql)
        return res

obj=MySQLHandler('127.0.0.1',3306,'db1')
obj.exc1('select * from tb1;')
obj.exc2('存储过程的名字')
```

```python
#改进
class MySQLHandler:
    def __init__(self,host,port,db,charset='utf8'):
        self.host=host
        self.port=port
        self.db=db
        self.charset=charset
        self.conn=connect(self.host,self.port,self.db,self.charset)
    def exc1(self,sql):
        return self.conn.execute(sql)

    def exc2(self,sql):
        return self.conn.call_proc(sql)

obj=MySQLHandler('127.0.0.1',3306,'db1')
obj.exc1('select * from tb1;')
obj.exc2('存储过程的名字')
```

## 继承与派生

### 什么是继承？

继承是一种创建新类的方式，新建的类可以继承一个或多个父类（python支持多继承），父类又可称作基类或超类，新建的类称为派生类或子类。

子类会“遗传”父类的属性，从而解决代码重用的问题。

python中类的继承分为：单继承和多继承

```python
class ParentClass1: #定义父类
    pass

class ParentClass2: #定义父类
    pass

class SubClass1(ParentClass1): #单继承，基类是ParentClass1，派生类是SubClass
    pass

class SubClass2(ParentClass1,ParentClass2): #python支持多继承，用逗号分隔开多个继承的类
    pass
```

### 查看继承

```python
SubClass1.__bases__ #__base__只查看从左到右继承的第一个子类，__bases__则是查看所有继承的父类
(<class '__main__.ParentClass1'>,)
SubClass2.__bases__
(<class '__main__.ParentClass1'>, <class '__main__.ParentClass2'>)
```

提示：如果没有指定基类，python的类会默认继承object类，object是所有python类的基类，他提供了一些常见的方法（如`__str__`）的实现。

```python
ParentClass1.__bases__
(<class 'object'>,)
ParentClass2.__bases__
(<class 'object'>,)
```

### 经典类与新式类

在`python2` 中类分为两种：

1.经典类:   指的是没有继承`object`类的类，以及该类的子类。

2.新式类：指的是继承`object`类的类，以及该类的子类。

在`python3`中类统一为新式类



继承描述的是子类和父类之间的关系，是一种`什么是什么的关系`。要找出这种关系，必须先抽象再继承。

抽象即抽取类似或者说比较像的部分。

**继承：是基于抽象的结果，通过编程语言去实现它，肯定是先经历抽象这个过程，才能通过继承的方式去表达出抽象的结构。**

抽象只是分析和设计的过程中，一个动作或者说一种技巧，通过抽象可以得到类。

在开发程序的过程中，如果我们定义了一个类A，然后又想新建立另外一个类B，但是类B的大部分内容与类A的相同时

我们不可能从头开始写一个类B，这就用到了类的继承的概念。

通过继承的方式新建类B，让B继承A，B会‘遗传’A的所有属性(数据属性和函数属性)，实现代码重用。

### 练习

定义两个类`Student`类和`Teacher`类，都有相同的数据属性（都在同一个学校），也有相同的函数属性。姓名、性别、年龄。

```python
class Student:
    school='qinghua'
    def __init__(self, name, sex, age):
        self.name=name
        self.sex=sex
        self.age=age
    def learn(self):
        print('%s is learing'%self.name)
class Teacher:
    school='qinghua'
    def __init__(self, name, sex, age):
        self.name=name
        self.sex=sex
        self.age=age
    def teach(self):
        print('%s is teaching'%self.name)
```

使用继承，把相同部分提取出来，单独做成父类。学生和老师类都作为子类继承父类的所有属性。

```python
class People:
    school='qinghua'
    def __init__(self, name, sex, age):
        self.name=name
        self.sex=sex
        self.age=age
class Student(People):
    def learn(self):
        print('%s is learing'%self.name) 
class Teacher(People):
    def teach(self):
        print('%s is teaching'%self.name)
#初始化
stu1=Student('jack','man',18)
teacher1=Teacher('john','woman',20)
#Student和Teacher作为子类继承了父类People类的所有属性。可以直接调用。
print(stu1.__dict__)
{'name': 'jack', 'sex': 'man', 'age': 18}
```

### 派生

当在父类中找不到所需的属性时，子类可以定义属于自己的特征。

```python
class People:
    school='qinghua'
    def __init__(self, name, sex, age):
        self.name=name
        self.sex=sex
        self.age=age
    def tell_info(self):
        print('<名字:%s 性别:%s 年龄:%s> '%(self.name, self.sex, self.age))
class Student(People):
    def learn(self):
        print('%s is learing'%self.name)
    def tell_info(self):
        print("我是学生:", end='')
        print('<名字:%s 性别:%s 年龄:%s> '%(self.name,self.sex,self.age))
class Teacher(People):
    def teach(self):
        print('%s is teaching'%self.name)
    def tell_info(self):
        print("我是老师:", end='')
        print('<名字:%s 性别:%s 年龄:%s> '%(self.name,self.sex,self.age))
#初始化
stu1=Student('jack','man',18)
teacher1=Teacher('john','woman',20)
#调用tell_info()函数属性
stu1.tell_info()
teacher1.tell_info()
我是学生:<名字:jack 性别:man 年龄:18> 
我是老师:<名字:john 性别:woman 年龄:20> 
```

`tell_info`函数属性查找顺序

```
stu1对象自己有没有-->stu1所在的类（Student）有没有-->发现有直接调用。
											如果没有-->查找父类（People）有没有（有直接调用，没有直接抛出异常）
		异常：AttributeError: 'Student' object has no attribute 'tell_info'
```

## 子类重用父类方法

在子类派生出的新方法中，往往需要重用父类的方法，我们有两种实现方式：

1.“指名道姓”，即`父类名.父类方法()`跟继承没关系。和函数调用类似。

`Student类`中除了继承父类的`name sex age `方法之外，还派生出自己的新方法`course stu_id`

```python
class People:
    school = 'qinghua'
    def __init__(self, name, sex, age):
        self.name = name
        self.sex = sex
        self.age = age
    def tell_info(self):
        print('<名字:%s 性别:%s 年龄:%s> '%(self.name, self.sex, self.age))
class Student(People):
    def __init__(self, name, sex, age, course, stu_id):
        People.__init__(self, name, sex, age)
        self.course = course
        self.stu_id = stu_id
    def learn(self):
        print("%s is learning."%(self.name))
    def tell_info(self):
        print("我是学生: ", end='')
        People.tell_info(self)

stu1 = Student('jack', 'man', 18,'python',1)
stu1.tell_info()
#结果
我是学生: <名字:jack 性别:man 年龄:18> 
```

2.使用`super()`，依赖于继承。依循`mro列表`从当前类的下一个类开始查找。

在`python2`中`super(自己的类名, self)`，而在`python3`中`super()`函数中可以不传参。

```python
class People:
    school = 'qinghua'
    def __init__(self, name, sex, age):
        self.name = name
        self.sex = sex
        self.age = age
    def tell_info(self):
        print('<名字:%s 性别:%s 年龄:%s> '%(self.name, self.sex, self.age))
class Student(People):
    def __init__(self, name, sex, age, course, stu_id):
        super(Student, self).__init__(name, sex, age)
        self.course = course
        self.stu_id = stu_id
    def learn(self):
        print("%s is learning."%(self.name))
    def tell_info(self):
        print("我是学生: ", end='')
        super(Student, self).tell_info()

stu1 = Student('jack', 'man', 18,'python',1)
stu1.tell_info()
#结果
我是学生: <名字:jack 性别:man 年龄:18> 
```

示例。问结果是什么？

```python
class Foo:
    def f2(self):
        print("======>f2")
    def f1(self):
        print('Foo f1')
        super().f2()
class Bar:
    def f2(self):
        print('Bar f2')
class Sub(Foo, Bar):
    pass

s=Sub()
s.f1()
```

解析：

先查看子类`Sub()`的`mro方法`

```python
print(Sub.mro()) #[<class '__main__.Sub'>, <class '__main__.Foo'>, <class '__main__.Bar'>, <class 'object'>]
```

寻找`f1()`时的顺序

```
对象s->对象所在类Sub->类Sub左侧父类Foo->找到f1(),并打印'Foo f1'。遇到super()，super()遵循mro列表继续查找f2().当前在Foo类。->找到Foo下一个类Bar中有f2方法，打印'Bar f2'->查找结束
#最终结果
Foo f1
Bar f2
即使super()所在类中有同名函数属性f2()也不会调用，只会调用当前所在类的下一个类中的方法。
即使没有直接继承关系，`super`仍然会按照`mro`继续往后查找。这就是为什么super所在的类有同名的f2函数也不会调用的原因。
```

## 组合与重用

组合指的是，在一个类中以另外一个类的对象作为数据属性，称为类的组合。

### 继承与组合的区别

继承：`什么是什么`

组合：`什么有什么`

#### 组合示例1

```python
class People:
    school = 'qinghua'
    def __init__(self, name, sex, age):
        self.name = name
        self.sex = sex
        self.age = age
    def tell_info(self):
        print('<名字:%s 性别:%s 年龄:%s> '%(self.name, self.sex, self.age))
class Student(People):
    def __init__(self, name, sex, age, course, stu_id):
        People.__init__(self, name, sex, age)
        self.course = course
        self.stu_id = stu_id
    def learn(self):
        print("%s is learning."%(self.name))
    def tell_info(self):
        print("我是学生: ", end='')
        People.tell_info(self)
class Teacher(People):
    def __init__(self, name, sex, age, level, salary):
        People.__init__(self, name, sex, age)
        self.level = level
        self.salary = salary
    def teach(self):
        print("%s is teaching."%(self.name))
    def tell_info(self):
        print('我是老师：', end='')
        People.tell_info(self)
class Date:
    def __init__(self, year, mon, day):
        self.year = year
        self.mon = mon
        self.day = day
    def tell_info(self):
        print('出生日期是:<%s-%s-%s>'%(self.year, self.mon, self.day))

stu1 = Student('jack', 'man', 18,'python',1)
date_obj1 = Date(1995, 10, 2)
stu1.birth = date_obj1
teacher1 = Teacher('smith', 'women', 20, 10, 4000)
date_obj2 = Date(1980, 1, 1)
teacher1.birth = date_obj2

stu1.birth.tell_info()
teacher1.birth.tell_info()
```

解析：

```
Teacher类和Stuent类都有共同的属性生日，所以单独创建Date类。
#初始化Date类
date_obj1 = Date(1995, 10, 2)
#学生1的生日是date_obj1
stu1.birth = date_obj1
#调用学生1的tell_info方法
stu1.birth.tell_info()
```

#### 组合示例2

```python
class People:
    def __init__(self,name,age,sex):
        self.name=name
        self.age=age
        self.sex=sex

class Course:
    def __init__(self,name,period,price):
        self.name=name
        self.period=period
        self.price=price
    def tell_info(self):
        print('<%s %s %s>' %(self.name,self.period,self.price))

class Teacher(People):
    def __init__(self,name,age,sex,job_title):
        People.__init__(self,name,age,sex)
        self.job_title=job_title
        self.course=[]
        self.students=[]

class Student(People):
    def __init__(self,name,age,sex):
        People.__init__(self,name,age,sex)
        self.course=[]

eg=Teacher('eg',18,'male','霸道金牌讲师')
s1=Student('牛二',18,'female')

python=Course('python','3mons',3000.0)
linux=Course('linux','3mons',3000.0)

#为老师eg和学生s1添加课程
eg.course.append(python)
eg.course.append(linux)
s1.course.append(python)

#为老师eg添加学生s1
eg.students.append(s1)

#查看老师的课程
for obj in eg.course:
    obj.tell_info()
#结果
<python 3mons 3000.0>
<linux 3mons 3000.0>
#查看学生的课程
for i in s1.course:
    i.tell_info()
#结果
<python 3mons 3000.0>
```

解析:

学生选课之前课程为空，课程有`Python`和`Linux`可以选择，学生选择`Python`。为学生分配导师，最后查看学生所选的课程。

## 抽象类

### 什么是抽象类？

抽象类是一个特殊类，只能被继承不能实例化。

### 为什么要有抽象类？

如果说类是从一堆对象中抽取相同的内容而来的，那么抽象类就是从一类中抽取相同的内容而来的，内容包括数据属性和函数属性。

从实现角度来看，抽象类与普通类的不同之处在于：抽象类中只能有抽象方法（没有实现功能），该类不能被实例化，只能被继承，且子类必须实现抽象方法。这一点与接口有点类似，但其实是不同的。

### 在`python`中实现抽象类

```python
import abc
class Animal(metaclass=abc.ABCMeta):
    @abc.abstractmethod 
    def eat(self):
        pass
    @abc.abstractmethod
    def run(self):
        pass
class People(Animal):
    pass

class pig(Animal):
    pass

peol=People() #TypeError: Can't instantiate abstract class People with abstract methods eat, run
```

解析：

引用`abc`模块实现`java`中接口功能。抽象化（`Animal`）类下面的子类必须使用父类（`Animal`）定义好的功能，名称也不能改。

**要在抽象类中定义抽象方法，我们必须使用装饰器：`@abstractmethod`。内置`abc`模块包含这两者。**

**作用是强制子类来使用定义好的功能。**

如果子类不使用抽象类中定义的功能，将不能进行实例化。异常如上段代码所示。

定义好功能后就能实例化正常调用了。

```python
import abc
class Animal(metaclass=abc.ABCMeta):
    @abc.abstractmethod
    def eat(self):
        pass
    @abc.abstractmethod
    def run(self):
        pass
    
class People(Animal):
    def eat(self):
        print("He is eatting")
    def run(self):
        print("He is running")

class pig(Animal):
    pass

peol=People() #实例化
peol.eat() #调用eat()。He is eatting
peol.run() #调用run()。He is running
```

`Linux`中一切皆文件。用抽象类定义一套模板。

```python
import abc
class File(metaclass=abc.ABCMeta):
    @abc.abstractmethod
    def read(self):
        pass
    def write(self):
        pass
class Disk(File):
    def read(self):
        print("disk read")
    def write(self):
        print("disk write")

class Process(File):
    def read(self):
        print("process read")
    def write(self):
        print("process write")

d=Disk
p=Process

d.read()
d.write()
```

## 继承的原理

属性的查找有两种方法：深度优先和广度优先。

![image-20220406220157089](/postimages/image-20220406220157089.webp)

![image-20220406220221407](/postimages/image-20220406220221407.webp)

**`python2`中默认是经典类（深度优先），而`python3`中默认是新式类（广度优先）。**

```python
class A(object):
    def test(self):
        print('from A')

class B(A):
    def test(self):
        print('from B')

class C(A):
    def test(self):
        print('from C')

class D(B):
    def test(self):
        print('from D')

class E(C):
    def test(self):
        print('from E')

class F(D,E):
    # def test(self):
    #     print('from F')
    pass
f1=F()
f1.test()
print(F.__mro__) #只有新式才有这个属性可以查看线性列表，经典类没有这个属性
#(<class '__main__.F'>, <class '__main__.D'>, <class '__main__.B'>, <class '__main__.E'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>)


#新式类继承顺序:F->D->B->E->C->A
#经典类继承顺序:F->D->B->A->E->C
#python3中统一都是新式类
#pyhon2中才分新式类与经典类
```

为了实现继承,python会在`MRO列表`上从左到右开始查找基类,直到找到第一个匹配这个属性的类为止。
而这个`MRO列表`的构造是通过一个`C3线性化算法`来实现的。我们不去深究这个算法的数学原理,它实际上就是合并所有父类的`MRO列表`并遵循如下三条准则:
1.子类会先于父类被检查
2.多个父类会根据它们在列表中的顺序被检查
3.如果对下一个类存在两个合法的选择,选择第一个父类。（查找顺序都是从左到右）

## 多态与多态性

### 什么是多态？

同一种事物的不同形态。如：水。有水蒸气、冰、还有液态水三种形态。

```
peo=People()
dog=Dog()
pig=Pig()

#peo、dog、pig都是动物,只要是动物肯定有talk方法
#于是我们可以不用考虑它们三者的具体是什么类型,而直接使用
peo.talk()
dog.talk()
pig.talk()

#更进一步,我们可以定义一个统一的接口来使用
def func(obj):
    obj.talk()
```

### 多态的好处

1.增加了程序的灵活性

　　以不变应万变，不论对象千变万化，使用者都是同一种形式去调用，如`func(animal)`

2.增加了程序额可扩展性

　　通过继承`animal类`创建了一个新的类，使用者无需更改自己的代码，还是用`func(animal)`去调用。

```
>>> class Cat(Animal): #属于动物的另外一种形态：猫
...     def talk(self):
...         print('say miao')
... 
>>> def func(animal): #对于使用者来说，自己的代码根本无需改动
...     animal.talk()
... 
>>> cat1=Cat() #实例出一只猫
>>> func(cat1) #甚至连调用方式也无需改变，就能调用猫的talk功能
say miao

'''
这样我们新增了一个形态Cat，由Cat类产生的实例cat1，使用者可以在完全不需要修改自己代码的情况下。使用和人、狗、猪一样的方式调用cat1的talk方法，即func(cat1)
'''
```

### 鸭子类型

Python崇尚鸭子类型，即‘如果看起来像、叫声像而且走起路来像鸭子，那么它就是鸭子’

python程序员通常根据这种行为来编写程序。例如，如果想编写现有对象的自定义版本，可以继承该对象

也可以创建一个外观和行为像，但与它无任何关系的全新对象，后者通常用于保存程序组件的松耦合度。

例1：利用标准库中定义的各种‘与文件类似’的对象，尽管这些对象的工作方式像文件，但他们没有继承内置文件对象的方法.

```python
#二者都像鸭子,二者看起来都像文件,因而就可以当文件一样去用
class TxtFile:
    def read(self):
        pass

    def write(self):
        pass

class DiskFile:
    def read(self):
        pass
    def write(self):
        pass
```

例2：其实大家一直在享受着多态性带来的好处，比如Python的序列类型有多种形态：字符串，列表，元组，多态性体现如下

```
#str,list,tuple都是序列类型
s=str('hello')
l=list([1,2,3])
t=tuple((4,5,6))

#我们可以在不考虑三者类型的前提下使用s,l,t
s.__len__()
l.__len__()
t.__len__()

len(s)
len(l)
len(t)
```


