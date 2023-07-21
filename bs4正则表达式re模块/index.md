# Bs4-正则表达式-Re模块



**`BeautifulSoup4`将复杂的`HTML`文档转换成一个复杂的树形结构，每个节点都是Python对象，所有对象可以归纳为4种**

```python
Tag
NavigableString
BeautifulSoup
Comment
```

### 全局代码

```python
from bs4 import BeautifulSoup

file = open(r"D:\code2022\auto-work\douban\test\baidu.html","rb")
html = file.read().decode('utf-8')
bs = BeautifulSoup(html,"html.parser")

print(bs.title) # <title>百度一下，你就知道</title>
print(bs.a)
print(bs.head)
print(type(bs.head)) # <class 'bs4.element.Tag'>
```

### Tag 标签及其内容

拿到它所找到的第一个内容

`NavigableString` 标签里的内容（字符串）

```python
print(bs.title.string) # 百度一下，你就知道
print(type(bs.title.string)) # <class 'bs4.element.NavigableString'>
```

`BeautifulSoup` 表示整个文档

```python
print(bs.a.attrs)
print(type(bs)) # <class 'bs4.BeautifulSoup'>
```

`Comment` 是一个特殊的`NavigableString`，输出的内容不包含注释符号。

```python
print(bs.name) # [document]
print(bs)
print(bs.a.string)
print(type(bs.a.string))
```

文档的遍历,遍历文档树。

```python
print(bs.head.contents)
print(bs.head.contents[1])
# 更多内容，搜索文档。
```

### 文档的搜索

#### `find_all()`

```python
# 字符串过滤：会查找与字符串完全匹配的内容
t_list = bs.find_all("a")

import re
# 正则表达式搜索：使用search() 方法来匹配内容

t_list = bs.find_all(re.compile("a"))

# 方法： 传入一个函数（方法），根据函数的要求来搜索(了解)
def name_is_exits(tag):
    return tag.has_attr("name")

t_list = bs.find_all(name_is_exits)
for item in t_list:
    print(item)

print(t_list)
```

#### `kwargs 参数`

```python
t_list = bs.find_all(id="head")

t_list = bs.find_all(href="http://news.baidu.com")
t_list = bs.find_all(class_=True)

for item in t_list:
    print(item)
```

#### `text参数`

```python
t_list = bs.find_all(text= "hao123") 
t_list = bs.find_all(text=["hao123","地图","贴吧"]) 
t_list = bs.find_all(text=re.compile("\d"))  # 应用正则表达式来查找包含特定文本的内容（标签里的字符串）
```

#### `limit 参数`

```python
t_list = bs.find_all("a",limit=3)
for item in t_list:
    print(item)
```

#### `css 选择器`

```python
t_list = bs.select('title') # 通过标签来查找
for item in t_list:
    print(item)
    
t_list = bs.select(".mnav") # 通过类名查找
for item in t_list:
    print(item)
    
t_list = bs.select("#u1")   # 通过id查找
for item in t_list:
    print(item)
    
t_list = bs.select("a[class='guide-info']") # 通过属性来查找
for item in t_list:
    print(item)
    
t_list = bs.select("head > title") # 通过子标签来查找
for item in t_list:
    print(item)

t_list = bs.select(".mnav ~ .bri") # 查找mnav下的bri标签
print(t_list[0].get_text())
```

## 正则表达式与Re库

### 正则表达式常用操作符（1）

| 操作符 | 说明                             | 实例                                         |
| ------ | -------------------------------- | -------------------------------------------- |
| .      | 表示任何单个字符                 |                                              |
| [  ]   | 字符集，对单个字符给出取值范围   | [`abc`]表示`a、b、c`,[`a-z`]表示a到z单个字符 |
| [^  ]  | 非字符集，对单个字符给出排除范围 | [`^abc`]表示非a或b或c的单个字符              |
| *      | 前一个字符0次或无限次扩展        | `abc*` 表示 `ab`、`abc`、`abcc`、`abccc`等   |
| +      | 前一个字符1次或无限次扩展        | `abc+` 表示 `abc`、`abcc`、`abccc`等         |
| ？     | 前一个字符0次或1次扩展           | `abc?` 表示 `ab`、`abc`                      |
| \|     | 左右表达式任意一个               | abc \| def 表示 abc、 def                    |

### 正则表达式常用操作符（2）

| 操作符  | 说明                             | 实例                                 |
| ------- | -------------------------------- | ------------------------------------ |
| `{m}`   | 扩展前一个字符`m`次              | `ab{2}c`表示`abbc`                   |
| `{m,n}` | 扩展前一个字符`m至n`次（含`n`）  | `ab{1,2}c`表示`abc`、`abbc`          |
| `^`     | 匹配字符串开头                   | `^abc` 表示`abc`且在一个字符串的开头 |
| `$`     | 匹配字符串结尾                   | `abc$`表示`abc`且在一个字符串末尾    |
| `()`    | 分组标记，内部只能使用 \| 操作符 | (abc)表示abc，(abc\|def)表示abc、def |
| `\d`    | 数字，等价于`[0-9]`              |                                      |
| `\w`    | 单词字符，等价于`[A-Za-z0-9_]`   |                                      |

### Re库主要功能函数

| 函数            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| `re.search()`   | 在一个字符串中搜索匹配正则表达式的第一个位置，返回`match`对象 |
| `re.match()`    | 从一个字符串的开始位置起匹配正则表达式，返回`match`对象      |
| `re.findall()`  | 搜索字符串，以列表类型返回全部能匹配的子串                   |
| `re.split()`    | 将一个字符串按照正则表达式匹配结果进行分割，返回列表类型     |
| `re.finditer()` | 搜索字符串，返回一个匹配结果的迭代类型，每个迭代元素是match对象 |
| `re.sub()`      | 在一个字符串中替换所有匹配正则表达式的字串，返回替换后的字符串。 |

正则表达式可以包含一些可选标志修饰符来控制匹配的模式。修饰符被指定为一个可选的标志。多个标志可以通过按位`OR(|)`它们来指定。如`re.l|re.M`被设置成`|`和`M`标志。

| 修饰符 | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| `re.l` | 使匹配对大小写不敏感                                         |
| `re.L` | 做本地化识别（local-aware）                                  |
| `re.M` | 多行匹配，影响`^`和`$`                                       |
| `re.S` | 使`.`匹配包括换行在内的所有字符                              |
| `re.U` | 根据`Unicode`字符集解析字符。这个标志影响`\w` ，`\W`， `\b`， `\B` |
| `re.X` | 该标志通过给与你更灵活的格式以便你将正则表达式写的更易于理解。 |

**正则表达式：字符串模式（判断字符串是否符合一定的标准）**

```python
import re
# 创建模式对象

pat = re.compile("AA") # 此处的AA 是正则表达式，用来去验证其他字符串

m = pat.search("CBA") # search 字符串被校验的内容

m = pat.search("ABCAA")
print(m) # <re.Match object; span=(3, 5), match='AA'>

m = pat.search("ABCAADDCCAAA") # search 方法进行比对查找
print(m) # <re.Match object; span=(3, 5), match='AA'>

# 没有模式对象

m = re.search("asd","Aasd") # 前面的字符串是规则（模板），后面的字符串是被校验的对象。
print(m) # <re.Match object; span=(1, 4), match='asd'>

print(re.findall("a","ASDaDFGAa"))    # ['a', 'a']  前面字符串是规则（正则表达式），后面字符串是被校验的字符串

print(re.findall("[A-Z]","ASDaDFGAa"))  # ['A', 'S', 'D', 'D', 'F', 'G', 'A']

print(re.findall("[A-Z]+","ASDaDFGAa")) # ['ASD', 'DFGA']

# sub 
print(re.sub("a","A","abcdcasd"))       # 找到a用A替换，在第三个字符串种查找

# 建议用正则表达式中，被比较的字符串前面加上r，不用担心转义字符的问题。

a = r"\aabd-\'"
print(a) # \aabd-\'
```


