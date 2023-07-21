# urllib库的基本使用


### 网页各种测试

```python
http://httpbin.org
```

#### 获取一个get请求

```python
import urllib.request
res = urllib.request.urlopen("http://www.baidu.com")
print(res.read().decode('utf-8')) # 对获取到的网页源码进行utf-8解码
```

#### 获取一个post请求.（模拟用户登录）

```python
import urllib.parse # 引入解析库
data  = bytes(urllib.parse.urlencode({"hello":"world"}),encoding='utf-8')
res1 = urllib.request.urlopen("http://httpbin.org/post",data=data)
print(res1.read().decode("utf-8"))
```

#### 超时处理

```python
try:
    res1 = urllib.request.urlopen("http://httpbin.org/get",timeout=0.01)
    print(res1.read().decode('utf-8'))
except urllib.error.URLError as e:
    print("time out!")
```

#### 获取基本信息

```python
res2 = urllib.request.urlopen("http://baidu.com")
print(res2.status) # 获取状态码
print(res2.getheaders()) # 获取头部信息
print(res2.getheader("Server")) # Apache
```

#### 直接请求豆瓣网址

 ```python
url = "https://douban.com"
req = urllib.request.Request(url=url)
response = urllib.request.urlopen(req)
print(response.read().decode("utf-8"))
 ```

#### 报错信息如下：（说明豆瓣已经识别到是爬虫的请求）

```python
raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 418
```

如何伪装成浏览器来请求呢？这时候需要在请求头中添加`user-agent`。`user-agent`如何获取？浏览器`F12`-》`NETWORK`->点击左侧带有`get`请求的网址，右侧拉到最下边复制`user-agent`的值。

#### 构建代码后再次请求，收到返回的数据了。

```python
url = "https://douban.com"
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.0.0 Safari/537.36"
} 
req = urllib.request.Request(url=url,headers=headers)
response = urllib.request.urlopen(req)
print(response.read().decode("utf-8"))
```

## UA伪装

#### 方式一

将常见的UserAgent用列表保存起来

```python
ua_list = [
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:91.0) Gecko/20100101 Firefox/91.0",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.0)",
    "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.6; rv:2.0.1) Gecko/20100101 Firefox/4.0.1",
    "Mozilla/5.0 (Windows NT 6.1; rv:2.0.1) Gecko/20100101 Firefox/4.0.1",
    "Opera/9.80 (Macintosh; Intel Mac OS X 10.6.8; U; en) Presto/2.8.131 Version/11.11",
    "Opera/9.80 (Windows NT 6.1; U; en) Presto/2.8.131 Version/11.11",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_7_0) AppleWebKit/535.11 (KHTML, like Gecko) Chrome/17.0.963.56 Safari/535.11",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; Maxthon 2.0)",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; TencentTraveler 4.0)",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1)",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; The World)",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; Trident/4.0; SE 2.X MetaSr 1.0; SE 2.X MetaSr 1.0; .NET CLR 2.0.50727; SE 2.X MetaSr 1.0)",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; 360SE)",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; Avant Browser)",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1)",
    "Mozilla/5.0 (iPhone; U; CPU iPhone OS 4_3_3 like Mac OS X; en-us) AppleWebKit/533.17.9 (KHTML, like Gecko) Version/5.0.2 Mobile/8J2 Safari/6533.18.5",
    "Mozilla/5.0 (iPod; U; CPU iPhone OS 4_3_3 like Mac OS X; en-us) AppleWebKit/533.17.9 (KHTML, like Gecko) Version/5.0.2 Mobile/8J2 Safari/6533.18.5",
    "Mozilla/5.0 (iPad; U; CPU OS 4_3_3 like Mac OS X; en-us) AppleWebKit/533.17.9 (KHTML, like Gecko) Version/5.0.2 Mobile/8J2 Safari/6533.18.5",
    "Mozilla/5.0 (Linux; U; Android 2.3.7; en-us; Nexus One Build/FRF91) AppleWebKit/533.1 (KHTML, like Gecko) Version/4.0 Mobile Safari/533.1",
    "MQQBrowser/26 Mozilla/5.0 (Linux; U; Android 2.3.7; zh-cn; MB200 Build/GRJ22; CyanogenMod-7) AppleWebKit/533.1 (KHTML, like Gecko) Version/4.0 Mobile Safari/533.1",
    "Opera/9.80 (Android 2.3.4; Linux; Opera Mobi/build-1107180945; U; en-GB) Presto/2.8.149 Version/11.10",
    "Mozilla/5.0 (Linux; U; Android 3.0; en-us; Xoom Build/HRI39) AppleWebKit/534.13 (KHTML, like Gecko) Version/4.0 Safari/534.13",
    "Mozilla/5.0 (BlackBerry; U; BlackBerry 9800; en) AppleWebKit/534.1+ (KHTML, like Gecko) Version/6.0.0.337 Mobile Safari/534.1+",
    "Mozilla/5.0 (hp-tablet; Linux; hpwOS/3.0.0; U; en-US) AppleWebKit/534.6 (KHTML, like Gecko) wOSBrowser/233.70 Safari/534.6 TouchPad/1.0",
    "Mozilla/5.0 (SymbianOS/9.4; Series60/5.0 NokiaN97-1/20.0.019; Profile/MIDP-2.1 Configuration/CLDC-1.1) AppleWebKit/525 (KHTML, like Gecko) BrowserNG/7.1.18124",
    "Mozilla/5.0 (compatible; MSIE 9.0; Windows Phone OS 7.5; Trident/5.0; IEMobile/9.0; HTC; Titan)"
]
```

代码演示

```python
import random
from urllib.request import urlopen, Request

url = "http://httpbin.org/get"

ua_list = [
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:91.0) Gecko/20100101 Firefox/91.0",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.0)",
    "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.6; rv:2.0.1) Gecko/20100101 Firefox/4.0.1",
    "Mozilla/5.0 (Windows NT 6.1; rv:2.0.1) Gecko/20100101 Firefox/4.0.1",
    "Opera/9.80 (Macintosh; Intel Mac OS X 10.6.8; U; en) Presto/2.8.131 Version/11.11",
    "Opera/9.80 (Windows NT 6.1; U; en) Presto/2.8.131 Version/11.11",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_7_0) AppleWebKit/535.11 (KHTML, like Gecko) Chrome/17.0.963.56 Safari/535.11",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; Maxthon 2.0)",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; TencentTraveler 4.0)",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1)",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; The World)",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; Trident/4.0; SE 2.X MetaSr 1.0; SE 2.X MetaSr 1.0; .NET CLR 2.0.50727; SE 2.X MetaSr 1.0)",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; 360SE)",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; Avant Browser)",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1)",
    "Mozilla/5.0 (iPhone; U; CPU iPhone OS 4_3_3 like Mac OS X; en-us) AppleWebKit/533.17.9 (KHTML, like Gecko) Version/5.0.2 Mobile/8J2 Safari/6533.18.5",
    "Mozilla/5.0 (iPod; U; CPU iPhone OS 4_3_3 like Mac OS X; en-us) AppleWebKit/533.17.9 (KHTML, like Gecko) Version/5.0.2 Mobile/8J2 Safari/6533.18.5",
    "Mozilla/5.0 (iPad; U; CPU OS 4_3_3 like Mac OS X; en-us) AppleWebKit/533.17.9 (KHTML, like Gecko) Version/5.0.2 Mobile/8J2 Safari/6533.18.5",
    "Mozilla/5.0 (Linux; U; Android 2.3.7; en-us; Nexus One Build/FRF91) AppleWebKit/533.1 (KHTML, like Gecko) Version/4.0 Mobile Safari/533.1",
    "MQQBrowser/26 Mozilla/5.0 (Linux; U; Android 2.3.7; zh-cn; MB200 Build/GRJ22; CyanogenMod-7) AppleWebKit/533.1 (KHTML, like Gecko) Version/4.0 Mobile Safari/533.1",
    "Opera/9.80 (Android 2.3.4; Linux; Opera Mobi/build-1107180945; U; en-GB) Presto/2.8.149 Version/11.10",
    "Mozilla/5.0 (Linux; U; Android 3.0; en-us; Xoom Build/HRI39) AppleWebKit/534.13 (KHTML, like Gecko) Version/4.0 Safari/534.13",
    "Mozilla/5.0 (BlackBerry; U; BlackBerry 9800; en) AppleWebKit/534.1+ (KHTML, like Gecko) Version/6.0.0.337 Mobile Safari/534.1+",
    "Mozilla/5.0 (hp-tablet; Linux; hpwOS/3.0.0; U; en-US) AppleWebKit/534.6 (KHTML, like Gecko) wOSBrowser/233.70 Safari/534.6 TouchPad/1.0",
    "Mozilla/5.0 (SymbianOS/9.4; Series60/5.0 NokiaN97-1/20.0.019; Profile/MIDP-2.1 Configuration/CLDC-1.1) AppleWebKit/525 (KHTML, like Gecko) BrowserNG/7.1.18124",
    "Mozilla/5.0 (compatible; MSIE 9.0; Windows Phone OS 7.5; Trident/5.0; IEMobile/9.0; HTC; Titan)"
]

# random.choice():随机从列表里取一个
headers = {"User-Agent": random.choice(ua_list)}
# 构造请求
req = Request(url, headers=headers)
# 发送请求
resp = urlopen(req)
# 相应内容
print(resp.read().decode('utf-8'))
```

内容输出

```python
{
  "args": {}, 
  "headers": {
    "Accept-Encoding": "identity", 
    "Host": "httpbin.org", 
    "User-Agent": "Mozilla/5.0 (BlackBerry; U; BlackBerry 9800; en) AppleWebKit/534.1+ (KHTML, like Gecko) Version/6.0.0.337 Mobile Safari/534.1+", 
    "X-Amzn-Trace-Id": "Root=1-62d7a54c-0a7c5b071d6f91e0471656ef"
  }, 
  "origin": "ip", 
  "url": "http://httpbin.org/get"
}

{
  "args": {},
  "headers": {
    "Accept-Encoding": "identity",
    "Host": "httpbin.org",
    "User-Agent": "Mozilla/5.0 (iPod; U; CPU iPhone OS 4_3_3 like Mac OS X; en-us) AppleWebKit/533.17.9 (KHTML, like Gecko) Version/5.0.2 Mobile/8J2 Safari/6533.18.5",
    "X-Amzn-Trace-Id": "Root=1-62d7a552-6c77190d0bef3bd93ddca46d"
  },
  "origin": "ip",
  "url": "http://httpbin.org/get"
}
```

#### **方式二**

准备工作：

```python
pip install fake_useragent
```

```python
# -*- coding:utf-8 -*-
from fake_useragent import UserAgent
from urllib.request import urlopen, Request

headers = {
    "User-Agent": UserAgent().random # 随机选取代理。
}

url = "http://httpbin.org/get"
# 构造请求
req = Request(url, headers=headers)
# 发送请求
resp = urlopen(req)
# 相应内容
print(resp.read().decode('utf-8'))
```

## 使用代理

### 常用的handler对象

- **HTTPHandler**: 普通的HTTP处理器
- **ProxyHandler**: 代理IP处理器
- **HTTPCookieProcessor**: cookie处理器

### 代码演示

#### HTTPHandler

```python
# -*- coding:utf-8 -*-
from fake_useragent import UserAgent
from urllib.request import Request, urlopen, build_opener, HTTPHandler

def download(url):

    headers = {
        "User-Agent": UserAgent().random
    }
    req = Request(url, headers=headers)
    # 创建http处理器对象,debuglevel打开调试
    handler = HTTPHandler(debuglevel=2)
    opener = build_opener(handler)
    resp = opener.open(req)
    print(resp.read().decode('utf-8'))

if __name__ == '__main__':
    url = "http://httpbin.org/get"
    download(url)
```

输出如下

```python
send: b'GET /get HTTP/1.1\r\nAccept-Encoding: identity\r\nHost: httpbin.org\r\nUser-Agent: Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.67 Safari/537.36\r\nConnection: close\r\n\r\n'
reply: 'HTTP/1.1 200 OK\r\n'
header: Date: Wed, 20 Jul 2022 08:06:06 GMT
header: Content-Type: application/json
header: Content-Length: 359
header: Connection: close
header: Server: gunicorn/19.9.0
header: Access-Control-Allow-Origin: *
header: Access-Control-Allow-Credentials: true
{
  "args": {}, 
  "headers": {
    "Accept-Encoding": "identity", 
    "Host": "httpbin.org", 
    "User-Agent": "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.67 Safari/537.36", 
    "X-Amzn-Trace-Id": "Root=1-62d7b76e-4c99f8712c1292a55b601fa4"
  }, 
  "origin": "访问的ip", 
  "url": "http://httpbin.org/get"
}
```

#### ProxyHandler

```python
# -*- coding:utf-8 -*-
from fake_useragent import UserAgent
from urllib.request import Request, build_opener, ProxyHandler

def proxy_handler(url):
    headers = {
        "User-Agent": UserAgent().random
    }
    req = Request(url, headers=headers)
    # ProxyHandler({"http": "ip:port"})
    handler = ProxyHandler({"http": "58.20.235.180:9091"})
    opener = build_opener(handler)
    resp = opener.open(req)
    # print(resp.info())
    print(resp.read().decode())


if __name__ == '__main__':
    url = "http://httpbin.org/get"
    proxy_handler(url)
```

输出如下

```python
{
  "args": {}, 
  "headers": {
    "Host": "httpbin.org", 
    "User-Agent": "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.93 Safari/537.36",
    "X-Amzn-Trace-Id": "Root=1-62d7b7ea-05098b4c34bab12d7cc0dfe7"
  },
  "origin": "58.20.235.180",
  "url": "http://httpbin.org/get"
}
```

发现使用了代理IP请求的网页。

登录cookie处理[见](https://www.jianshu.com/p/98b0334a6951?u_atoken=cf050b03-4426-4ae8-ae76-ec76a49811bb&u_asession=01F8tG1j1-6mUqPBfizMyXtOLgj1_YdpWLy6tLQTrMJKkPO4B8VGQufulZrcnb3kYsX0KNBwm7Lovlpxjd_P_q4JsKWYrT3W_NKPr8w6oU7K8-AbenNtsISwUd_aPGR9dhPpcarp92QKzyJKyYjREPlmBkFo3NEHBv0PZUm6pbxQU&u_asig=05Y_GO1vKbHGrEmwMy-8Bd_sFNaHiYK3j6noCMVAA70lD05yk2CFL3YOsWSQgXdUg3GG7jeXvY63IgguMHqMXco8jlTjdjUZ2-AEjBbKB3fRHgP8bB5H8JlYanu-2gJ6IT3At8RY4894XQDDjc4CC6hg10-lAg9OWVEp3GNxFS9sf9JS7q8ZD7Xtz2Ly-b0kmuyAKRFSVJkkdwVUnyHAIJzQRCyRu4UuJTb01FQsw4RUa7a4S_mrYVXff1xSzt_uoNWPRPQyB_SKrj-61LB_f61u3h9VXwMyh6PgyDIVSG1W-bbgCpyxFT_zAiQqv6S6quGVjuvW8Mbnw36tryIj0GRovjjV_Udp6mp6xfOvuIWAyvNX7IvsygEZUv24qXCsuLmWspDxyAEEo4kbsryBKb9Q&u_aref=k8mWOuZuai%2F2wl2ZQb1rFJPv6AU%3D)


