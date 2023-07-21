# shell脚本常用参考

# Shell 脚本常用参考

## echo cat read sed awk

### cat

```shell
cat > /path/${TEMP}/account.conf<<EOF
export ${API_ID_HEADER}="${API_ID_INPUT}"
export ${API_KEY_HEADER}="${API_KEY_INPUT}"
EOF
# 单引号转译内容
# cat > /path/${TEMP}/account.conf<<'EOF'
```

### sed

```shell
sed -i \
    -e '/Port 22/a\Port 22222' \
    -e '/^PasswordAuthentication.*yes$/s/yes/no/g' \
    -e '/^PermitRootLogin.*no$/s/no/yes/g' \
    -e '/^#PermitRoot.*yes$/s/#PermitRoot/PermitRoot/g' \
    -e '/#ClientAliveInterval/s/#ClientAliveInterval 0/ClientAliveInterval 30/g' \
    -e '/#MaxSessions/s/#MaxSessions 10/MaxSessions 100/g' \
    /etc/ssh/sshd_config
```

### awk

从文件获取 分隔符 = 如果第一列含有 DOMAIN 打印第二列

```shell
DOMAIN=$(cat /conf/account.conf | awk -F= '{if($1~"DOMAIN")print $2}')
```

### read

```shell
read -p "请输入:" INPUT
```

### case

```shell
case "$INPUT" in
    1)
    echo "1"
    ;;
    2)
    echo "2"
    ;;
    *)
    echo "其他"
    exit 0
    esac
```

## if 判断

字符串对比判断

```shell
if [ "$INPUT" == "3" ]; then
    echo "变量等于 3"
fi
```

判断变量是否存在

```shell
if [ ! -n "${INPUT}" ]; then
    echo "变量不存在"
fi
```

判断目录是否存在

```shell
if [ -d /data/test ]; then
    echo "目录存在"
fi
```

判断文件是否存在

```shell
if [ -f /data/test ]; then
    echo "目录存在"
fi
```

服务器软件判断

```shell
if type docker-compose >/dev/null 2>&1; then
    echo "已安装 docker-compose"
else
    echo "未安装 docker-compose"
fi
```

判断是否是root

```shell
if [[ $EUID -ne 0 ]]; then
    echo "非 root 用户"
    exit 1
fi
```

判断 OpenVZ

```shell
if [[ -d "/proc/vz" ]]; then
    echo -e "这是 OpenVZ,非 KVM"
    exit 1
fi
```

数值比较判断

```shell
if [ $TIMES -eq 3 ]; then
    echo "Times 大于 3"
fi
```

## 运算符

### 算术运算符

```shell
#!/bin/sh
a=10
b=20
val=`expr $a + $b`
echo "a + b : $val"
val=`expr $a - $b`
echo "a - b : $val"
val=`expr $a \* $b`
echo "a * b : $val"
val=`expr $b / $a`
echo "b / a : $val"
val=`expr $b % $a`
echo "b % a : $val"
if [ $a == $b ]
then
   echo "a is equal to b"
fi
if [ $a != $b ]
then
   echo "a is not equal to b"
fi
```

| 运算符 | 说明                                          | 举例                         |
| ------ | --------------------------------------------- | ---------------------------- |
| +      | 加法                                          | `expr $a + $b` 结果为 30。   |
| -      | 减法                                          | `expr $a - $b` 结果为 10。   |
| *      | 乘法                                          | `expr $a \* $b` 结果为 200。 |
| /      | 除法                                          | `expr $b / $a` 结果为 2。    |
| %      | 取余                                          | `expr $b % $a` 结果为 0。    |
| =      | 赋值                                          | a=$b 将把变量 b 的值赋给 a。 |
| ==     | 相等。用于比较两个数字，相同则返回 true。     | [ $a == $b ] 返回 false。    |
| !=     | 不相等。用于比较两个数字，不相同则返回 true。 | [ $a != $b ] 返回 true。     |

### 关系运算符

```shell
#!/bin/sh
a=10
b=20
if [ $a -eq $b ]
then
   echo "$a -eq $b : a is equal to b"
else
   echo "$a -eq $b: a is not equal to b"
fi
if [ $a -ne $b ]
then
   echo "$a -ne $b: a is not equal to b"
else
   echo "$a -ne $b : a is equal to b"
fi
if [ $a -gt $b ]
then
   echo "$a -gt $b: a is greater than b"
else
   echo "$a -gt $b: a is not greater than b"
fi
if [ $a -lt $b ]
then
   echo "$a -lt $b: a is less than b"
else
   echo "$a -lt $b: a is not less than b"
fi
if [ $a -ge $b ]
then
   echo "$a -ge $b: a is greater or  equal to b"
else
   echo "$a -ge $b: a is not greater or equal to b"
fi
if [ $a -le $b ]
then
   echo "$a -le $b: a is less or  equal to b"
else
   echo "$a -le $b: a is not less or equal to b"
fi
```

关系运算符列表

| 运算符 | 说明                                                  | 举例                       |
| ------ | ----------------------------------------------------- | -------------------------- |
| -eq    | 检测两个数是否相等，相等返回 true。                   | [ $a -eq $b ] 返回 true。  |
| -ne    | 检测两个数是否相等，不相等返回 true。                 | [ $a -ne $b ] 返回 true。  |
| -gt    | 检测左边的数是否大于右边的，如果是，则返回 true。     | [ $a -gt $b ] 返回 false。 |
| -lt    | 检测左边的数是否小于右边的，如果是，则返回 true。     | [ $a -lt $b ] 返回 true。  |
| -ge    | 检测左边的数是否大等于右边的，如果是，则返回 true。   | [ $a -ge $b ] 返回 false。 |
| -le    | 检测左边的数是否小于等于右边的，如果是，则返回 true。 | [ $a -le $b ] 返回 true。  |

### 布尔运算符

```shell
#!/bin/sh
a=10
b=20
if [ $a != $b ]
then
   echo "$a != $b : a is not equal to b"
else
   echo "$a != $b: a is equal to b"
fi
if [ $a -lt 100 -a $b -gt 15 ]
then
   echo "$a -lt 100 -a $b -gt 15 : returns true"
else
   echo "$a -lt 100 -a $b -gt 15 : returns false"
fi
if [ $a -lt 100 -o $b -gt 100 ]
then
   echo "$a -lt 100 -o $b -gt 100 : returns true"
else
   echo "$a -lt 100 -o $b -gt 100 : returns false"
fi
if [ $a -lt 5 -o $b -gt 100 ]
then
   echo "$a -lt 100 -o $b -gt 100 : returns true"
else
   echo "$a -lt 100 -o $b -gt 100 : returns false"
fi
```

| 运算符 | 说明                                                | 举例                                     |
| ------ | --------------------------------------------------- | ---------------------------------------- |
| !      | 非运算，表达式为 true 则返回 false，否则返回 true。 | [ ! false ] 返回 true。                  |
| -o     | 或运算，有一个表达式为 true 则返回 true。           | [ $a -lt 20 -o $b -gt 100 ] 返回 true。  |
| -a     | 与运算，两个表达式都为 true 才返回 true。           | [ $a -lt 20 -a $b -gt 100 ] 返回 false。 |

### 字符串运算符

```shell
#!/bin/sh
a="abc"
b="efg"
if [ $a = $b ]
then
   echo "$a = $b : a is equal to b"
else
   echo "$a = $b: a is not equal to b"
fi
if [ $a != $b ]
then
   echo "$a != $b : a is not equal to b"
else
   echo "$a != $b: a is equal to b"
fi
if [ -z $a ]
then
   echo "-z $a : string length is zero"
else
   echo "-z $a : string length is not zero"
fi
if [ -n $a ]
then
   echo "-n $a : string length is not zero"
else
   echo "-n $a : string length is zero"
fi
if [ $a ]
then
   echo "$a : string is not empty"
else
   echo "$a : string is empty"
fi
```

| 运算符 | 说明                                      | 举例                     |
| ------ | ----------------------------------------- | ------------------------ |
| =      | 检测两个字符串是否相等，相等返回 true。   | [ $a = $b ] 返回 false。 |
| !=     | 检测两个字符串是否相等，不相等返回 true。 | [ $a != $b ] 返回 true。 |
| -z     | 检测字符串长度是否为0，为0返回 true。     | [ -z $a ] 返回 false。   |
| -n     | 检测字符串长度是否为0，不为0返回 true。   | [ -z $a ] 返回 true。    |
| str    | 检测字符串是否为空，不为空返回 true。     | [ $a ] 返回 true。       |

### 文件测试运算符列表

```shell
#!/bin/sh
file="/var/www/tutorialspoint/unix/test.sh"
if [ -r $file ]
then
   echo "File has read access"
else
   echo "File does not have read access"
fi
if [ -w $file ]
then
   echo "File has write permission"
else
   echo "File does not have write permission"
fi
if [ -x $file ]
then
   echo "File has execute permission"
else
   echo "File does not have execute permission"
fi
if [ -f $file ]
then
   echo "File is an ordinary file"
else
   echo "This is sepcial file"
fi
if [ -d $file ]
then
   echo "File is a directory"
else
   echo "This is not a directory"
fi
if [ -s $file ]
then
   echo "File size is not zero"
else
   echo "File size is zero"
fi
if [ -e $file ]
then
   echo "File exists"
else
   echo "File does not exist"
fi
```

| 操作符  | 说明                                                         | 举例                      |
| ------- | ------------------------------------------------------------ | ------------------------- |
| -b file | 检测文件是否是块设备文件，如果是，则返回 true。              | [ -b $file ] 返回 false。 |
| -c file | 检测文件是否是字符设备文件，如果是，则返回 true。            | [ -b $file ] 返回 false。 |
| -d file | 检测文件是否是目录，如果是，则返回 true。                    | [ -d $file ] 返回 false。 |
| -f file | 检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。 | [ -f $file ] 返回 true。  |
| -g file | 检测文件是否设置了 SGID 位，如果是，则返回 true。            | [ -g $file ] 返回 false。 |
| -k file | 检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。  | [ -k $file ] 返回 false。 |
| -p file | 检测文件是否是具名管道，如果是，则返回 true。                | [ -p $file ] 返回 false。 |
| -u file | 检测文件是否设置了 SUID 位，如果是，则返回 true。            | [ -u $file ] 返回 false。 |
| -r file | 检测文件是否可读，如果是，则返回 true。                      | [ -r $file ] 返回 true。  |
| -w file | 检测文件是否可写，如果是，则返回 true。                      | [ -w $file ] 返回 true。  |
| -x file | 检测文件是否可执行，如果是，则返回 true。                    | [ -x $file ] 返回 true。  |
| -s file | 检测文件是否为空（文件大小是否大于0），不为空返回 true。     | [ -s $file ] 返回 true。  |
| -e file | 检测文件（包括目录）是否存在，如果是，则返回 true。          | [ -e $file ] 返回 true。  |

## 参数

[参考liunk](http://c.biancheng.net/cpp/view/2739.html)

| 变量 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| $0   | 当前脚本的文件名                                             |
| $n   | 传递给脚本或函数的参数。n 是一个数字，表示第几个参数。例如，第一个参数是$1，第二个参数是$2。 |
| $#   | 传递给脚本或函数的参数个数。                                 |
| $*   | 传递给脚本或函数的所有参数。                                 |
| $@   | 传递给脚本或函数的所有参数。被双引号(" ")包含时，与 $* 稍有不同，下面将会讲到。 |
| $?   | 上个命令的退出状态，或函数的返回值。                         |
| $$   | 当前Shell进程ID。对于 Shell 脚本，就是这些脚本所在的进程ID。 |

## 循环

标准按次循环

```shell
for (( i=1;i<=3;i++ )); do
    xxx
done
```

for 文件循环 套 while 比对循环

```shell
for LOG in $(ls -1 ${LOG_PATH}/*.log)
do
    SIZE=`ls -l $LOG | awk '{print $5}'`
    while [ $SIZE -gt $MAX_SIZE ]
    do
        sed -i '1,100000d' $LOG
        SIZE=`ls -l $LOG | awk '{print $5}'`
    done
done
```

