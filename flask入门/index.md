# Flask入门


## 数据可视化

### 关于Flask

Flask作为web框架，它的作用主要是开发web应用程序。

### Flask基础的操作

#### 浏览器打印`hello world!`

```python
from flask import Flask

# flask类接收一个参数__name__
app = Flask(__name__)

# 路由解析，通过用户范文的路径，匹配相应的函数。
@app.route('/')
def hello_world():
    return 'hello world!'

# debug模式开启,更改代码后，浏览器实时显示。
# Flask应用程序实例的run方法启动web服务器
if __name__ == '__main__':
    app.run()
```

运行代码

访问网址：`http://127.0.0.1:5000`屏幕打印`hello world!`

```python
FLASK_APP = app.py
FLASK_ENV = development
FLASK_DEBUG = 0
In folder D:/code2022/auto-work/demo
D:\pccharmcode\venv\Scripts\python.exe -m flask run
 * Running on http://127.0.0.1:5000 (Press CTRL+C to quit)
 * Serving Flask app 'app.py' (lazy loading)
 * Environment: development
 * Debug mode: off
```

#### 访问`/index`路径

```python
from flask import Flask

# flask类接收一个参数__name__
app = Flask(__name__)

# 路由解析，通过用户范文的路径，匹配相应的函数。
# 装饰起的作用是将路由映射到视图函数hello
@app.route("/index")
def hello():
    return "你好"

# debug模式开启,更改代码后，浏览器实时显示。
# Flask应用程序实例的run方法启动web服务器
if __name__ == '__main__':
    app.run()
```

#### 通过访问路径，获取用户的字符串参数

```python
from flask import Flask

# flask类接收一个参数__name__
app = Flask(__name__)

# 路由解析，通过用户范文的路径，匹配相应的函数。

@app.route("/user/<name>")
def welcom(name):
    return "你好,%s"% name

# debug模式开启,更改代码后，浏览器实时显示。
# Flask应用程序实例的run方法启动web服务器
if __name__ == '__main__':
    app.run()
```

#### 通过访问路径，获取用户的整形参数

```python
from flask import Flask

# flask类接收一个参数__name__
app = Flask(__name__)

# 路由解析，通过用户范文的路径，匹配相应的函数。

@app.route("/user/<int:id>")
def welcom2(id):
    return "你好,%d 号的会员"% id

# debug模式开启,更改代码后，浏览器实时显示。
# Flask应用程序实例的run方法启动web服务器
if __name__ == '__main__':
    app.run()
# 路由路径不能重复，用户通过唯一路径来访问特定的函数。
```

#### 返回用户渲染后的网页文件

```python
from flask import Flask, render_template, request

# flask类接收一个参数__name__
app = Flask(__name__)

# 路由解析，通过用户范文的路径，匹配相应的函数。

@app.route("/")
def index2():
    return render_template("index.html")

# debug模式开启,更改代码后，浏览器实时显示。
# Flask应用程序实例的run方法启动web服务器
if __name__ == '__main__':
    app.run()
# 路由路径不能重复，用户通过唯一路径来访问特定的函数。
```

#### 向页面传递一些变量

```python
from flask import Flask, render_template, request
import datetime
# flask类接收一个参数__name__
app = Flask(__name__)

# 路由解析，通过用户范文的路径，匹配相应的函数。

@app.route("/")
def index2():
    time = datetime.date.today()    # 普通变量
    name = ["小张", "小王", "小赵"]   # 列表类型
    task = {"任务": "打扫卫生", "时间": "3小时"}  # 字典类型
    return render_template("index.html", var = time, list = name, task = task)

# debug模式开启,更改代码后，浏览器实时显示。
# Flask应用程序实例的run方法启动web服务器
if __name__ == '__main__':
    app.run()
# 路由路径不能重复，用户通过唯一路径来访问特定的函数。
```

#### 表单提交

**至少需要2个页面，提交界面和结果界面。**

```python
from flask import Flask, render_template, request
import datetime
# flask类接收一个参数__name__
app = Flask(__name__)

# 路由解析，通过用户范文的路径，匹配相应的函数。

@app.route('/test/register')
def register():
    return render_template("test/register.html")

# debug模式开启,更改代码后，浏览器实时显示。
# Flask应用程序实例的run方法启动web服务器
if __name__ == '__main__':
    app.run()
# 路由路径不能重复，用户通过唯一路径来访问特定的函数。
```

#### 接收表单提交的路由，需要指定methods为post

```python
from flask import Flask, render_template, request
import datetime
# flask类接收一个参数__name__
app = Flask(__name__)

# 路由解析，通过用户范文的路径，匹配相应的函数。
@app.route('/result', methods=['POST','GET'])
def result():
    if request.method == 'POST':
        result = request.form
        return render_template("test/result.html", result=result)
    
# debug模式开启,更改代码后，浏览器实时显示。
# Flask应用程序实例的run方法启动web服务器
if __name__ == '__main__':
    app.run()
# 路由路径不能重复，用户通过唯一路径来访问特定的函数。
```




