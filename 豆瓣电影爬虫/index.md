# 豆瓣电影数据分析


## 爬虫分析步骤

#### 准备工作

#### 获取数据

#### 解析内容

#### 保存数据



### url分析

页面包括250条电影数据，分10页，每页25条。每页的url的不同之处：最后的数值= （页数-1）*25

```python
# 第一页第一条
https://movie.douban.com/top250?start=0
```

服务器通过headers鉴定客户端信息。

### 保存到excel表格中，代码如下

注意：如果`ip`被限制可以带`cookie`请求，这样服务器会认为是同一个客户端发出的请求。

或者使用代理IP来访问。

代理IP[网站](https://free.kuaidaili.com/free/)

```python
from bs4 import BeautifulSoup # 网页解析，获取数据
import re # 正则表达式，进行文字匹配
import urllib.request, urllib.error # 指定url，获取网页数据
from urllib.error import URLError
import xlwt # 进行execl操作
import sqlite3 # 进行sqlite 数据库操作
from fake_useragent import UserAgent # 引入动态随机UserAgent
from urllib.request import build_opener, ProxyHandler # 反反爬，使用代理IP，防止被服务器封掉。


def main():
    baseurl = "https://movie.douban.com/top250?start="
    # 1.爬取网页
    datalist = getData(baseurl)
    savepath = ".\\豆瓣电影top250.xls"
    # 3.保存数据
    saveData(datalist,savepath)

    # askURL("https://movie.douban.com/top250?start=0")

# 影片详情的规则
findLink = re.compile(r'<a href="(.*?)">')     # 创建正则表达式对象，表示规则（字符串的模式）
# 影片图片
findImgSrc = re.compile(r'<img.*src="(.*?)"',re.S)  # re.S 让换行符包含在字符中      
# 影片片名
findTitle = re.compile(r'<span class="title">(.*)</span>')
# 影片评分
findRating = re.compile(r'<span class="rating_num" property="v:average">(.*)</span>')
# 找到评价人数
findJudge = re.compile(r'<span>(\d*)人评价</span>')
# 找到概况
findInq = re.compile(r'<span class="inq">(.*)</span>')
# 找到影片的相关内容
findBd =re.compile(r'<p class="">(.*?)</p>',re.S)


# 爬取网页
def getData(baseurl):
    datalist = []
    for i in range(0,10):       # 调用获取页面信息的函数,10次,正好10页内容250条。
        url = baseurl + str(i*25)
        askURL(url)
        html = askURL(url)      # 保存获取到的网页源码

        # 2.逐一解析数据
        soup = BeautifulSoup(html,"html.parser")
        for item in soup.find_all("div",class_="item"):    #查找符合要求的字符串形成列表。
            # print(item) # 测试查看电影item全部信息
            data = []   #保存一部电影的所有信息
            item = str(item)


            # 影片详情的链接
            link = re.findall(findLink,item)[0]     #re库用来通过正则表达式查找指定的字符串
            # print(link)
            data.append(link)                   # 添加链接
            imgSrc= re.findall(findImgSrc,item)[0]  #添加图片
            data.append(imgSrc)
            titles = re.findall(findTitle,item)        # 片名可能只有一个中文名，没有外国名
            if (len(titles) == 2):
                ctitle = titles[0]          # 添加中文名
                data.append(ctitle)
                otitle = titles[1].replace("/","")      # 去掉无关的符号
                data.append(otitle)                 # 添加外国名
            else:
                data.append(titles[0])
                data.append(' ')        # 外国名字留空
            
            rating = re.findall(findRating,item)[0]     # 添加评分
            data.append(rating)

            judgeNim = re.findall(findJudge,item)[0]
            data.append(judgeNim)           # 添加评价人数

            inq = re.findall(findInq,item)
            if len(inq) != 0:
                inq = inq[0].replace("。","")       # 去掉句号
                data.append(inq)                # 添加概述
            else:
                data.append(" ")        # 留空

            bd = re.findall(findBd,item)[0]
            bd = re.sub('<br(\s+)?/>(\s+)?'," ",bd)     # 去掉<br/>
            bd = re.sub('/'," ",bd)     # 替换/
            data.append(bd.strip())     # 去掉前后的空格

            datalist.append(data)       # 把处理好的一部分电影信息放入datalist
    # print(datalist)
    return datalist


# 得到一个指定的URL的网页内容
def askURL(url):
    head = {        # 模拟浏览器头部信息，向豆瓣服务器发送消息
        "User-Agent": UserAgent().random
    }
    request = urllib.request.Request(url,headers=head)
    handler = ProxyHandler({"http":"58.20.184.187:9091"}) # 使用代理IP请求资源
    opener = build_opener(handler)
    # 用户代理表示告诉豆瓣服务器，我们是什么类型的机器、浏览器(本质上是告诉浏览器，我们可以接收什么水平的文件内容)
    html = ""
    try:
        resp = opener.open(request)
        html = resp.read().decode("utf-8")
        # print(html)
    except URLError as e:
        if hasattr(e,"code"):
            print(e.code)
        if hasattr(e,"reason"):
            print(e.reason)
    return html

    
     
# 3.保存数据
def saveData(datalist,savepath):
    print("save....")
    book = xlwt.Workbook(encoding='utf-8') # 创建workbook对象
    sheet = book.add_sheet('豆瓣电影Top250',cell_overwrite_ok=True)    # 创建工作表
    col = ("电影详情链接","图片链接","影片中文名","影片外国名","评分","评价数","概况","相关信息")
    for i in range(0,8):
        sheet.write(0,i,col[i])     # 列名
    
    for i in range(0,250):
        print("第%d条" %i)
        data = datalist[i]
        for j in range(0,8):
            sheet.write(i+1,j,data[j])      # 数据
        
    book.save(savepath)    # 保存数据表


if __name__ == "__main__": # 当程序执行时。程序入口，控制函数调用顺序。
# 调用函数
    main()
    print("爬取完毕！")
```

### 保存到数据库中，代码如下

```python
from bs4 import BeautifulSoup # 网页解析，获取数据
import re # 正则表达式，进行文字匹配
import urllib.request, urllib.error # 指定url，获取网页数据
from urllib.error import URLError
import xlwt # 进行execl操作
import sqlite3 # 进行sqlite 数据库操作
from fake_useragent import UserAgent # 引入动态随机UserAgent
from urllib.request import build_opener, ProxyHandler # 反反爬，使用代理IP，防止被服务器封掉。


def main():
    baseurl = "https://movie.douban.com/top250?start="
    # 1.爬取网页
    datalist = getData(baseurl)
    dbpath = "movie.db"
    # 3.保存数据
    saveData2DB(datalist, dbpath)

    # askURL("https://movie.douban.com/top250?start=0")

# 影片详情的规则
findLink = re.compile(r'<a href="(.*?)">')     # 创建正则表达式对象，表示规则（字符串的模式）
# 影片图片
findImgSrc = re.compile(r'<img.*src="(.*?)"',re.S)  # re.S 让换行符包含在字符中      
# 影片片名
findTitle = re.compile(r'<span class="title">(.*)</span>')
# 影片评分
findRating = re.compile(r'<span class="rating_num" property="v:average">(.*)</span>')
# 找到评价人数
findJudge = re.compile(r'<span>(\d*)人评价</span>')
# 找到概况
findInq = re.compile(r'<span class="inq">(.*)</span>')
# 找到影片的相关内容
findBd =re.compile(r'<p class="">(.*?)</p>',re.S)


# 爬取网页
def getData(baseurl):
    datalist = []
    for i in range(0,10):       # 调用获取页面信息的函数,10次,正好10页内容250条。
        url = baseurl + str(i*25)
        askURL(url)
        html = askURL(url)      # 保存获取到的网页源码

        # 2.逐一解析数据
        soup = BeautifulSoup(html,"html.parser")
        for item in soup.find_all("div",class_="item"):    #查找符合要求的字符串形成列表。
            # print(item) # 测试查看电影item全部信息
            data = []   #保存一部电影的所有信息
            item = str(item)


            # 影片详情的链接
            link = re.findall(findLink,item)[0]     #re库用来通过正则表达式查找指定的字符串
            # print(link)
            data.append(link)                   # 添加链接
            imgSrc= re.findall(findImgSrc,item)[0]  #添加图片
            data.append(imgSrc)
            titles = re.findall(findTitle,item)        # 片名可能只有一个中文名，没有外国名
            if (len(titles) == 2):
                ctitle = titles[0]          # 添加中文名
                data.append(ctitle)
                otitle = titles[1].replace("/","")      # 去掉无关的符号
                data.append(otitle)                 # 添加外国名
            else:
                data.append(titles[0])
                data.append(' ')        # 外国名字留空
            
            rating = re.findall(findRating,item)[0]     # 添加评分
            data.append(rating)

            judgeNim = re.findall(findJudge,item)[0]
            data.append(judgeNim)           # 添加评价人数

            inq = re.findall(findInq,item)
            if len(inq) != 0:
                inq = inq[0].replace("。","")       # 去掉句号
                data.append(inq)                # 添加概述
            else:
                data.append(" ")        # 留空

            bd = re.findall(findBd,item)[0]
            bd = re.sub('<br(\s+)?/>(\s+)?'," ",bd)     # 去掉<br/>
            bd = re.sub('/'," ",bd)     # 替换/
            data.append(bd.strip())     # 去掉前后的空格

            datalist.append(data)       # 把处理好的一部分电影信息放入datalist
    # print(datalist)
    return datalist


# 得到一个指定的URL的网页内容
def askURL(url):
    head = {        # 模拟浏览器头部信息，向豆瓣服务器发送消息
        "User-Agent": UserAgent().random
    }
    request = urllib.request.Request(url,headers=head)
    handler = ProxyHandler({"http":"58.20.184.187:9091"}) # 使用代理IP请求资源
    opener = build_opener(handler)
    # 用户代理表示告诉豆瓣服务器，我们是什么类型的机器、浏览器(本质上是告诉浏览器，我们可以接收什么水平的文件内容)
    html = ""
    try:
        resp = opener.open(request)
        html = resp.read().decode("utf-8")
        # print(html)
    except URLError as e:
        if hasattr(e,"code"):
            print(e.code)
        if hasattr(e,"reason"):
            print(e.reason)
    return html

    
     
# 3.保存数据
def saveData2DB(datalist,dbpath):
    init_db(dbpath)
    conn = sqlite3.connect(dbpath)
    cur = conn.cursor()

    for data in datalist:
        for index in range(len(data)):
            if index == 4 or index == 5:
                continue
            data[index] = '"'+data[index]+'"'
        sql = '''
                insert into movie250(
                info_link,pic_link,cname,ename,score,rated,instroduction,info
                )
                values(%s)'''%",".join(data)
        # print(sql) # 打印插入语句（测试用）
        cur.execute(sql)
        conn.commit()
    cur.close()
    conn.close()



def init_db(dbpath):
    sql = '''
        create table movie250
        (
        id integer primary key autoincrement,
        info_link text,
        pic_link text,
        cname varchar ,
        ename varchar ,
        score numeric ,
        rated numeric ,
        instroduction text ,
        info text
        )
    '''     # 创建数据表
    conn = sqlite3.connect(dbpath)
    cursor = conn.cursor()
    cursor.execute(sql)
    conn.commit()
    conn.close()

if __name__ == "__main__":  # 当程序执行时。程序入口，控制函数调用顺序。
# 调用函数
    main()
    # init_db("movietest.db") # 测试用，生成测试库。
    print("爬取完毕！")
```


