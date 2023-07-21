# python回炉重造-字符串


## 字符串

### 定义

字符串 就是一串字符，是编程语言中表示文本的数据类型

在 `python`中可以使用 一堆双引号 `""`或者 一对单引号 `''`定义一个字符串

虽然可以使用`\"`或者`\'`做字符串的转义，但是在实际开发中：

如果字符串内部需要使用`""`,可以使用`''`定义字符串。

如果字符串内部需要使用`''`,可以使用`""`定义字符串。

可以使用 索引 获取一个字符串中 指定位置的字符，索引计数从 0 开始

亦可以使用 `for`循环遍历 字符串中每一个字符

大多数编程语言都是用`""`来定义字符串

```python
string = 'Hello Python'
for c in string:
    print(c)
```

字符串的索引值是从0 开始的

|  p   |  y   |  t   |  h   |  o   |  n   |
| :--: | :--: | :--: | :--: | :--: | :--: |
|  ↑   |      |      |      |      |      |

len(字符串) 	获取字符串的长度

字符串.count(字符串) 	小字符串在大字符串中出现的次数

字符串[索引] 	从字符串中取出单个字符。

它也支持切片操作

字符串[1:4:]	# yth

字符串.index(字符串)	获得小字符串第一次出现的索引



## 字符串的常规操作

在 `ipython3`中定义一个 字符串 ，例如 ： `hello_str = ""`

输入`hello_str`,按下`Tab`键，`ipython`会提示 字符串能够使用的方法如下：

```python
hello_str.capitalize	hello_str.isidentifier	hello_str.rindex	hello_str.casefold	hello_str.islower
hello_str.rjust			hello_str.center		hello_str.isnumeric	hello_str.rpartition	hello_str.count
hello_str.isprintable	hello_str.rsplit		hello_str.encode	hello_str.isspace		hello_str.rstrip
hello_str.endswith		hello_str.istitle		hello_str.split		hello_str.expandtabs   hello_str.isupper
hello_str.splitlines	hello_str.find			hello_str.join		hello_str.startswith    hello_str.format
hello_str.ljust			hello_str.strip			hello str.format_map	hello_str.lower	  hello_str.swapcase
hello_str.index			hello_str.lstrip		hello_str.title		hello_str.isalnum	 hello_str.maketrans
hello_str.translate		hello_str.isalpha		hello_str.partition		hello_str.upper	 hello_str.isdecimal
hello_str.replace		hello_str.zfill			hello_str.isdigit	hello_str.rfind
```

提示：正是因为python 内置提供的方法足够多，才使得在开发时，能够针对字符串进行更加灵活的操作。

#### 1）判断类型-9

| 方法               | 说明                                                         |
| ------------------ | ------------------------------------------------------------ |
| string.isspace()   | 如果 string 中只包含空格，则返回 True                        |
| string.isalnum()   | 如果 string 至少有一个字符并且所有字符都是字母或数字则返回True |
| string.isalpha()   | 如果 string 至少有一个字符并且所有字符都是字母则返回True     |
| string.isdecimal() | 如果 string 只包含数字则返回True ,全角数字                   |
| string.isnumeric() | 如果 string 只包含数字则返回True ,全角数字，汉字数字         |
| string.isdigit()   | 如果 string 只包含数字则返回True ,全角数字、{1}、`\u00b2`    |
| string.istitle()   | 如果 string是标题化的（每个单词首字母大写）则返回True 。     |
| string.islower()   | 如果 string 中包含至少一个区分大小写的字符，并且所有这些（区分大小写的）字符都是小写，则返回True 。 |
| string.upper()     | 如果 string 中包含至少一个区分大小写的字符，并且所有这些（区分大小写的）字符都是大写，则返回True 。 |

2）查找和替换-7

| 方法                                               | 说明                                                         |
| -------------------------------------------------- | ------------------------------------------------------------ |
| string.startswith(str)                             | 检查字符串是否以str开头，是则返回True。                      |
| string.find(str, beg=0, end=len(string))           | 检测 str 是否包含在 string 中，如果 beg 和 end 指定范围，则检查是否包含在指定范围内，如果是返回开始的索引值，否则返回-1 |
| string.index(str, beg=0, end=len(string))          | 跟find()方法一样，只不过如果str不在 string中会报一个异常.    |
| string.rfind(str, beg=0,end=len(string) )          | 类似于 find() 函数，返回字符串**最后一次出现**的位置，如果没有匹配项则返回 -1。 |
| string.rindex( str, beg=0,end=len(string))         | 类似于 index()，不过是返回**最后一个匹配**到的子字符串的索引号。 |
| string.replace(str1, str2, num=string.count(str1)) | 把 string 中的 str1 替换成 str2,如果 num 指定，则替换不超过 num 次. |



## 字符串的方法

在python中，我们可以通过字符串类型自带的方法对字符串进行操作和处理，对于一个字符串的变量，我们可以用`变量名.方法名()`的方式来调用它的方法。所谓方法其实就是跟某个类型的变量绑定的函数，后面我们讲面向对象编程的时候还会对这一概念详加说明。

### 大小写相关操作

下面的代码演示了和字符串大小写变换相关的方法。

```python
s1 = 'hello, world!'

# 使用capitalize方法获得字符串首字母大写后的字符串
print(s1.capitalize()) # Hello, world!
# 使用title方法获得字符串每个单词首字母大写后的字符串
print(s1.title()) # Hello, World!
# 使用upper方法获得字符串大写后的字符串
print(s1.upper()) # HELLO, WORLD!

s2 = 'GOODBYE'
# 使用lower方法获得字符串小写后的字符串
print(s2.lower()) # goodbye
```

### 查找操作

如果想在一个字符串中从前向后查找有没有另外一个字符串，可以使用字符串的`find`或`index`方法。

```python
s = 'hello, world!'

# find 方法从字符串中查找另一个字符串所在的位置
# 找到了返回字符串中另一个字符串首字母字符串的索引

print(s.find('or')) # 8
# 找不到返回 -1 
print(s.find('shit')) # -1
# index方法与find方法类似
# 找到了返回字符串中另一个字符串首字符的索引
print(s.index('or')) # 8
# 找不到引发异常
print(s.index('shit')) # ValueError: substring not found
```

在使用`find`和`index`方法时还可以通过方法的参数来指定查找的范围，也就是查找不必从索引0的位置开始。`find`和`index`方法还有逆向查找（从后向前查找）的版本，分别是`rfind`和`rindex` ，代码如下所示。

```python
s = 'hello good world!'

# 从前向后查找字符o出现的位置（相当于第一次出现）
print(s.find('o')) # 4
# 从索引为5 的位置开始查找o出现的位置
print(s.find('o', 5)) # 7
# 从后向前查找字符o出现的位置（相当于最后一次出现）
print(s.rfind('o')) # 12
```

### 性质判断

可以通过字符串的`startswith`、`endswith`俩判断字符串是否以某个字符串开头和结尾；还可以用`is`开头的方法判断字符串的特征，这些方法都返回布尔值，代码如下图所示。

```python
s1 = 'hello, world!'

# startwith 方法检查字符串是否以指定的字符串开头返回布尔值
print(s1.startswith('He')) # False
print(s1.startswith('hel')) # True

# endwith 方法检查字符串是否以指定的字符串结尾返回布尔值
print(s1.endswith('!')) # True

s2 = 'abc123456'

# isdigit 方法检查字符串是否由数字构成返回布尔值
print(s2.isdigit()) # False
# isalpha 方法检查字符串是否由字母构成返回布尔值
print(s2.isalpha()) # False
# isalnum 方法检查字符串是否以数字和字母构成返回布尔值
print(s2.isalnum()) # True
```

## 格式化字符串

在python中，字符串类型可以通过`center`、`ljust`、`rjust`方法做居中、左对齐和右对齐处理。如果要在字符串的左侧补零，也可以使用`zfill`方法。

```python
s = 'hello, world!'

# center 方法以宽度20 将字符串居中并在两侧填充*
print(s.center(20, '*')) # ***hello, world!****

print(s.rjust(20)) #        hello, world!

print(s.ljust(20, '-')) # hello, world!-------

print('33'.zfill(5)) # 00033

print('-33'.zfill(5)) # -0033
```

在用`print`函数输出字符串时，可以用下面的方式对字符串进行格式化。

```python
a = 321
b = 123
print('%d * %d = %d'% (a, b, a * b))
```

当然，我们也可以用字符串的方法来完成字符串的格式，代码如下所示。

```python
a = 321
b = 123
print('{0} * {1} = {2}'.format(a, b, a * b))
```

从python3.6开始，格式化字符串还有更为简介的书写方式，就是在字符串前加`f`来格式化字符串，在这种以`f`打头的字符串中`{变量名}`是一个占位符，会被变量对应的值将其替换掉，代码如下所示。

```python
a = 321
b = 123
print(f'{a} * {b} = {a * b}')
```

**f 表达式只支持python3.6 以上版本。。**

如果需要进一步控制格式化中变量值的形式，可以参照下面的表格来进行字符串格式化操作。

| 变量值      | 占位符                  | 格式化结果    | 说明                   |
| ----------- | ----------------------- | ------------- | ---------------------- |
| `3.1415926` | `{:.2f}`                | `3.14`        | 保留小数点后两位       |
| `3.1415926` | `{:+.2f}`               | `+3.14`       | 带符号保留小数点后两位 |
| `-1`        | `{:+.2f}`               | `-1.00`       | 带符号保留小数点后两位 |
| `3.1415926` | `{:.0f}`                | `3`           | 不带小数               |
| `123`       | `{:0>10d}`              | `0000000123`  | 左边补0，补够10位      |
| `123`       | `{:x<10d}`              | `123xxxxxxx`  | 右面补x，补够10位      |
| `123`       | `{:>10d}`               | `      123`   | 左补空格，补够10位     |
| `123`       | `{:<10d}`               | `123       `  | 右补空格，补够10位     |
| `123456789` | `{:,}`                  | `123,456,789` | 逗号分隔格式           |
| `0.123`     | `{:.2%}`                | `12.30%`      | 百分比格式             |
| `123456789` | `{:.2e}`                | `1.23e+08`    | 科学计数法格式         |
| 11          | `'{:0>10d}'.format(11)` | `0000000011`  | 进制                   |

### 修剪操作

字符串的`strip`方法可以帮我们获得将原字符串修剪左右两端空格之后的字符串。这个方法非常有实用价值，通常用来将用户输入因不小心键入的头尾空格去掉，`strip`方法还有`lstrip`和`rstrip`两个版本。

```python
s = '      tuling_python@163.com \t \r \n'
# strip 方法获得的字符串修剪左右两侧空格之后的字符串
print(s.strip()) # tuling_python@163.com
```

### 替换操作

如果希望用新的内容替换字符串中指定内容，可以用`replace`方法，代码如下。`replace`方法的第一个参数是被替换的内容，第二个参数是替换后的内容，还可以通过第三个参数指定替换的次数。

```python
s1 = 'hello, world'
print(s1.replace('o','@')) # hell@, w@rld
print(s1.replace('o','@',1)) # hell@, world
```

拆分、合并操作

可以使用字符串的`split`方法将一个字符串拆分为多个字符串（放在一个列表中），也可以使用字符串的`join`方法将列表中的多个字符串连接成一个字符串。

```python
s2 = 'I love you'

words = s2.split()
print(words) # ['I', 'love', 'you']
print('#'.join(words)) # I#love#you
```

需要说明的是，`split`方法默认使用空格进行拆分，也可以指定其他的字符来拆分字符串，而且还可以指定最大拆分次数来控制拆分的效果。

```python
s3 = 'I#love#you#so#much'
word1 = s3.split('#')
print(word1) # ['I', 'love', 'you', 'so', 'much']
word2 = s3.split('#', 3)
print(word2) # ['I', 'love', 'you', 'so#much']
```

### 编码、解码操作

python中除了字符串`str`类型外，还有一种表示二进制数据的字节串类型（bytes）。所谓字节串，就是由零个或多个字节组成的有限序列。通过字符串的`encode`方法，我们可以按照某种编码方式将字符串编码为字节串，也可以使用字节串的`decode`方法，将字节串解码为字符串，代码如下所示。

```python
a = '小红'
b = a.encode('utf-8')
c = a.encode('gbk')

print(b, c) # b'\xe5\xb0\x8f\xe7\xba\xa2' b'\xd0\xa1\xba\xec'

print(b.decode('utf-8')) # 小红
print(c.decode('gbk')) # 小红
```

注意：用什么方式编码 ，就用什么方式解码。


