# python回炉重造-字典


## 字典的定义

`dictionary`（字典）是 除列表意外`python`之中 最灵活的数据类型

字典同样可以用来 存储多个数据

通常用于存储 描述一个 `物体`的相关信息

和列表的区别

列表是 有序 的对象集合

字典是 无序的对象集合

字典使用 `键值对`存储数据，键值对之间使用`,`分割

键 `key`是索引

值 `value` 是数据

键 和 值 之间使用`:`分割

键必须是唯一的

值 可以取任何数据类型，但 键 只能使用 字符串、数字或元组

```python
xiaoming  = {
    "name": "小明".
    "age": 18,
    "gender" : True
    "height": 1.75
}
```

​													len(字典) 获取字典的 键值对数量

|  key   | value |
| :----: | :---: |
|  name  | 小明  |
|  age   |  18   |
| gender | true  |
| height | 1.75  |



字典.keys() 所有 key 列表

字典.values() 所有value 列表

字典.items() 所有（key, value）元组列表

字典[key] 可以从字典中取值， key 不存在会报错

字典.get(key) 可以从字典中取值， key 不存在不会报错



字典[key] = value 

如果 key 存在 ，修改数据

如果 key 不存在 ，新建键值对



字典.setdefault(key, value)

如果 key 存在 ，不会修改数据

如果 key 不存在 ，新建键值对



字典.update(字典2) 将字典2 的数据合并到字典1



del 字典[key] 删除指定键值对， key 不存在会报错

字典.pop(key) 删除指定键值对， key 不存在会报错

字典.popitem() 随机删除一个键值对

字典.clear() 清空字典



## 字典常用操作

在 `ipython3`中定义一个 字典 ，例如 ：`xiaoing = {}`

输入 `xiaoming.` 按下`Tab`键，`ipython`会提示字典 能够使用的函数如下：

```python
xiaoming.clear()	xiaoming.copy()	xiaoming.get()	xiaoming.items()	xiaoming.keys()	xiaoming.pop()
xiaoming.popitem()	xiaoming.setdefault()	xiaoming.update()	xiaoming.values()
```

有关 字典的 常用操作 可以参照上图练习

### 清空字典

```python
info = {"name": "john", "age":18}

print("清空前:%s" %info)

info.clear()
print("清空后:%s" %info)
```

结果

```python
清空前:{'name': 'john', 'age': 18}
清空后:{}
```

## 遍历循环

遍历就是 依次 从字典中获取所有键值对

```python
#  for 循环内部使用`key的变量` in 字典 

for k,v in xiaoming.items():
    
    print("key=%s,value=%s"%(k,v))
```

提示：在实际开发中，由于字典中每一个键值对保存的数据类型是不同的，所以针对字典的循环需求并不是很多。

## 应用场景

尽量可以使用`for in`遍历 字典

但是在开发中，更多的应用场景是：

使用 多个键值对，存储 描述一个`物体`的相关信息 --------描述更复杂的数据信息

将 多个字典 放在一个列表中，再进行遍历， 在循环体内部针对每一个字典进行 相同的处理

```python
card_list = [
    {
        "name": "张三",
        "qq": "123465",
        "phone": "110",
    },
    {
        "name": "李四",
        "qq": "123456",
        "phone":"120"
    }
]
```
























