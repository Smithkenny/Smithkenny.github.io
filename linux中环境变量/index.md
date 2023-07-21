# linux中环境变量

# linux中环境变量

#### 针对当前会话（重启失效）

```python
PATH=$PATH:/home/test/mysql/bin
echo $PATH
```

#### 针对当前用户有效（重启失效）

```shell
vim ~/.bash_profile
#更改，原行 PATH=$PATH:$HOME/bin， 改为下面的
PATH=$PATH:$HOME/bin:/home/test/mysql/bin
#保存退出
source ~/.bash_profile
#或者
. ~/.bash_profile
#使之生效
```

#### /etc/profile/(永久生效)

```shell
vim /etc/profile
#在文档最后新增
MYSQL_HOME=/home/test/mysql
export PATH=$PATH:$MYSQL_HOME/bin
#保存退出
source ~/.bash_profile
#或者
. ~/.bash_profile
#使之生效
```

#### /etc/profile.d/ (永久生效)(推荐)

```shell
vim /etc/profile.d/mysql.sh
#添加如下内容
MYSQL_HOME=/home/test/mysql
export PATH=$PATH:$MYSQL_HOME/bin
#保存退出
source /etc/profile
#或者
. /etc/profile
#使之生效
```

**Tips:**

**推荐原因：只需要更改自己的脚本，增加或删除就可以使环境变量发生改变，不影响系统文件。**




