# 集合的相关特性


## 集合的相关特性

### 去重

```python
s = {1,2,2,2,2,2,3,3,3,4,4,4,5}
print(s) #{1,2,3,4,5}
```

确保不要将集合与字典混淆，字典也用花括号括起来。

```python
my_set = {1,2,3} 
my_dict = {"term": "definition", "term2": "definition2"}
```

### set()

**将其他数据类型转换成集合类型**

```python
l = [1,2,3,4]
s = set(l)
print(s) #{1,2,3,4}
```

### 并集(.union())

联合是一个新集合，包含所有原始集合中的元素。这可以被认为是两个或多个集合的总和。

```py
a = {1,2,3}
b = {3,4,5}
c = a.union(b)
print(c) #{1,2,3,4,5}
```

### 交集(.intersection())

交集标识多个集合之间的共同元素或重叠。

```python
a = {1,2,3} 
b = {3,4,5} 
c = a.intersection(b) 
print(c) #{3}
```

### 差集(.difference())

差异标识存在于基集中而不存在于另一个基集中的值。a集合中有而b集合中没有的部分。

```python
a = {1,2,3} 
b = {3,4,5} 
c = a.difference(b)
print(c) #{1,2}
```

### 对称差集(.symmetric_difference)

从并集中减去交集剩下的就是对称差集

```python
a = {1,2,3} 
b = {3,4,5} 
c = a.symmetric_difference(b)
print(c) #{1,2,4,5}
```

### 子集判断

`.issubset()`和`issuperset()`方法返回布尔值`True`或者`False`

子集是一个集合，其中所有元素都存在于更大的集合中

```python
a = {1,2,3,4,5} 
b = {3,4,5} 
c = a.issubset(b)
print(c) #a是b的子集吗？ 结果是 False
```

a、b集合调换位置

```python
a = {1,2,3,4,5} 
b = {3,4,5} 
c = b.issubset(a)
print(c) #b是a的子集吗？ 结果是 True
```

### 超集判断

超集与子集相反。如果提供的参数的所有元素都存在于其中，则基集被声明为超集。

```python
a = {1,2,3,4,5} 
b = {3,4,5} 
c = a.issuperset(b) 
print(c) # True
```






