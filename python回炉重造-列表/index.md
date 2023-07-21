# python回炉重造-列表


## 列表常用操作

在`ipython3`中定义一个列表，例如：`name_list = []`

输入`name_list.`按下`Tab`键，`ipython`会提示能够使用的方法如下：

```python
name_list.append	name_list.count		name_list.insert 	name_list.reverse
name_list.clear		name_list.extend	name_list.pop		name_list.sort
name_list.copy		name_list.index		name_list.remove
```

| 序号 | 分类 | 关键字/函数/方法        | 说明                     |
| ---- | ---- | ----------------------- | ------------------------ |
| 1    | 增加 | 列表.insert(索引,数据)  | 在指定位置插入数据       |
|      |      | 列表.append(数据)       | 在末尾追加数据           |
|      |      | 列表.extend(列表2)      | 将列表2的数据追加到列表  |
| 2    | 修改 | 列表[索引] = 数据       | 修改指定索引的数据       |
| 3    | 删除 | del 列表[索引]          | 删除指定索引的数据       |
|      |      | 列表.remove[数据]       | 删除第一个出现的指定数据 |
|      |      | 列表.pop                | 删除末尾 数据            |
|      |      | 列表.pop(索引)          | 删除指定索引数据         |
|      |      | 列表.clear              | 清空列表                 |
| 4    | 统计 | len(列表)               | 列表长度                 |
|      |      | 列表.count(数据)        | 数据在列表中出现的次数   |
| 5    | 排序 | 列表.sort()             | 升序排序                 |
|      |      | 列表.sort(reverse=True) | 降序排序                 |
|      |      | 列表.reverse()          | 逆序、反转               |

del关键字

使用`del`关键字（`delete`）同样可以删除列表元素

`del`关键字本质上是用来将一个变量从内存中删除的

如果使用`del`关键字将变量从内存中删除，后续的代码就不能使用这个变量了

```python
del name_list[1]
```

在日常开发中，要从列表删除数据，建议用列表提供的方法。

关键字、函数和方法

关键字是`python`内置的、具有特殊意义的标识符

```python
import keyword
print(keyword.kwlist)
print(len(keyword.kwlist))
```

关键字后面不需要使用括号

函数 封装了独立功能，可以直接调用

```python
函数名(参数)
```

函数需要死记硬背

方法 和函数类似，同样是封装了独立的功能

方法 需要通过 对象来调用，表示针对这个对象要做的操作。

```python
对象.方法名（参数）
```

在变量后面输入`.`，然后选择针对这个变量要执行的操作，记忆起来比函数要简单的多。

## 循环遍历

遍历 就是 从头到尾 依次 从列表中获取数据

在 循环体内部 针对 每一个元素，执行相同操作

在 `python`中为了提高列表的遍历效率，专门提供的 `迭代iteration遍历`

使用 `for`就能实现迭代遍历

```python
# for 循环内部使用的变量 in 列表
for name in name_list:
    循环内部针对列表元素进行操作
    print(name)
    
```

## 浅拷贝和深拷贝

浅拷贝：利用列表的 copy() 方法或者切片来实现

深拷贝：利用 copy 模块的 deepcopy() 函数来实现

浅拷贝可以用于处理一维列表，对于嵌套列表的拷贝，只能拷贝第一层数据，其余仅拷贝其引用：

```python
>>> x = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
>>> y = x.copy()
>>> x[1][1] = 0
>>> x
[[1, 2, 3], [4, 0, 6], [7, 8, 9]]
>>> y
[[1, 2, 3], [4, 0, 6], [7, 8, 9]]
```

深拷贝可以用于处理多维列表：

```python
>>> import copy
>>> x = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
>>> y = copy.deepcopy(x)
>>> x[1][1] = 0
>>> x
[[1, 2, 3], [4, 0, 6], [7, 8, 9]]
>>> y
[[1, 2, 3], [4, 5, 6], [7, 8, 9]]
```

### 小结

**列表浅拷贝只是外层对象拷贝，将原对象拷贝的同时，不会拷贝对象中所引用的子对象**

```python
copy()
切片[:]
浅拷贝，如果包含嵌套对象，那么拷贝的只是其引用
```

**列表的深拷贝将原对象拷贝的同时，也将对象中所有引用的子对象一并进行拷贝。**

```python
copy.copy() # 浅拷贝
copy.deepcopy()
```

### 查找特定下标范围的元素

```python
mylist = ['a','c','b','a']

print(mylist.index('a',1,4)) # 查找特定下标范围的元素，并返回找到对应数据的下标。 范围区间，左闭右开。开区间取不到。
```

## 列表元素删除

```python
moviename = ['apple','banana','糖豆人','apple']

print("-------删除前，电影列表的数据-----")
for name in moviename:
    print(name)

# moviename.pop()
# del moviename[0]
moviename.remove('apple') # 多个重复元素只删除最近的一个

print("-------删除后，电影列表的数据-----")
for name in moviename:
    print(name)
```

### 列表元素反转与排序

```python
a = [1,3,4,5,612,29]

a.reverse()
print(a) # [29, 612, 5, 4, 3, 1]

a.sort() # 升序
print(a) # [1, 3, 4, 5, 29, 612]

a.sort(reverse=True) # [612, 29, 5, 4, 3, 1] 降序
print(a)
```

### 多个列表嵌套元素取法

```python
schoolName = [["北京大学","清华大学"],["南开大学","天津大学","天津师范大学"],["山东大学","北京航空航天大学"]]
print(schoolName[0][0]) # 北京大学
```

### 练习

需求：不同的人随机分配3个办公室，一个人只属于一个办公室，可以多人在一个办公室。

```python
import random
# 三个空办公室
offices = [[],[],[]]
# 6个待分配的人
names = ["A","B","C","D","E","F"]
# 给6个人随机分配到3个办公室
for name in names:
    index = random.randint(0,2) # 获得0-2范围内随机整数
    offices[index].append(name) 

# 展示办公室活动人员基本信息 
i = 1
for office in offices:
    print("办公室%s的人数为:%d"%(i,len(office))) # office的列表长度就是办公室中的人数
    i += 1
    for name in office: # 展示每个办公室中的人员信息
        print("%s"%name,end="\t")
    print("\n")
    print("_"*20)
```

程序执行结果

```python
办公室1的人数为:1
A

____________________     
办公室2的人数为:4        
B       C       E       F

____________________     
办公室3的人数为:1        
D

____________________
```

### 列表同时取出下标和元素小技巧

```python
mylist = [1,20,200,2000,3,90]

for i,v in enumerate(mylist):
    print(i,v)
```

输出结果

```python
0 1
1 20
2 200
3 2000
4 3
5 90
```

还可以从1开始计数

```python
mylist = [1,20,200,2000,3,90]

for i,v in enumerate(mylist):
    print(i+1,v)
```




