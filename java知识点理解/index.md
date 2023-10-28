# Java知识点理解


## 如何区分对象是否有返回值？

### 1.有返回值

需要指定返回值类型，方法执行完毕后用return 将结果返回给调用者。

```java
# 类型double
public double area(){
	return height * weight
}
```

### 2.无返回值

用void 定义。不需要返回结果。
特点：定义的方法中没有return 语句或者return 语句后没有表达式。

```java
# 类型：void
public void info(String name){
	System.out.println(name);
}
```

在决定是否定义一个具有返回值的方法时，可以考虑以下几个因素：

- [x] 没有返回值的方法只能执行一个固定的功能。

- [x] 有返回值的方法还可以将有用的数据作为返回值供其他地方使用。

- [x] 如何选用：如果只是完成一个功能，就直接用没有返回值的方法，否则用有返回值的方法。

- 需要返回结果：如果你需要从方法中获取某种计算结果或数据，那么就需要定义一个具有返回值的方法。返回值可以是基本类型（如int、double等）或引用类型（如对象、数组等）。


- 方法执行的目的：如果方法的主要目的是执行一些操作或修改对象的状态，而不需要返回结果，那么可以定义一个无返回值的方法（返回类型为void）。

## 方法重载的理解

Java允许在一个类中定义**多个名称相同的方法**，但是参数的类型或个数必须不同，这就是方法的重载。

特点：

- [x] 方法名一定相同
- [x] 参数类型或者参数个数一定不同
- [x] 一定在同一个类中

举例：

参数个数不同：如`method(int x)`与`method(int x,int y)`不同。

参数类型不同：如`method(int x)`与`method(double x)`不同。

参数顺序不同：如`method(int x,double y)`与`method(double x,int y)`不同。

**重载只与方法名与参数类型相关与返回值无关**

如`void method(int x)`与`int method(int y)`不是方法重载，不能同时存在。

**重载与具体的变量标识符无关**

如`method(int x)`与`method(int y)`不是方法重载，不能同时存在。

## 面向对象完成具体功能的操作的三步流程（非常重要）

- 创建类，并设计类的内部成员（属性、方法）
- 创建类的对象。比如：`Phone p1 = new Phone();`
- 通过对象，调用其内部声明的属性或方法，完成相关的功能。

## 类的成员之一： 属性

### 变量的分类：

- 角度一：按照数据类型来分：基本数据类型（8种）、引用数据类型（数组、类、接口、枚举、注解、记录）
- 角度二：按照变量在类中声明的位置不同：成员变量（或属性）、局部变量（方法内、方法形参、构造器内、构造器外）

### 属性的几个称谓： 成员变量、属性、field（字段、域）

### 区分成员变量 vs 局部变量

#### 相同点：

变量声明格式相同： 数据类型 变量名 = 变量值
变量都有其有效的作用域。出了作用域，就失效了。
变量必须先声明，后赋值，再使用。

#### 不同点

##### 类中声明位置不同：

属性：声明类内，方法外的变量
局部变量： 声明方法、构造器内部的变量

##### 在内存中分配的位置不同（难）

属性：随着对象的创建，存储在堆空间中
局部变量：存储在栈空间中

##### 生命周期：

属性：随着对象的创建而创建，随着对象的消亡而消亡。
局部变量：随着方法对应的栈帧入栈，局部变量会在栈中分配；随着方法对应的栈帧出栈，局部变量消亡。

##### 作用域:

属性： 在整个类的内部都是有效的
局部变量：仅限于声明此变量所在的方法（或构造器、代码块）中

##### 是否有权限修饰符进行修饰：

都有哪些权限修饰符：public 、protected、缺省、private（用于表明所修饰的结构可调用的范围的大小）
属性：是可以使用权限修饰符的。而局部变量，不能进行任何权限修饰符进行修饰。

例子1：

```java
public class MyMethod {
    // 属性（成员变量）
    String name;
//   public String name; 可以使用权限修饰符修饰
    int age;

    public void eat(){
        System.out.println("正在吃饭...");
    }
    public String play(String name){
        int age1 = 20; // 局部变量（只可方法内使用）
//        public int age1; // 错误。局部变量不能任何权限修饰符修饰。
        return name;
    }
}
```

##### 是否有默认值（重点）：

属性： 都有默认初始化值
意味着，如果没有给属性进行显式初始化赋值，则会有默认初始化值。

局部变量：都没有默认初始化值
意味着，在使用局部变量之前，必须要显式赋值，否则报错。
注意：对于方法的形参而言，在调用方法时，给形参赋值即可。

**例子1中方法、属性的调用**

```java
public class MymethodTest {
    public static void main(String[] args) {
        MyMethod myMethod = new MyMethod();
        System.out.println(myMethod.age);
        System.out.println(myMethod.play("篮球"));
    }
}
# 结果
    0
    篮球
```

## 类成员之二： 方法（method）

### 使用方法的好处

方法的理解： 方法 是类或对象行为特征的抽象，用来完成某个功能操作。

方法的好处：实现代码重用，减少冗余，简化代码。

### 使用举例

- Math.random()的 random方法
- Math.sqrt(x)的sqrt(x)方法 
- System.out.println(x)的println(x)方法 
- new Scanner(System.in).nextInt()的nextInt()方法 
- Arrays类中的binarySearch()方法、sort()方法、equals()方法

### 声明举例

- public void eat()
- public void sleep(int hour)
- public String interests(String hobby)
- public int getAge()

### 方法声明的格式（重要）

```sh
权限修饰符 [其他修饰符]  返回值类型 方法名(形参列表) [throws 异常类型] { // 方法头

	// 方法体

}
```

#### 权限修饰符

`Java`中规定了那些权限修饰符呢？ 有四种：`private`、缺省、`protected`、`public `

返回值类型：描述当调用完此方法时，是否需要返回一个结果。

##### 分类：

- 无返回值类型：使用void表示即可。比如`System.out.println(x)` 的`println(x)` 方法、`Arrays`的`sort()`方法。
- 有具体的返回值类型：需要指明返回的数据的类型。可以是基本数据类型，也可以引用数据类型。
- 需要在方法内部配合使用“return + 返回值类型的变量或常量”
- 比如：`Math.random() 、new Scanner(System.in).netxInt()`等

**【经验】我们在声明方法时，要不要提供返回值类型呢？**

- 根据方法具体实现的功能来决定。换句话说，具体问题具体分析
- 根据题目要求

#### 方法名：

属于标识符。需要满足标识符的规定和规范。“见名知意”。

#### 形参列表：

形参，属于局部变量，且可以声明多个。

##### 格式：

形参类型1 形参1,形参类型2 形参2,....）

##### 分类：

##### 无形参列表、有形参列表

- 无形参列表：不能省略一对()。比如：`Math.random()、new Scanner(System.in).nextIn();`
- 有形参列表：根据方法调用时，需要的不确定的变量的类型和个数，确定形参的类型和个数。

比如：`Array`类中的`binarySearch()`方法、`sort()`方法、`equals()`方法。

**【经验】我们在声明方法时，要不要形参列表呢？**

- 根据方法具体实现的功能来决定。换句话说，具体问题具体分析
- 根据题目要求

#### 方法体：

当我们调用一个方法时。真正执行的代码。体现了此方法的功能。

**注意点：**

- `Java`里的方法不能独立存在，所有的方法必须定义在类里。
- `Java`中的方法不调用，不执行。每调用一次，执行一次。
- 方法内可以调用本类中的（其他）方法或属性。
- 方法内不能定义方法

#### 关键字：

`return`

##### return的作用

- 结束一个方法
- 结束一个方法的同时，可以返回数据给方法的调用者（方法声明中如果有返回值类型，则方法内需要搭配return使用）

**使用注意点：**

`return`后面不能声明执行语句。

#### 代码理解：

```java
public class MethodTest {
    public static void main(String[] args) {
        Person p1 = new Person();
        p1.eat();
        p1.info();
    }
}
class Person{
    // 属性
    String name;
    int age;
    public void eat(){

        System.out.println("人吃饭");
        sleep(8);
        System.out.println("name= " + name);
    }
    public void sleep(int hours){
        System.out.println("人每天至少需要睡" + hours + "小时。");
    }
    public String interests(String hobby){
        String info = "我的爱好是" + hobby;
        System.out.println(info);
        return info;
    }
    public int getAge(){
        return age;
    }

    public void info(){
        System.out.println("Person info()");
//        info(); // 自己调用自己发生栈溢出。StackOverflowError。
        // 方法内不能定义方法
//        public void show(){
//
//        }
    }
    public void printNumber(int targetNUmber){
        for(int i = 1;i<=targetNUmber;i++){
            if( i == 4){
                return; //用于结束方法。
                // return 后面不能声明执行语句
//                System.out.println("666");
            }
            System.out.println(i);
        }
    }
}
```






















