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

## 方法重载的理解（overload）

### 定义：

在同一个类中，允许存在一个以上的同名方法，只要他们的参数列表不同即可。

满足这样特征的多个方法，彼此之间构成方法的重载。

### 总结为：

“两同一不同”

**两同：**同一个类、相同的方法名

**一不同：**参数列表不同。①参数个数不同 ②参数类型不同

**注意：方法的重载与形参的名、权限修饰符、返回值类型都没有关系**

### 如何判断两个方法是相同的呢？

**方法名相同，且形参列表相同。（形参列表相同指的是参数个数和类型相同，与形参名无关。）**

要求：在一个类中，允许存在多个相同名字的方法，只要他们的形参列表不同即可。

**编译器是如何确定调用的某个具体的方法呢？**

先通过方法名确定了一波重载的方法，进而通过不同的形参列表，确定具体的方法。

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

### 代码举例

```java
public class ArgsTest {
    public static void main(String[] args) {
        ArgsTest test = new ArgsTest();
        test.print();
        test.print(1);
        test.print(1,2);
    }
    public void print(int ...nums){
        System.out.println("1111");
        for (int i = 0; i < nums.length; i++) {
            System.out.println(nums[i]);
        }
    }
    public void print(int i){
        System.out.println("2222");
    }
    public void print(int i,int j){
        System.out.println("3333");
    }
}
```

三个`print()`方法互为重载。

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



## 对象数组

### 何为对象数组？如何理解？

数组的元素可以是基本数据类型，也可以是引用数据类型。当元素是引用数据类型时，我们称为对象数组。

举例：

`String[]、Person[]、Student[]、Customer[]等`

### 案例：

```sh
定义类Student，包含三个属性：学号number(int)，年级state(int)，成绩score(int)。
创建20个学生对象，
学号为1到20，年级和成绩都由随机数确定。
问题一：打印出3年级(state值为3）的学生信息。
问题二：使用冒泡排序按学生成绩排序，并遍历所有学生信息
提示：
1) 生成随机数：Math.random()，返回值类型double;
2) 四舍五入取整：Math.round(double d)，返回值类型long。
```

创建类：`Student.java`

```java
package Project1.MyMethod;

/**
 * ClassName: Student
 * Package: Project1.MyMethod
 * Description:
 *
 * @Author LHP
 * @Create 2023/10/30 21:41
 * @Version 1.0
 */
public class Student {
    int number;
    int state;
    int score;
    public String show(){
        return "number = " + number + ",state = " + state + ",score = " + score;
    }
}
```

将需求封装成方法方便调用`StudentTools.java`

```java
package Project1.MyMethod;

/**
 * ClassName: StudentTools
 * Package: Project1.MyMethod
 * Description:
 *
 * @Author LHP
 * @Create 2023/10/30 21:42
 * @Version 1.0
 */
public class StudentTools {
    /**
     * 打印出指定年级的学生信息
     */
    public void printStudentWithState(Student[] students,int state){
        for (int i = 0; i < students.length; i++) {
            if(state == students[i].state){
                Student stu = students[i];
                System.out.println(stu.show());
            }
        }
    }
    /**
     * 遍历指定学生数组
     */
    public void printStudents(Student[] students){
        for (int i = 0; i < students.length; i++) {
            System.out.println(students[i].show());
        }
    }
    /**
     * 针对学生数组，按照score属性从低到高排列（冒泡排序）
     * for循环: 外层i控制遍历次数，内层j控制相邻元素的比较和交换。
     * 比较相邻两个成绩的大小，如果前一个成绩大于后一个成绩，触发交换机制。
     * 交换的是内存地址。
     */
    public void sortStudents(Student[] students){
        for (int i = 0; i < students.length -1; i++) {
            for (int j = 0; j < students.length -1 -i; j++) {
                if(students[j].score > students[j + 1].score){
                    Student temp = students[j];
                    students[j] = students[j + 1];
                    students[j + 1] = temp;
                }
            }

        }
    }
}
```

创建测试类，方便测试功能。`StudentTest.java`

```java
package Project1.MyMethod;

/**
 * ClassName: StudentTest
 * Package: Project1.MyMethod
 * Description:
 *
 * @Author LHP
 * @Create 2023/10/30 21:41
 * @Version 1.0
 */
public class StudentTest {
    public static void main(String[] args) {
        // 创建student[]
        Student[] students = new Student[20];
        // 使用循环给数组元素赋值。
        for (int i = 0; i < students.length ; i++) {
            students[i] = new Student();
            // 给每一个学生对象的number、state、score属性赋值。
            students[i].number = i + 1;
            students[i].state = (int)(Math.random() * 6 + 1);
            students[i].score = (int)(Math.random() * 101);
        }
        // 需求1： 打印出3年级（state 为 3） 的学生信息
            StudentTools studentTools = new StudentTools();
            studentTools.printStudentWithState(students,3);
        System.out.println("******************************************");

        // 需求2：使用冒泡排序按学生成绩排序，并遍历所有学生信息。
        // 排序前遍历
        studentTools.printStudents(students);
        System.out.println("**************************************");
        System.out.println("开始排序...");
        studentTools.sortStudents(students);
        System.out.println("******************");
        // 排序后遍历
        studentTools.printStudents(students);
    }
}
```

## 可变个数形参的方法（jdk5.0）

### 使用场景

在调用方法时，可能会出现方法形参的类型是确定的，但是参数的个数不确定。此时我们就可以使用可变个数形参的方法。

### 格式

```sh
(参数类型 ...参数名)
```

### 说明

- 可变个数形参的方法在调用时，针对于可变的参数赋的实参个数可以为：0个、1个或多个。
- 可变个数形参的方法与同一个类中，同名的多个方法之间可以构成重载。
- 特例：可变个数形参的方法与同一个类中方法名相同，且与可变个数形参的类型相同的**数组参数**不构成重载。
- 可变个数的形参必须声明在形参列表的最后。
- 可变个数的形参最多再一个方法的形参列表中出现一次。

### 举例

```java
public class ArgsTest {
    public static void main(String[] args) {
        ArgsTest test = new ArgsTest();
        test.print();
        test.print(1);
        test.print(1,2);
    }
    public void print(int ...nums){
        System.out.println("1111");
        for (int i = 0; i < nums.length; i++) {
            System.out.println(nums[i]);
        }
    }
    public void print(int i){
        System.out.println("2222");
    }
    public void print(int i,int j){
        System.out.println("3333");
    }
}
```

## 方法的值传递机制

对于方法内声明的局部变量来说，如果出现赋值操作

- 如果是基本数据类型的变量，则将此变量保存的数据值传递出去。
- 如果是引用数据类型的变量，则将此变量保存的地址值传递出去。

形参：在定义方法时，方法名后面括号（）中声明的变量成为形式参数，简称形参。

实参：在调用方法时，方法名后面括号（）中使用的值/变量/表达式成为实际参数，简称实参。

### 规则：

实参给形参赋值的过程

- 如果形参是基本数据类型的变量，则将实参保存的数据值赋给形参。
- 如果形参是引用数据类型的变量，则将实参保存的地址值赋给形参。

Java中的参数传递机制是什么？值传递（不是引用传递）。

### 举例：

```java
public class ValueTransferTest {
    public static void main(String[] args) {
        // 1.基本数据类型的局部变量
        int m = 10;
        int n = m;
        System.out.println("m = " + m + " n = " + n); // m =10 n =10
        m++;
        System.out.println("m = " + m + " n = " + n); // m = 11 n = 10
        // 2.引用数据类型的局部变量
        // 2.1 数组类型
        int[] arr1 = new int[]{1,2,3,4,5};
        int[] arr2 = arr1;
        arr2[0] = 10;
        System.out.println(arr1[0]); //10
        // 2.2 对象类型
        Order order1 = new Order();
        order1.orderId = 1001;
        Order order2 = order1;
        order2.orderId = 1002;
        System.out.println(order1.orderId);//1002
    }
}
class Order{
    int orderId;
}
```

### 交换举例：

```java
public class ValueTransferTest3 {
    public static void main(String[] args) {
        ValueTransferTest3 test = new ValueTransferTest3();
        Data data = new Data();
        data.m = 10;
        data.n = 20;
        System.out.println("交换前：" + "m = " + data.m + ",n = " + data.n);
        test.swap(data);
        System.out.println("交换后：" + "m = " + data.m + ",n = " + data.n);
        }
    public void swap(Data data) {
        int temp = data.m;
        data.m = data.n;
        data.n = temp;
    }
    }
    class Data{
    int m;
    int n;
    }
```






































