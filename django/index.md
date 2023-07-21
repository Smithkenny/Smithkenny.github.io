# Django


### 安装及查看版本

```python
pip3 install Django==4.0.6

# 进入python
python
import django
print(django.get_version())

```

windows安装路径

```python
C:\Users\Administrator\AppData\Local\Programs\Python\Python39\Lib\site-packages\django
```

### 创建第一个django 项目

```python
# 新建一个文件夹
E:\code2022\django\demo
# 进入文件夹 
cd E:\code2022\django\demo
# 创建项目 mysite 是项目名称
django-admin.exe startproject mysite
# 进入到mysite 然后启动项目
cd mysite
python manage.py runserver

"""
July 08, 2022 - 15:34:30
Django version 4.0.6, using settings 'mysite.settings'
Starting development server at http://127.0.0.1:8000/
"""
# 访问：http://127.0.0.1:8000/ 出现网页，项目启动成功。
```

注意：启动项目可以指定端口，不指定默认是8000

```python
python manage.py runserver 8002
```

创建网页文件`views.py`

**在mysite文件夹内部创建**

```python
from django.http import HttpResponse

def hello(requets):
    return HttpResponse("Hello world! ")
```

修改url路径

编辑`urls.py`

```python
from django.urls import path
from . import views

urlpatterns = [
    path('hello/', views.hello),
]
```

测试访问：

```python
http://127.0.0.1:8000/hello/
```

访问过程：

```python
浏览器访问：http://127.0.0.1:8000/hello/ -> 先到urls.py中hello路径对应的页面 views.hello->
        然后执行views.py中定义的hello函数->最后把结果返回给浏览器。
```

创建第二个项目`mysite2`

```python
# 路径  E:\code2022\django\demo
django-admin.exe startproject mysite2
```

创建和`mysite2`同级的文件夹`templates`并在其内部创建`mytemp.html`

```python
# 路径  E:\code2022\django\demo\mysite2 注意要和mysite2 内部的mysite2同级。
# mytemp.html 内容如下
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
        <h1>{{ hello }}</h1>
    </body>
</html>
```

修改`E:\code2022\django\demo\mysite2\mysite2\settings.py`文件内容

找到刚刚创建的`templates`文件夹路径，修改下

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR,'templates')],
        
在第一行引入os模块
        import os
```

解释`os.path.john(BASE_DIR,'templates')`

引入`os`模块，将`BASE_DIR`(项目绝对路径)和`templates`做路径拼接。拼接后的结果是：`E:\code2022\django\demo\mysite2\templates`

在`E:\code2022\django\demo\mysite2\mysite2`中创建`views.py`文件

```python
from django.shortcuts import render


def mytemp(request):

    context          = {}
    context['hello'] = 'Hello World!'
    return render(request, 'mytemp.html', context)
```

修改url（`E:\code2022\django\demo\mysite2\mysite2\urls.py`）

```python
from django.urls import path

from . import views

urlpatterns = [
    path('mytemp/', views.mytemp),
]
```

进入项目所在文件夹，在cmd中启动项目指定端口8002

```python
python manage.py runserver 8002
```

测试访问

```python
http://127.0.0.1:8002/mytemp/
```

访问过程

```python
127.0.0.1:8002/mytemp
    ->urls.py中寻找/mytemp 它的映射为： views.mytemp
    -> mytemp函数在views.py中定义
    ->mytemp函数还定义了context空字典，传入键（hello）和值(Hello World!) 。键将在主页（mytemp.html）中调用。
    -> h1标签中调用键（hello）
```

### django模板标签

[链接](https://www.cnblogs.com/emanlee/p/15853546.html)

### **列表**

templates 中的 mytemp.html 中，可以用 . 索引下标取出对应的元素。

`D:\code2022\Django\demo\mysite2\mysite2\urls.py`中修改路径

```python
from django.urls import path
from . import views
urlpatterns = [
    path('mytemp/', views.mydef),
]
```

新建`D:\code2022\Django\demo\mysite2\mysite2\views.py`文件，定义`mydef`函数

```python
from django.shortcuts import render

def mydef(request):
    views_list = ["简单示例demo1","简单示例demo2","简单示例demo3"]
    return render(request, 'mytemp.html', {"views_list": views_list})
```

创建模板文件夹和主页文件

`D:\code2022\Django\demo\mysite2\templates`和`D:\code2022\Django\demo\mysite2\templates\mytemp.html`

**Tip**:可以用`!+Tab`键快速创建html代码，然后再添加内容。s

```python
# mytemp.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <p>{{ views_list }}</p> # 取出整个列表
<p>{{ views_list.0 }}</p> # 取出列表的第一个元素
</head>
<body>
    
</body>
</html>
```

设置主页文件(`mytemp.html`)路径

`D:\code2022\Django\demo\mysite2\mysite2\settings.py`

找到关于`templates`文件夹的配置

```python
import os 

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR,'templates')],
```

启动项目

```python
python .\manage.py runserver 8003
```

访问`127.0.0.1:8003/mytemp`测试。

### 字典

`D:\code2022\Django\demo\mysite3\mysite3\urls.py`

```python
from django.urls import path
from . import views
urlpatterns = [
    path('mytemp/', views.mydef),
]
```

`D:\code2022\Django\demo\mysite3\mysite3\views.py`

```python
from django.shortcuts import render

def mydef(request):
    views_dict = {'name':'简单demo演示'}
    return render(request,'mytemp.html', {'views_dict': views_dict})
```

`D:\code2022\Django\demo\mysite3\templates\mytemp.html`

```python
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <p>{{ views_dict }}</p>
<p>{{ views_dict.name }}</p>
</head>
<body>
    
</body>
</html>
```

`D:\code2022\Django\demo\mysite3\mysite3\settings.py`

```python
import os

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR,'templates')],
```

启动项目并测试

```python
python .\manage.py runserver 8004
http://127.0.0.1:8004/mytemp
```










