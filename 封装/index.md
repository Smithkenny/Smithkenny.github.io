# 封装




## 封装与反射

片面理解：封装相当于隐藏。

### 如何将属性隐藏（设置成私有）？

用双下划线开头方式将属性隐藏起来。

1.`__`开头的属性只是一种语法意义上的变形，并不会真的限制外部的访问。`_类名__属性名`可以访问但毫无意义。

2.这种变形只在类的定义阶段发送一次，类定义之后再新增的`__`开头的属性不会变形。

3.这种隐藏只对外不对内，因为类内部定义的属性在类定义阶段统一发生变形。

```python
class Foo:
    __N=0
    def __init__(self):
        self.__X=10
    def __foo(self):    #相当于_Foo__foo()
        print('from A')
    def bar(self):
        self.__foo()

f = Foo() #类实例化，f是对象。
f.bar() #结果 'from A'。内部调用了__foo()验证第3条。
f.__foo() #报错。AttributeError: 'Foo' object has no attribute '__foo'，属性被隐藏，外部不可调用。验证第一条外部不能访问隐藏属性。
# 可以用f._Foo__foo()调用，但毫无意义。验证第1条。

f.__N=10
print(f.__N) #验证第二条。类定义时已经定义了__N=0,而__N=10是在类定义之后。此时可以调用__N.结果为10
```

封装应用

```python
class People:
    def __init__(self,name,age):
        # self.__name=name
        # self.__age=age
        self.set_info(name,age)
    def tell_info(self):
        print("姓名：<%s> 年龄：<%s>"%(self.__name,self.__age))
    def set_info(self,name,age):
        if type(name) is not str: #判断name是否是str类型
            raise TypeError('name must be str')
        if type(age) is not int: #判断age是否是int类型
            raise TypeError('age must be int')
        self.__name=name
        self.__age=age
p=People(12312323,18) #TypeError: name must be str
p.tell_info()
```

核心思想：通过设置`set_info()`方法统一接口，把`name`、`age`隐藏。用户只需要调用`tell_info()`方法，不需要了解其中的过程。

### 封装之property

`property`是一种特殊属性，访问它时会执行一段功能（函数）然后返回值。

成人的`BMI数值`：

过轻：低于18.5

正常：18.5-23.9

过重：24-27

肥胖：28-32

非常肥胖, 高于32

　　`体质指数（BMI）=体重（kg）÷身高^2（m）`

　　`EX：70kg÷（1.75×1.75）=22.86`

```python
class People:
    def __init__(self,name,age,height,weight):
        self.name=name
        self.age=age
        self.height=height
        self.weight=weight

    @property
    def bmi(self):
        return self.weight / (self.height ** 2)
jack=People('jack',18,1.80,75)
# jack.height=1.82
print(jack.bmi)
```

`bmi`应该是不断变化的，是一种数据属性。

#### 为什么要用property

将一个类的函数定义成特性以后，对象再去使用的时候`ojb.name`，根本无法察觉自己的`name`是执行了一个而函数然后计算出来，这种特性的使用方式遵循了统一访问的原则。

```python
class People:
    def __init__(self,name):
        self.__name = name #将所有的数据属性都隐藏起来
    @property
    def name(self):
        return self.__name #obj.name访问的是self.__name（这也是真实值的存放位置）
jack=Pepole('jack') #类实例化
print(jack.name) #jack
```

此时名字不能修改,修改会报异常。

```python
jack.name='smith' #AttributeError: can't set attribute
```

怎样可以修改呢？

设置`同名的属性.setter()`方法

```python
class People:
    def __init__(self,name):
        self.__name = name 
    @property
    def name(self):
        return self.__name
    
    @name.setter
    def name(self,obj): 
        if type(obj) is not str: #在设定值之前进行类型检查
            raise TypeError("name must be str")
        self.__name=obj
        
jack=Pepole('jack')
jack.name='smith'
print(jack.name) #smith 
```

如何删除属性呢？

添加`同名属性.deleter`

```python
class People:
    def __init__(self,name):
        self.__name = name 
    @property
    def name(self):
        return self.__name
    
    @name.setter
    def name(self,obj): 
        if type(obj) is not str: #在设定值之前进行类型检查
            raise TypeError("name must be str")
        self.__name=obj
        
    @name.deleter
    def name(self,obj):
        del self.__name
jack=Pepole('jack')
jack.name='smith'
del jack.name 
print(jack.name) #AttributeError: 'People' object has no attribute '_People__name' 成功删除了。 
```

## 反射

### 内置函数补充

`isinstance`判断类型

`issubclass`:判断是不是子类

```python
l=[]
print(type(l) is list)
print(isinstance(l,list)) #True

class Foo:
    pass
class Bar(Foo):
    pass
print(issubclass(Bar,Foo)) #True
```

反射

通过字符串映射或修改程序运行时的状态、属性、方法有以下4个方法：

`hasattr(obj,name_str)`:判断一个对象里是否有对应的`name_str`字符串的方法

`setattr(obj,y,v)`：设置属性,相当于`obj.y=v`

`getattr(obj,name_str,default=None)`：根据字符串`name_str`去获取`obj对象`里的方法内存地址

`delattr(x,y)`：删除某种属性

```python
class Foo:
    def __init__(self,name):
        self.name=name
    def f1(self):
        print('from f1')
obj=Foo('jack')
# print(obj.name) #等同于obj.__dict__['name']

#hasattr
print(hasattr(obj,'name')) #True
print(hasattr(obj,'f1')) #True
#setattr
setattr(obj,'x',1)
print(obj.__dict__) #结果是：{'name': 'jack', 'x': 1}

#getattr
if hasattr(obj, 'f1'):
    f = getattr(obj, 'f1') # f=obj.f1
    # print(f) #<bound method Foo.f1 of <__main__.Foo object at 0x000002A8A3677DC0>>
    f() #from f1
#delattr
delattr(obj,'name')
print(obj.__dict__) #{'x': 1}
```

## 类的内置方法

`__str__`：改变对象字符串显示

不使用`__str__`显示

```python
class People:
    def __init__(self,name,age):
        self.name=name
        self.age=age
jack=People('jack',18)
print(jack) #<__main__.People object at 0x000001D7DEF31310>
```

使用之后可以自定义显示内容

```python
class People:
    def __init__(self,name,age):
        self.name=name
        self.age=age
    def __str__(self):
        return '<name: %s,age: %s>' %(self.name,self.age)

jack=People('jack',18)
print(jack) #<name: jack,age: 18>
```

`__del__`：析构方法，当对象在内存种被释放时，自动触发执行。

注：如果产生的对象仅仅只是python程序级别的（用户级），那么无需定义`__del__`,如果产生的对象的同时还会向操作系统发起系统调用，即一个对象有用户级与内核级两种资源，比如（打开一个文件，创建一个数据库链接），则必须在清除对象的同时回收系统资源，这就用到了`__del__`。

```python
class Foo:
    def __del__(self):
        print('执行我啦')
f1=Foo()
del f1
print("------>")
```

结果：

```python
执行我啦
------>
```

如果去掉`del f1`那么结果又不一样了。

```python
class Foo:
    def __del__(self):
        print('执行我啦')
f1=Foo()
print("------>")
```

结果

```python
------>
执行我啦
```


































