# 豆瓣电影top250展示页



## 豆瓣展示页

### 新建flask项目

使用`pycharm`新建`flask`项目，项目名为：`douban_flask`。

将之前的爬取的`movie.db`文件放到该项目的根目录下。准备前端页面代码（可以到模板之家下载[http://www.cssmoban.com/cssthemes/9296.shtml]）

### 前端代码

下载后解压缩放到`static`目录下.`static`目录用于存放网页相关代码。

目录结构如下：

```python
douban_flask
- static
  - assets
    - css
    - img
    - js
    - vendor
- templates
  - test
    echarts.min.js
  index.html
  movie.html
  score.html
  team.html
  temp.html
  word.html
- venv
  app.py
  movie.db
  testCloud.py
```

新建模板目录`templates`，在此目录下新建主页文件`index.html`.可以把`temp.html`中的内容复制一份到`index.html`。

### flask路由

开始写路由`app.py`

Tips:使用`flask`下`render_template`模块可以找到`templates`目录内的`html`页面。

```python
from flask import Flask, render_template
import sqlite3
app = Flask(__name__)


@app.route('/')
def index():
    return render_template("index.html")

@app.route('/index')
def home():
    # return render_template("index.html")
    return index()

@app.route('/movie')
def movie():
    datalist = []
    con = sqlite3.connect("movie.db")
    cur = con.cursor()
    sql = "select * from movie250"
    data = cur.execute(sql)
    for item in data:
        datalist.append(item)
    cur.close()
    con.close()
    return render_template("movie.html", movies = datalist)

    # return render_template("movie.html")

@app.route('/score')
def score():
    score = []  # 评分
    num = []    # 每个评分所统计出的电影数量
    con = sqlite3.connect("movie.db")
    cur = con.cursor()
    sql = "select score,count(score) from movie250 group by score"
    data = cur.execute(sql)
    for item in data:
        score.append(str(item[0]))
        num.append(item[1])
    cur.close()
    con.close()
    return render_template("score.html", score=score, num=num)

@app.route('/word')
def word():
    return render_template("word.html")

@app.route('/team')
def team():
    return render_template("team.html")

if __name__ == '__main__':
    app.run()
```

开始运行`flask`(可以开启Debug模式，好处是修改的代码会实时更新。关闭则需要重启运行后更改的代码才会生效。)

**注意：**

**如果页面只显示文字，没有`css`效果也不显示图片，查看页面源代码。(谷歌浏览器：右键->检查)查看`css样式`**
**路径和图片路径是否正确。路径应该和我们的项目`douban_flask`目录结构一致。更改后，刷新浏览器再次查看效果。**

显示正常后，我们需要展示5个页面，分别是`首页`、`电影`、`评分`、`词云`、`团队`。所以将`index.html`复制4份，分别修改里面的内容，展示对应的数据。对应关系如下：

|    首页    |    电影    |    评分    |   词云    |   团队    |
| :--------: | :--------: | :--------: | :-------: | :-------: |
| index.html | movie.html | score.html | word.html | team.html |

### index

先做主页`index.html`，仔细观察源码发现网页的每一部分都有注释该模块的功能。由于代码太多，可以将同一种功能的代码折叠起来，使用的功能保留，不用的功能可以删除。最终`index.html`中代码如下：

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta content="width=device-width, initial-scale=1.0" name="viewport">

  <title>豆瓣top250数据可视化</title>
  <meta content="" name="descriptison">
  <meta content="" name="keywords">

  <!-- Favicons -->
  <link href="static/assets/img/favicon.png" rel="icon">
  <link href="static/assets/img/apple-touch-icon.png" rel="apple-touch-icon">

  <!-- Google Fonts -->
  <link href="https://fonts.googleapis.com/css?family=Open+Sans:300,300i,400,400i,600,600i,700,700i|Raleway:300,300i,400,400i,600,600i,700,700i,900" rel="stylesheet">

  <!-- Vendor CSS Files -->
  <link href="static/assets/vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">
  <link href="static/assets/vendor/icofont/icofont.min.css" rel="stylesheet">
  <link href="static/assets/vendor/boxicons/css/boxicons.min.css" rel="stylesheet">
  <link href="static/assets/vendor/animate.css/animate.min.css" rel="stylesheet">
  <link href="static/assets/vendor/venobox/venobox.css" rel="stylesheet">
  <link href="static/assets/vendor/aos/aos.css" rel="stylesheet">

  <!-- Template Main CSS File -->
  <link href="static/assets/css/style.css" rel="stylesheet">

</head>

<body>

  <!-- ======= Top Bar ======= -->


  <!-- ======= Header ======= -->
  <header id="header">
    <div class="container">

      <div class="logo float-left">
        <h1 class="text-light"><a href="index"><span>豆瓣电影</span></a></h1>
        <!-- Uncomment below if you prefer to use an image logo -->
        <!-- <a href="temp.html"><img src="static/assets/img/logo.png" alt="" class="img-fluid"></a>-->
      </div>

      <nav class="nav-menu float-right d-none d-lg-block">
        <ul>
          <li class="active"><a href="/index">首页 <i class="la la-angle-down"></i></a></li>
          <li><a href="/movie">电影</a></li>
          <li><a href="/score">评分</a></li>
          <li><a href="/word">词云</a></li>
          <li><a href="/team">团队</a></li>

        </ul>
      </nav><!-- .nav-menu -->

    </div>
  </header><!-- End Header -->

    <!-- ======= Our Team Section ======= -->
    <section id="team" class="team">
      <div class="container">

        <div class="section-title">
          <h2>豆瓣电影top250数据分析</h2>
          <p>应用Python爬虫Flask框架、Echarts、WordCloud等技术实现</p>
        </div>

            <!-- ======= Counts Section ======= -->
    <section class="counts section-bg">
      <div class="container">

        <div class="row">

          <div class="col-lg-3 col-md-6 text-center" data-aos="fade-up">
              <a href= "/movie">
            <div class="count-box">
              <i class="icofont-simple-smile" style="color: #20b38e;"></i>
              <span data-toggle="counter-up">250</span>
              <p>经典电影</p>
            </div>
              </a>
          </div>

          <div class="col-lg-3 col-md-6 text-center" data-aos="fade-up" data-aos-delay="200">
              <a href= "/score">
            <div class="count-box">
              <i class="icofont-document-folder" style="color: #c042ff;"></i>
              <span data-toggle="counter-up">1</span>
              <p>评分统计</p>
            </div>
              </a>
          </div>

          <div class="col-lg-3 col-md-6 text-center" data-aos="fade-up" data-aos-delay="400">
              <a href= "/word">
            <div class="count-box">
              <i class="icofont-live-support" style="color: #46d1ff;"></i>
              <span data-toggle="counter-up">5693</span>
              <p>词汇统计</p>
            </div>
              </a>
          </div>

          <div class="col-lg-3 col-md-6 text-center" data-aos="fade-up" data-aos-delay="600">
              <a href= "/team">
            <div class="count-box">
              <i class="icofont-users-alt-5" style="color: #ffb459;"></i>
              <span data-toggle="counter-up">4</span>
              <p>团队成员</p>
            </div>
              </a>
          </div>

        </div>

      </div>
    </section><!-- End Counts Section -->

      </div>
    </section><!-- End Our Team Section -->

  <!-- ======= Footer ======= -->
  <footer id="footer">


    <div class="container">
      <div class="copyright">
        &copy; Copyright <strong><span>Mr.Lee</span></strong>. All Rights Reserved
      </div>
      <div class="credits">
      </div>
    </div>
  </footer><!-- End Footer -->

  <a href="#" class="back-to-top"><i class="icofont-simple-up"></i></a>

  <!-- Vendor JS Files -->
  <script src="static/assets/vendor/jquery/jquery.min.js"></script>
  <script src="static/assets/vendor/bootstrap/js/bootstrap.bundle.min.js"></script>
  <script src="static/assets/vendor/jquery.easing/jquery.easing.min.js"></script>
  <script src="static/assets/vendor/php-email-form/validate.js"></script>
  <script src="static/assets/vendor/jquery-sticky/jquery.sticky.js"></script>
  <script src="static/assets/vendor/venobox/venobox.min.js"></script>
  <script src="static/assets/vendor/waypoints/jquery.waypoints.min.js"></script>
  <script src="static/assets/vendor/counterup/counterup.min.js"></script>
  <script src="static/assets/vendor/isotope-layout/isotope.pkgd.min.js"></script>
  <script src="static/assets/vendor/aos/aos.js"></script>

  <!-- Template Main JS File -->
  <script src="static/assets/js/main.js"></script>

</body>

</html>
```

### movie

`movie.html`电影页面需要连接之前准备好的数据库`movie.db`，读取里面的`sql`语句，并以表格的形式显示出来。

这里使用`sqlite3`模块来连接数据库。

实现功能代码见`app.py`中`movie()`函数

`movie.html`网页源码如下：

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta content="width=device-width, initial-scale=1.0" name="viewport">

  <title>豆瓣top250数据可视化</title>
  <meta content="" name="descriptison">
  <meta content="" name="keywords">

  <!-- Favicons -->
  <link href="static/assets/img/favicon.png" rel="icon">
  <link href="static/assets/img/apple-touch-icon.png" rel="apple-touch-icon">

  <!-- Google Fonts -->
  <link href="https://fonts.googleapis.com/css?family=Open+Sans:300,300i,400,400i,600,600i,700,700i|Raleway:300,300i,400,400i,600,600i,700,700i,900" rel="stylesheet">

  <!-- Vendor CSS Files -->
  <link href="static/assets/vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">
  <link href="static/assets/vendor/icofont/icofont.min.css" rel="stylesheet">
  <link href="static/assets/vendor/boxicons/css/boxicons.min.css" rel="stylesheet">
  <link href="static/assets/vendor/animate.css/animate.min.css" rel="stylesheet">
  <link href="static/assets/vendor/venobox/venobox.css" rel="stylesheet">
  <link href="static/assets/vendor/aos/aos.css" rel="stylesheet">

  <!-- Template Main CSS File -->
  <link href="static/assets/css/style.css" rel="stylesheet">

</head>

<body>

  <!-- ======= Top Bar ======= -->


  <!-- ======= Header ======= -->
  <header id="header">
    <div class="container">

      <div class="logo float-left">
        <h1 class="text-light"><a href="index"><span>豆瓣电影</span></a></h1>
        <!-- Uncomment below if you prefer to use an image logo -->
        <!-- <a href="temp.html"><img src="static/assets/img/logo.png" alt="" class="img-fluid"></a>-->
      </div>

      <nav class="nav-menu float-right d-none d-lg-block">
        <ul>
          <li class="active"><a href="/index">首页 <i class="la la-angle-down"></i></a></li>
          <li><a href="/movie">电影</a></li>
          <li><a href="/score">评分</a></li>
          <li><a href="/word">词云</a></li>
          <li><a href="/team">团队</a></li>

        </ul>
      </nav><!-- .nav-menu -->

    </div>
  </header><!-- End Header -->

    <!-- ======= Our Team Section ======= -->
    <section id="team" class="team">
      <div class="container">

        <div class="section-title">
          <h2>豆瓣电影top250电影</h2>
        </div>

            <!-- ======= Counts Section ======= -->
    <section class="counts section-bg">
      <div class="container">

        <table class="table table-striped">
            <tr>
                <td>排名</td>
                <td>电影名称</td>
                <td>外国名称</td>
                <td>评分</td>
                <td>评价人数</td>
                <td>一句话描述</td>
                <td>其他信息</td>
            </tr>

            {% for movie in movies %}
               <tr>
               <td>{{ movie[0] }}</td>
               <td>
                    <a href="{{ movie[1] }}">
                    {{movie[3]}}
                    </a>
                </td>
                <td>{{ movie[4] }}</td>
                <td>{{ movie[5] }}</td>
                <td>{{ movie[6] }}</td>
                <td>{{ movie[7] }}</td>
                <td>{{ movie[8] }}</td>
            </tr>
            {% endfor %}
        </table>

      </div>
    </section><!-- End Counts Section -->

      </div>
    </section><!-- End Our Team Section -->



  <!-- ======= Footer ======= -->
  <footer id="footer">


    <div class="container">
      <div class="copyright">
        &copy; Copyright <strong><span>Mr.Lee</span></strong>. All Rights Reserved
      </div>
      <div class="credits">
      </div>
    </div>
  </footer><!-- End Footer -->

  <a href="#" class="back-to-top"><i class="icofont-simple-up"></i></a>

  <!-- Vendor JS Files -->
  <script src="static/assets/vendor/jquery/jquery.min.js"></script>
  <script src="static/assets/vendor/bootstrap/js/bootstrap.bundle.min.js"></script>
  <script src="static/assets/vendor/jquery.easing/jquery.easing.min.js"></script>
  <script src="static/assets/vendor/php-email-form/validate.js"></script>
  <script src="static/assets/vendor/jquery-sticky/jquery.sticky.js"></script>
  <script src="static/assets/vendor/venobox/venobox.min.js"></script>
  <script src="static/assets/vendor/waypoints/jquery.waypoints.min.js"></script>
  <script src="static/assets/vendor/counterup/counterup.min.js"></script>
  <script src="static/assets/vendor/isotope-layout/isotope.pkgd.min.js"></script>
  <script src="static/assets/vendor/aos/aos.js"></script>

  <!-- Template Main JS File -->
  <script src="static/assets/js/main.js"></script>

</body>

</html>
```



### score

`score.html`评分，这里获取数据和电影页面类似，也是需要读取数据库，不过最终是以柱形图的形式展示出来。使用`Echarts`技术来实现。

实现功能代码见`app.py`中`score()`函数

`score.html`源码如下：

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta content="width=device-width, initial-scale=1.0" name="viewport">
    <script src="static/assets/js/echarts.min.js"></script>
  <title>豆瓣top250数据可视化</title>
  <meta content="" name="descriptison">
  <meta content="" name="keywords">

  <!-- Favicons -->
  <link href="static/assets/img/favicon.png" rel="icon">
  <link href="static/assets/img/apple-touch-icon.png" rel="apple-touch-icon">

  <!-- Google Fonts -->
  <link href="https://fonts.googleapis.com/css?family=Open+Sans:300,300i,400,400i,600,600i,700,700i|Raleway:300,300i,400,400i,600,600i,700,700i,900" rel="stylesheet">

  <!-- Vendor CSS Files -->
  <link href="static/assets/vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">
  <link href="static/assets/vendor/icofont/icofont.min.css" rel="stylesheet">
  <link href="static/assets/vendor/boxicons/css/boxicons.min.css" rel="stylesheet">
  <link href="static/assets/vendor/animate.css/animate.min.css" rel="stylesheet">
  <link href="static/assets/vendor/venobox/venobox.css" rel="stylesheet">
  <link href="static/assets/vendor/aos/aos.css" rel="stylesheet">

  <!-- Template Main CSS File -->
  <link href="static/assets/css/style.css" rel="stylesheet">

</head>

<body>

  <!-- ======= Top Bar ======= -->


  <!-- ======= Header ======= -->
  <header id="header">
    <div class="container">

      <div class="logo float-left">
        <h1 class="text-light"><a href="index"><span>豆瓣电影</span></a></h1>
        <!-- Uncomment below if you prefer to use an image logo -->
        <!-- <a href="temp.html"><img src="static/assets/img/logo.png" alt="" class="img-fluid"></a>-->
      </div>

      <nav class="nav-menu float-right d-none d-lg-block">
        <ul>
          <li class="active"><a href="/index">首页 <i class="la la-angle-down"></i></a></li>
          <li><a href="/movie">电影</a></li>
          <li><a href="/score">评分</a></li>
          <li><a href="/word">词云</a></li>
          <li><a href="/team">团队</a></li>

        </ul>
      </nav><!-- .nav-menu -->

    </div>
  </header><!-- End Header -->

    <!-- ======= Our Team Section ======= -->
    <section id="team" class="team">
      <div class="container">

        <div class="section-title">
          <h2>豆瓣电影top250评分分布图</h2>
          <p>应用Python爬虫Flask框架、Echarts、WordCloud等技术实现</p>
        </div>

            <!-- ======= Counts Section ======= -->
    <section class="counts section-bg">
      <div class="container">

            <!-- 为 ECharts 准备一个定义了宽高的 DOM -->
            <div id="main" style="width: 100%;height: 300px;"></div>

      </div>
    </section><!-- End Counts Section -->

      </div>
    </section><!-- End Our Team Section -->
    
    <!-----Echarts----------->
 <script type="text/javascript">
    var dom = document.getElementById('main');
    var myChart = echarts.init(dom, null, {
      renderer: 'canvas',
      useDirtyRect: false
    });
    var app = {};

    var option;

    option = {
  title:{
    text:'电影评分表'
  },
  color:['#3398DB'],
  tooltip: {
    trigger:'axis',
    axisPointer:{
      type:'shadow'
    }
  },
  grid:{
    left:'3%',
    right:'4%',
    bottom:'3%',
    containLabel:true
  },
  xAxis: {
    type: 'category',
    data: {{ score|tojson }}
      <!----['mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun','999']----->
  },
  yAxis: {
    type: 'value'
  },
  series: [
    {
      data: {{ num }},
        <!---[120, 20, 150, 80, 70, 110, 130,200],----->
      barWidth:'60%',
      type: 'bar'
    }
  ]
};

    if (option && typeof option === 'object') {
      myChart.setOption(option);
    }

    window.addEventListener('resize', myChart.resize);
  </script>


  <!-- ======= Footer ======= -->


  <a href="#" class="back-to-top"><i class="icofont-simple-up"></i></a>

  <!-- Vendor JS Files -->
  <script src="static/assets/vendor/jquery/jquery.min.js"></script>
  <script src="static/assets/vendor/bootstrap/js/bootstrap.bundle.min.js"></script>
  <script src="static/assets/vendor/jquery.easing/jquery.easing.min.js"></script>
  <script src="static/assets/vendor/php-email-form/validate.js"></script>
  <script src="static/assets/vendor/jquery-sticky/jquery.sticky.js"></script>
  <script src="static/assets/vendor/venobox/venobox.min.js"></script>
  <script src="static/assets/vendor/waypoints/jquery.waypoints.min.js"></script>
  <script src="static/assets/vendor/counterup/counterup.min.js"></script>
  <script src="static/assets/vendor/isotope-layout/isotope.pkgd.min.js"></script>
  <script src="static/assets/vendor/aos/aos.js"></script>

  <!-- Template Main JS File -->
  <script src="static/assets/js/main.js"></script>

</body>

</html>
```

### word

`word.html`词云。使用`WordCloud`技术，生成一张带有出现频率较高的词汇的图片。原图片是白底的树，根据它来生成词汇树。

`word.html`源码如下：

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta content="width=device-width, initial-scale=1.0" name="viewport">

  <title>豆瓣top250数据可视化</title>
  <meta content="" name="descriptison">
  <meta content="" name="keywords">

  <!-- Favicons -->
  <link href="static/assets/img/favicon.png" rel="icon">
  <link href="static/assets/img/apple-touch-icon.png" rel="apple-touch-icon">

  <!-- Google Fonts -->
  <link href="https://fonts.googleapis.com/css?family=Open+Sans:300,300i,400,400i,600,600i,700,700i|Raleway:300,300i,400,400i,600,600i,700,700i,900" rel="stylesheet">

  <!-- Vendor CSS Files -->
  <link href="static/assets/vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">
  <link href="static/assets/vendor/icofont/icofont.min.css" rel="stylesheet">
  <link href="static/assets/vendor/boxicons/css/boxicons.min.css" rel="stylesheet">
  <link href="static/assets/vendor/animate.css/animate.min.css" rel="stylesheet">
  <link href="static/assets/vendor/venobox/venobox.css" rel="stylesheet">
  <link href="static/assets/vendor/aos/aos.css" rel="stylesheet">

  <!-- Template Main CSS File -->
  <link href="static/assets/css/style.css" rel="stylesheet">

</head>

<body>

  <!-- ======= Header ======= -->
  <header id="header">
    <div class="container">

      <div class="logo float-left">
        <h1 class="text-light"><a href="index"><span>豆瓣电影</span></a></h1>
        <!-- Uncomment below if you prefer to use an image logo -->
        <!-- <a href="temp.html"><img src="static/assets/img/logo.png" alt="" class="img-fluid"></a>-->
      </div>

      <nav class="nav-menu float-right d-none d-lg-block">
        <ul>
          <li class="active"><a href="/index">首页 <i class="la la-angle-down"></i></a></li>
          <li><a href="/movie">电影</a></li>
          <li><a href="/score">评分</a></li>
          <li><a href="/word">词云</a></li>
          <li><a href="/team">团队</a></li>

        </ul>
      </nav><!-- .nav-menu -->

    </div>
  </header><!-- End Header -->

            <!-- ======= About Us Section ======= -->
    <section id="about" class="about">
      <div class="container">

        <div class="row no-gutters">
          <div class="col-lg-6 video-box">
            <img src="static/assets/img/word.png" class="img-fluid" alt="">
          </div>

          <div class="col-lg-6 d-flex flex-column justify-content-center about-content">

            <div class="section-title">
              <h2>词频统计</h2>
              <p>根据250部电影的一句话描述，提炼出词云树，可以让我们更加清晰的了解人们对于经典电影的理解</p>
            </div>



            <div class="icon-box" data-aos="fade-up" data-aos-delay="100">
              <div class="icon"><i class="bx bx-gift"></i></div>
              <h4 class="title"><a href="">关于电影</a></h4>
              <p class="description">不知道你从中领悟到了什么</p>
            </div>

          </div>
        </div>

      </div>
    </section><!-- End About Us Section -->




  <a href="#" class="back-to-top"><i class="icofont-simple-up"></i></a>

  <!-- Vendor JS Files -->
  <script src="static/assets/vendor/jquery/jquery.min.js"></script>
  <script src="static/assets/vendor/bootstrap/js/bootstrap.bundle.min.js"></script>
  <script src="static/assets/vendor/jquery.easing/jquery.easing.min.js"></script>
  <script src="static/assets/vendor/php-email-form/validate.js"></script>
  <script src="static/assets/vendor/jquery-sticky/jquery.sticky.js"></script>
  <script src="static/assets/vendor/venobox/venobox.min.js"></script>
  <script src="static/assets/vendor/waypoints/jquery.waypoints.min.js"></script>
  <script src="static/assets/vendor/counterup/counterup.min.js"></script>
  <script src="static/assets/vendor/isotope-layout/isotope.pkgd.min.js"></script>
  <script src="static/assets/vendor/aos/aos.js"></script>

  <!-- Template Main JS File -->
  <script src="static/assets/js/main.js"></script>

</body>

</html>
```

### team

`team.html`团队。介绍团队成员。

`team.html`源码如下：

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta content="width=device-width, initial-scale=1.0" name="viewport">

  <title>豆瓣top250数据可视化</title>
  <meta content="" name="descriptison">
  <meta content="" name="keywords">

  <!-- Favicons -->
  <link href="static/assets/img/favicon.png" rel="icon">
  <link href="static/assets/img/apple-touch-icon.png" rel="apple-touch-icon">

  <!-- Google Fonts -->
  <link href="https://fonts.googleapis.com/css?family=Open+Sans:300,300i,400,400i,600,600i,700,700i|Raleway:300,300i,400,400i,600,600i,700,700i,900" rel="stylesheet">

  <!-- Vendor CSS Files -->
  <link href="static/assets/vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">
  <link href="static/assets/vendor/icofont/icofont.min.css" rel="stylesheet">
  <link href="static/assets/vendor/boxicons/css/boxicons.min.css" rel="stylesheet">
  <link href="static/assets/vendor/animate.css/animate.min.css" rel="stylesheet">
  <link href="static/assets/vendor/venobox/venobox.css" rel="stylesheet">
  <link href="static/assets/vendor/aos/aos.css" rel="stylesheet">

  <!-- Template Main CSS File -->
  <link href="static/assets/css/style.css" rel="stylesheet">

</head>

<body>

  <!-- ======= Top Bar ======= -->


  <!-- ======= Header ======= -->
  <header id="header">
    <div class="container">

      <div class="logo float-left">
        <h1 class="text-light"><a href="index"><span>豆瓣电影</span></a></h1>
        <!-- Uncomment below if you prefer to use an image logo -->
        <!-- <a href="temp.html"><img src="static/assets/img/logo.png" alt="" class="img-fluid"></a>-->
      </div>

      <nav class="nav-menu float-right d-none d-lg-block">
        <ul>
          <li class="active"><a href="/index">首页 <i class="la la-angle-down"></i></a></li>
          <li><a href="/movie">电影</a></li>
          <li><a href="/score">评分</a></li>
          <li><a href="/word">词云</a></li>
          <li><a href="/team">团队</a></li>

        </ul>
      </nav><!-- .nav-menu -->

    </div>
  </header><!-- End Header -->

    <!-- ======= Our Team Section ======= -->
    <section id="team" class="team">
      <div class="container">

        <div class="section-title">
          <h2>Our Team</h2>
          <p>Magnam dolores commodi suscipit. Necessitatibus eius consequatur ex aliquid fuga eum quidem.</p>
        </div>

        <div class="row">

          <div class="col-xl-3 col-lg-4 col-md-6" data-aos="fade-up">
            <div class="member">
              <div class="pic"><img src="static/assets/img/team/team-1.jpg" class="img-fluid" alt=""></div>
              <div class="member-info">
                <h4>Walter White</h4>
                <span>Chief Executive Officer</span>
                <div class="social">
                  <a href=""><i class="icofont-twitter"></i></a>
                  <a href=""><i class="icofont-facebook"></i></a>
                  <a href=""><i class="icofont-instagram"></i></a>
                  <a href=""><i class="icofont-linkedin"></i></a>
                </div>
              </div>
            </div>
          </div>

          <div class="col-xl-3 col-lg-4 col-md-6" data-aos="fade-up" data-aos-delay="100">
            <div class="member">
              <div class="pic"><img src="static/assets/img/team/team-2.jpg" class="img-fluid" alt=""></div>
              <div class="member-info">
                <h4>Sarah Jhonson</h4>
                <span>Product Manager</span>
                <div class="social">
                  <a href=""><i class="icofont-twitter"></i></a>
                  <a href=""><i class="icofont-facebook"></i></a>
                  <a href=""><i class="icofont-instagram"></i></a>
                  <a href=""><i class="icofont-linkedin"></i></a>
                </div>
              </div>
            </div>
          </div>

          <div class="col-xl-3 col-lg-4 col-md-6" data-aos="fade-up" data-aos-delay="200">
            <div class="member">
              <div class="pic"><img src="static/assets/img/team/team-3.jpg" class="img-fluid" alt=""></div>
              <div class="member-info">
                <h4>William Anderson</h4>
                <span>CTO</span>
                <div class="social">
                  <a href=""><i class="icofont-twitter"></i></a>
                  <a href=""><i class="icofont-facebook"></i></a>
                  <a href=""><i class="icofont-instagram"></i></a>
                  <a href=""><i class="icofont-linkedin"></i></a>
                </div>
              </div>
            </div>
          </div>

          <div class="col-xl-3 col-lg-4 col-md-6" data-aos="fade-up" data-aos-delay="300">
            <div class="member">
              <div class="pic"><img src="static/assets/img/team/team-4.jpg" class="img-fluid" alt=""></div>
              <div class="member-info">
                <h4>Amanda Jepson</h4>
                <span>Accountant</span>
                <div class="social">
                  <a href=""><i class="icofont-twitter"></i></a>
                  <a href=""><i class="icofont-facebook"></i></a>
                  <a href=""><i class="icofont-instagram"></i></a>
                  <a href=""><i class="icofont-linkedin"></i></a>
                </div>
              </div>
            </div>
          </div>

        </div>

      </div>
    </section><!-- End Our Team Section -->

  <main id="main">



<div class="tlinks">Collect from <a href="http://www.cssmoban.com/"  title="网站模板">网站模板</a></div>
      

  </main><!-- End #main -->



  <a href="#" class="back-to-top"><i class="icofont-simple-up"></i></a>

  <!-- Vendor JS Files -->
  <script src="static/assets/vendor/jquery/jquery.min.js"></script>
  <script src="static/assets/vendor/bootstrap/js/bootstrap.bundle.min.js"></script>
  <script src="static/assets/vendor/jquery.easing/jquery.easing.min.js"></script>
  <script src="static/assets/vendor/php-email-form/validate.js"></script>
  <script src="static/assets/vendor/jquery-sticky/jquery.sticky.js"></script>
  <script src="static/assets/vendor/venobox/venobox.min.js"></script>
  <script src="static/assets/vendor/waypoints/jquery.waypoints.min.js"></script>
  <script src="static/assets/vendor/counterup/counterup.min.js"></script>
  <script src="static/assets/vendor/isotope-layout/isotope.pkgd.min.js"></script>
  <script src="static/assets/vendor/aos/aos.js"></script>

  <!-- Template Main JS File -->
  <script src="static/assets/js/main.js"></script>

</body>

</html>
```

### 关于Echarts

使用前先下载源码

[地址](https://github.com/apache/echarts/tree/master/dist/echarts.min.js)

只需要这一个文件（echarts.min.js）

将文件(echarts.min.js)复制到项目test目录内

Echarts官方网站: https://echarts.apache.org/examples/zh/index.html#chart-type-line



可以根据官方示例更改代码实现想要的结果，最后可以下载示例。

示例网址[https://echarts.apache.org/examples/zh/editor.html?c=bar-background]

下载后将代码复制一份到`score.html`中。

### 关于wordcloud

在项目根目录内创建文件`testCloud.py`文件，实现词云页面所需要的词云树。

需要安装几个第三方的库

`jieba`:	分词

`matplotlib`：	绘图、数据可视化

`wordcloud`：词云

`numpy`：矩阵运算

`sqlite3`：数据库

`PIL`：图形处理库

代码如下：

```python
from tkinter import Image
import jieba # 分词
from matplotlib import pyplot as plt # 绘图，数据可视化
from wordcloud import WordCloud # 词云
import numpy as np # 矩阵运算
import sqlite3 # 数据库
from PIL import Image

# 准备词云所需的文字（词）
con = sqlite3.connect('douban_flask\movie.db')
cur = con.cursor()
sql = 'select instroduction from movie250'
data = cur.execute(sql)
text = ""
for item in data:
    text = text + item[0]
    # print(item[0])
# print(text)
cur.close()
con.close()

# 分词
cut = jieba.cut(text)
string = ' '.join(cut)
print(len(string))

img = Image.open(r'D:\code2022\auto-work\douban_flask\static\assets\img\tree.png') # 打开遮罩图片
img_array = np.array(img)
wc = WordCloud(
    background_color = 'white',
    mask = img_array,
    font_path = "msyh.ttc" # C:\Windows\Fonts 字体所在位置。
)
wc.generate_from_text(string)

# 绘制图片

fig = plt.figure(1)
plt.imshow(wc)
plt.axis('off') # 是否显示坐标轴

# plt.show() # 显示生成的词云图片

# 输出词云图片到文件
plt.savefig(r'D:\code2022\auto-work\douban_flask\static\assets\img\word.png',dpi=500)
```




