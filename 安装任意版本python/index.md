# 基于centos7/8安装任意版本python


### 基于centos7/8安装任意版本python

#### 编译安装python

### 安装python依赖环境

```python
sudo yum -y install epel-release
sudo yum -y update
```

重启(可选)

```python
reboot
```

### 安装编译环境

```python
sudo yum -y groupinstall "Development Tools"
sudo yum -y install openssl-devel bzip2-devel libffi-devel xz-devel
```

### 确认gcc可用

```python
$ gcc --version
gcc (GCC) 4.8.5 20150623 (Red Hat 4.8.5-44)
Copyright (C) 2015 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

### 选择编译包

[官网编译包](https://www.python.org/ftp/python/)

```python
wget https://www.python.org/ftp/python/3.9.12/Python-3.9.12.tgz
```

### 解压并进入

```python
tar -zxvf Python-3.9.12.tgz 
cd Python-3.9.12/
```

### 配置脚本

```python
./configure --enable-optimizations
```

### 开始编译

```python
sudo make altinstall
```

### 如果成功将会显示以下内容

```python
copying build/scripts-3.9/pydoc3.9 -> /usr/local/bin
copying build/scripts-3.9/idle3.9 -> /usr/local/bin
copying build/scripts-3.9/2to3-3.9 -> /usr/local/bin
changing mode of /usr/local/bin/pydoc3.9 to 755
changing mode of /usr/local/bin/idle3.9 to 755
changing mode of /usr/local/bin/2to3-3.9 to 755
rm /usr/local/lib/python3.9/lib-dynload/_sysconfigdata__linux_x86_64-linux-gnu.py
rm -r /usr/local/lib/python3.9/lib-dynload/__pycache__
/usr/bin/install -c -m 644 ./Misc/python.man \
	/usr/local/share/man/man1/python3.9.1
if test "xupgrade" != "xno"  ; then \
	case upgrade in \
		upgrade) ensurepip="--altinstall --upgrade" ;; \
		install|*) ensurepip="--altinstall" ;; \
	esac; \
	 ./python -E -m ensurepip \
		$ensurepip --root=/ ; \
fi
Looking in links: /tmp/tmp3k1lhlmm
Processing /tmp/tmp3k1lhlmm/setuptools-58.1.0-py3-none-any.whl
Processing /tmp/tmp3k1lhlmm/pip-22.0.4-py3-none-any.whl
Installing collected packages: setuptools, pip
Successfully installed pip-22.0.4 setuptools-58.1.0
```

### 确认是否安装成功

```python
[root@43f873a76b81 Python-3.9.12]# python3.9 --version 
Python 3.9.12
```

### pip是否也安装了

```python
[root@43f873a76b81 Python-3.9.12]# pip3.9 --version 
pip 22.0.4 from /usr/local/lib/python3.9/site-packages/pip (python 3.9)
```

