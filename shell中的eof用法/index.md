# shell中的EOF用法

# shell中的EOF用法

#### EOF

Shell中通常将EOF与<<结合使用，表示后续的输入作为子命令或子shell的输入,直到遇到EOF为止，再返回到主shell。可以把EOF换成其他东西，意思是把内容当作标准输入传给程序。

当shell看到<<的时候，他就会知道下一个词是一个芬姐符。在该分界符以后的内容都会被当作输入，直到shell又看到该分界符（位于单独的一行）。这个分界符可以是你所定义的任何字符串。

例子：自动登录mysql,查询mysql库中user表的所有内容。

```shell
mysql -uroot -p1qaz9ol. <<EoF
use mysql;
select * from user \G;
exit
EOF
```

特殊用法：

```shell
:<<COMMENTBLOCK
shell 脚本代码段
COMMENTBLOCK
用来注释整段脚本代码。:是shell中的空语句。其中COMMENTBLOCK 可以换成任何字符。
```

实例：

```shell
#!/bin/bash
echo start
:<<COMMENTBLOCK
echo
echo "this is a test"
echo
COMMENTBLOCK
echo end
```

执行结果，中间部分不会执行。

```shell
start
end
```

#### cat 和eof结合使用具有追加功能

使用cat 和EOF多行输入

```shell
cat >> /root/test.txt <<EOF
123123123
hello world!
EOF
```

以上的脚本命令是，在test.txt文件后面加上三行代码。




