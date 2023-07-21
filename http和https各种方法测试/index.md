# http和https各种方法测试



测试网站：

```bash
http://httpbin.org/
```

## http所有方法

### get

```python
import requests
def get_html_text(url):
    headers = {
        'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36'
    }
    r = requests.get(url, headers=headers)
    if r.status_code == 200:
        html = r.text
        print(html)
    else:
        print("访问失败")

url = 'http://httpbin.org/get'
get_html_text(url)
```

返回数据

```bash
{
  "args": {}, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Host": "httpbin.org", 
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36", 
    "X-Amzn-Trace-Id": "Root=1-62b26fdb-5f9089b418e89e7533cd64f7"
  }, 
  "origin": "124.64.235.140",
  "url": "http://httpbin.org/get"
}
```

### 带请求头和不带请求头访问区别

#### 不带请求头

```python
import requests
def get_html_text(url):
    r = requests.get(url)
    if r.status_code == 200:
        html = r.text
        print(html)
    else:
        print("访问失败")

url = 'http://httpbin.org/user-agent'
get_html_text(url)
```

结果

只显示原始请求，有些网站会拒绝访问。

```ba
{
  "user-agent": "python-requests/2.25.1"
}
```

#### 带请求头

```python
import requests
def get_html_text(url):
    headers = {
        'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36'
    }
    r = requests.get(url, headers=headers)
    if r.status_code == 200:
        html = r.text
        print(html)
    else:
        print("访问失败")

url = 'http://httpbin.org/user-agent'
get_html_text(url)
```

结果

可以伪装成任意设备。

```bash
{
  "user-agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36"
}
```

### cookies

请求头带cookies请求资源

```python
import requests

def auth_html(url):
    headers = {
        'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36',
        'cookies': 'stale_after=never; fake=fake_value; last_nonce=65ef22149cc1c4c4214bf968aab45271'
    }
    r = requests.get(url, headers=headers)
    if r.status_code == 200:
        html = r.text
        print(html, r.status_code)
    else:
        print("访问失败")

url = 'http://httpbin.org/get'
auth_html(url)
```

结果

```bash
{
  "args": {}, 
  "headers": {
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9", 
    "Accept-Encoding": "gzip, deflate", 
    "Accept-Language": "zh-CN,zh;q=0.9", 
    "Cookie": "stale_after=never; fake=fake_value; last_nonce=65ef22149cc1c4c4214bf968aab45271", 
    "Host": "httpbin.org", 
    "Upgrade-Insecure-Requests": "1", 
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36", 
    "X-Amzn-Trace-Id": "Root=1-62b28149-3ba69f2e27ccba90684da343"
  }, 
  "origin": "124.64.235.140", 
  "url": "http://httpbin.org/get"
}
```

### post

```python
import requests
def post_html_text(url):
    headers = {
        'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36'
    }
    r = requests.post(url, headers=headers)
    if r.status_code == 200:
        html = r.text
        print(html)
    else:
        print("访问失败")

url = 'http://httpbin.org/post'
get_html_text(url)
```

执行结果

```bash
{
  "args": {},  
  "data": "",  
  "files": {}, 
  "form": {},
  "headers": {
    "Accept": "*/*",
    "Accept-Encoding": "gzip, deflate",
    "Content-Length": "0",
    "Host": "httpbin.org",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36",
    "X-Amzn-Trace-Id": "Root=1-62b2706d-7f50d849381586d306230274"
  },
  "json": null,
  "origin": "124.64.235.140",
  "url": "http://httpbin.org/post"
}
```

如果想带数据post,可以加入参数，请求结果显示出来。

```python
import requests
def post_html_text(url):
    headers = {
        'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36'
    }
    data = 'hello world'
    params = {'name':'jack'}
    r = requests.post(url, headers=headers, data=data, params=params)
    if r.status_code == 200:
        html = r.text
        print(html)
    else:
        print("访问失败")

url = 'http://httpbin.org/post'
post_html_text(url)
```

请求结果

```bash
{
  "args": {
    "name": "jack"
  },
  "data": "hello world",
  "files": {},
  "form": {},
  "headers": {
    "Accept": "*/*",
    "Accept-Encoding": "gzip, deflate",
    "Content-Length": "11",
    "Host": "httpbin.org",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36",
    "X-Amzn-Trace-Id": "Root=1-62b27146-6d5aa1171e476ed97757a9a5"
  },
  "json": null,
  "origin": "124.64.235.140",
  "url": "http://httpbin.org/post?name=jack"
}
```

### auth认证

请求头中加入`authorization`，带着请求头去请求。

```bash
import requests

def auth_html(url):
    headers = {
        'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36',
        'authorization': 'Basic MTIzOjEyMw=='
    }
    data = 'hello world'
    params = {'name':'jack'}
    r = requests.get(url, headers=headers, data=data, params=params)
    if r.status_code == 200:
        html = r.text
        print(html)
    else:
        print("访问失败")
        
url = 'http://httpbin.org/basic-auth/123/123'
auth_html(url)
```

请求结果

```bash
{
  "authenticated": true,
  "user": "123"
}
```

`MTIzOjEyMw==`是`base64`加密后的。

解密

```bash
echo "MTIzOjEyMw==" | base64 -d
123:123
```

也可以直接浏览器测试认证功能。

```bash
http://httpbin.org/basic-auth/用户名/密码
```

浏览器f2 过滤无关元素

```bash
-.js -.jpg -.css -.gif -.png -.ico -.webp
```

### linux系统命令行请求

#### get请求

```bash
curl -X GET "http://httpbin.org/get" -H "accept: application/json" -H "user-agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36"
```

#### post请求

```bash
curl -X POST "http://httpbin.org/post" -H "accept: application/json"
```


