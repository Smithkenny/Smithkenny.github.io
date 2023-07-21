# python回炉重造-集合


## 集合

特性：

1.无序性： 一个集合中，每个元素的地位都是相同的，元素之间是无序的

2.互异性： 一个集合中，任何两个元素都是不同的，即元素在集合中只能出现一次

3.确定性： 给定一个集合和一个任意元素，给袁思要么属于这个集合，要么不属于这个集合，二者必居其一，不允许有模棱两可的情况出现。

集合的成员运算在性能上要优于列表的成员 运算，这是集合的底层存储特性（哈希存储）决定的。

### 创建集合

用`set()`创建，或者用 `{}`定义一个空集合，但`{}`中至少要有一个元素，没有元素就不是空集合而是空字典了。也可以将其他序列转换成集合，例如：`set('hello')`会得到一个包含4个字符的集合（重复的`l`会被去掉）。还可以用生成式语法来创建集合，和用生成式创建列表一样。要知道集合中有多少个元素，还是使用内置函数`len`。使用`for`循环可以实现对集合元素的遍历。

```python
# 创建集合（重复元素不会出现在集合中，因此常用集合这一特性去重！！）
set1 = {1, 2, 3, 3, 3, 2}
print(set1) # {1, 2, 3}
print(len(set1)) # 3
# 创建集合构造器语法
set2 = set('hello')
print(set2) # {'o', 'l', 'e', 'h'}

# 将列表转换成集合（可以去掉列表中重复的元素）
set3 = set([1,2,3,3,2,1])
print(set3) # {1, 2, 3}
# 创建集合的生成式语法（将列表生成式的[] 换成{}）
set4 = {num for num in range(1,20) if num % 3 == 0 or num % 5 == 0}
print(set4) # {3, 5, 6, 9, 10, 12, 15, 18}
# 集合元素的循环遍历
for elem in set4:
    print(elem)
```

注意：集合中的元素必须是`hashable`类型。`hashable`类型指的是能够计算出哈希码的数据类型。可以暂时将哈希码理解为和变量对应的唯一的ID值。**通常不可变类型都是`hashable`类型。**如整数、浮点、字符串、元组等。而可变类型都不是`hashable`类型，因为可变类型无法确定唯一的ID值，所以也就不能放到集合中。集合本身也是可变类型，所以集合不能作为集合中的元素。

### 集合的运算

python为集合类型提供了非常丰富的运算符，主要包括:成员运算、交集运算、并集运算、差集运算、比较运算（相等性、子集、超集）等。

#### 成员运算

通过成员运算`in`、`not in`检查元素是否在集合中

```python
set1 = {11,12,13,14,15}
print(10 in set1) # False
print(15 in set1) # True

set2 = {'python', 'Java', 'Go', 'swift'}

print('Ruby' in set2) # False
print('Java' in set2) # True
```

#### 交并差运算

python中的集合跟数学上的集合一样。可以进行交集、并集、差集等运算，而且可以通过运算符和方法调用方式来操作，代码如下。

```python
set1 = {1,2,3,4,5,6,7}
set2 = {2,4,6,8,10}

# 交集
# 方式一：使用 & 运算符
print(set1 & set2) # {2, 4, 6}
# 方法二： 使用intersection方法
print(set1.intersection(set2)) # {2, 4, 6}

# 并集
# 方法一： 使用 | 运算符
print(set1 | set2) # {1, 2, 3, 4, 5, 6, 7, 8, 10}

# 方法二： 使用union方法
print(set1.union(set2)) # {1, 2, 3, 4, 5, 6, 7, 8, 10}

# 差集
# 方法一： 使用 - 运算符
print(set1 - set2) # {1, 3, 5, 7}
# 方法二： 使用difference 方法
print(set1.difference(set2)) # {1, 3, 5, 7}

# 对称差
# 方法一： 使用 ^ 运算符
print(set1 ^ set2) # {1, 3, 5, 7, 8, 10}
# 方法二： 使用 方法
print(set1.symmetric_difference(set2)) # {1, 3, 5, 7, 8, 10}
# 方法三： 对称差相当于两个集合的并集减去交集
print((set1 - set2) - (set1 & set2)) # {1, 3, 5, 7}
```

总结：对于两个集合求交集，`&`运算符和`intersection`方法的作用是完全相同的，使用运算符的方式更直观而且代码也比较简短。

#### 比较运算

两个集合可以用`==`和`!=`进行相等性判断，如果两个集合中的元素完全相同，那么`==`比较的结果就是

`True`，否则就是`False`，如果集合`A`中的任意一个元素都是集合`B`的元素，那么集合`A`称为集合`B`的子集。

```python
set1 = {1,3,5}
set2 = {1,2,3,4,5}
set3 = set2
# < 运算符表示真子集， <= 表示子集
print(set1 < set2, set1 <= set2) # True True
print(set2 < set3, set2 <= set3) # False True
# 通过issubset 方法也能进行子集判断
print(set1.issubset(set2)) # True

# 反过来可以用issuperset 或>运算符进行超集判断
print(set2.issuperset(set1)) # True
print(set2 > set1) # True
```

#### 集合的方法

python中的集合是可变类型，我们可以通过集合类型的方法为集合添加或删除元素。

```python
# 创建一个空集合
set1 = set() 
# 通过add方法添加元素
set1.add(33)
set1.add(55)
set1.update({1,10,100,1000})
print(set1) # {33, 1, 100, 55, 1000, 10}

# 通过discard方法删除指定元素
set1.discard(100)
set1.discard(99) # 元素不存在不会抛出异常 和remove方法对比。
print(set1) # {33, 1, 55, 1000, 10}

# 通过remove方法删除指定元素，建议先做成员运算再删除
# 否则元素如果不在集合中就会引发KeyError异常
set1.remove(99)
print(set1) # KeyError: 99

# pop方法可以从集合中随机删除一个元素并返回该元素
print(set1.pop()) # 33

# clear方法可以清空整个集合
set1.clear()

print(set1) # set()
```

如果要判断两个集合有没有相同的元素可以使用`isdisjoint`方法，没有相同元素返回`True`,否则返回

`False`，代码如下。

```python
set1 = {'Java', 'Python', 'Go', 'Kotlin'}
set2 = {'Kotlin', 'Swift', 'Java', 'Objective-C', 'Dart'}
set3 = {'HTML', 'CSS', 'JavaScript'}
print(set1.isdisjoint(set2))  # False
print(set1.isdisjoint(set3))  # True
```

### 不可变集合

python中还有一种不可变类型的集合，名字叫`frozenset`。`set`和`frozenset`的区别就如同`list`跟`tuple`的区别。`frozenset`由于是不可变类型，能够计算出哈希码，因此它可以作为`set`中的元素。除了不能添加和删除元素，`frozenset`在其他方面跟`set`基本一样。

```python
set1 = frozenset({1,3,5,7})
set2 = frozenset(range(1,6))
print(set1 & set2) # frozenset({1, 3, 5})
print(set1 | set2) # frozenset({1, 2, 3, 4, 5, 7})
print(set1 - set2) # frozenset({7})
print(set1 < set2) # False
```

### 总结

python中集合底层使用了哈希存储的方式。集合是一种容器，元素必须是`hashable`类型，与列表不同的地方在于集合中元素没有序、不能用索引运算、不能重复。


