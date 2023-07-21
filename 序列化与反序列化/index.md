# 序列化与反序列化


## 什么是序列化？

我们把对象（变量）从内存中变成可存储或传输的过程称之为序列化。在`Python`中叫`pickling`，在其他语言中也被称之为`serialization`，`marshalling`，`flattening`等等，都是一个意思。

## 为什么要序列化？

持久保存状态

内存是无法永久保存数据的，当程序运行了一段时间，我们断电或者重启程序，内存中关于这个程序的之前一段时间的数据（有结构）都被清空了。在断电或重启程序之前将程序当前内存中所有的数据都保存下来（保存到文件中），以便于下次程序执行能够从文件中载入之前的数据，然后继续执行，这就是序列化。

跨平台数据交互

序列化之后，不仅可以把序列化后的内容写入磁盘，还可以通过网络传输到别的机器上，如果收发的双方约定好实用一种序列化的格式，那么便打破了平台/语言差异化带来的限制，实现了跨平台数据交互。
反过来，把变量内容从序列化的对象重新读到内存里称之为反序列化，即`unpickling`。

## 如何序列化之`json`和`pickle`

`json反序列化`

如果我们要在不同的编程语言之间传递对象，就必须把对象序列化为标准格式，比如`XML`，但更好的方法是序列化为`JSON`，因为`JSON`表示出来就是一个字符串，可以被所有语言读取，也可以方便地存储到磁盘或者通过网络传输。`JSON`不仅是标准格式，并且比`XML`更快，而且可以直接在`Web页面`中读取，非常方便。
`JSON`表示的对象就是标准的`JavaScript语言`的对象，`JSON和Python`内置的数据类型对应如下：

|  json类型  | python类型 |
| :--------: | :--------: |
|     {}     |    dic     |
|     []     |    list    |
|  "string"  |    str     |
|  1234.56   | int或float |
| true/false | True/False |
|    null    |    None    |

内存中结构化的数据->`格式json`->字符串->保存到文件中或基于网络传输

序列化

`python->json`

```
json.dumps()
json.dump()
```

反序列化

`json->python`

```
json.loads()
json.load()
```

### 练习

需求1

把`user = {'name': 'jack', 'age': 20, 'sex': 'male'}`序列化。保存为`user_new.json`

序列化

```python
#1.使用dumps
import json
user = {'name': 'jack', 'age': 20, 'sex': 'male'}
with open('user_new.json', 'w', encoding='utf-8') as f:
    f.write(json.dumps(user))
#结果
{"name": "jack", "age": 20, "sex": "male"}

#2.使用dump
import json
user = {'name': 'jack', 'age': 20, 'sex': 'male'}
json.dump(user, open('user_new.json', 'w', encoding='utf-8'))

使用dumps和使用dump效果一样。
```

需求2：
把刚刚序列化后的文件`user_new.json`反序列化，并查询key所对应的值。

```python
#使用loads反序列化
import json
with open('user_new.json', 'r', encoding='utf-8') as f:
     user=json.loads(f.read())
     print(user['name'])
#结果：jack

#使用load反序列化
import json
user=json.load(open('user_new.json', 'r', encoding='utf-8'))
print(user['name'])

#结果：jack
```

以上都是序列化和反序列化文件，自定义的字符串能不能进行序列化和反序列化？

```python
s = '{"Name": "jack", "Hobby": "play", "age": 20}'
print(json.loads(s))
#结果 {'Name': 'jack', 'Hobby': 'play', 'age': 20} 
```

注意

```
import json
dct="{'1':111}"#json 不认单引号
dct=str({"1":111})#报错,因为生成的数据还是单引号:{'one': 1}
```

```
dct='{"1":"111"}'
print(json.loads(dct))
```

总结

无论数据是怎样创建的，只要满足`json`格式，就可以`json.loads`出来,不一定非要`dumps`的数据才能`loads`

## pickle反序列化

`json`并不是所有类型的数据都能序列化。比如集合类型，这时候需要使用`pickle模块`了。
`pickle`会把所有类型转换成`bytes类型`。
`Pickle`的问题和所有其他编程语言特有的序列化问题一样，就是它只能用于`Python`，并且可能不同版本的`Python`彼此都不兼容，因此，只能用`Pickle`保存那些不重要的数据，不能成功地反序列化也没关系。

```python
s={1,2,3,4,5}
print(pickle.dumps(s))
#结果
b'\x80\x04\x95\x0f\x00\x00\x00\x00\x00\x00\x00\x8f\x94(K\x01K\x02K\x03K\x04K\x05\x90.'
```

将集合存入文件再序列化

```python
import pickle
s = {1, 2, 3, 4, 5}
#方式1
with open('s.plk', 'wb') as f:
     f.write(pickle.dumps(s))
#方式2
pickle.dump(s, open('s.plk', 'wb'))
```

反序列化文件

```python
import pickle
#方式1
with open('s.plk', 'rb') as f:
    s = pickle.loads(f.read())
    print(s)
#方式2
s = pickle.load(open('s.plk', 'rb'))
print(s)
```

## shelve模块

shelve模块比pickle模块简单，只有一个open函数，返回类似字典的对象，可读可写;key必须为字符串，而值可以是python所支持的数据类型
打开一个文件的同时会创建3个同名文件。`.bak .dat .dir`结尾。

```python
#创建
import shelve
f = shelve.open('db.sh1')
f['stu1']={'name':'jack','age':20}
f.close()

#查询
print(f['stu1']['name'])
```


