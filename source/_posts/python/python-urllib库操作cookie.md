---
title: python-urllib库操作cookie
comments: true
toc: true
date: 2018-07-27 11:51:21
updated: 
tags:
categories:
- python
banner:
---
以下基于python3的urllib库。爬去“携程”网为例子。
## 请求网站，接收cookie，并保存到文件
```python 
import urllib.request
import http.cookiejar
import os

cookie_file = os.path.join(os.path.dirname(__file__), 'cookie.txt')     # 将要保存cookie的路径。非必须如果你不需要保存cookie到文件中。
cookie = http.cookiejar.MozillaCookieJar()   # 生成cookie对象。文档里有写不需了解细节，用下面的HTTPCookieProcessor即可。不同系统下保存的cookie有所区别。
handler = urllib.request.HTTPCookieProcessor(cookie)    # 生成操作handler
opener = urllib.request.build_opener(handler)       # 自定义handler注册到新的request opener中
# response = opener.open('http://flights.ctrip.com/')   
request = urllib.request.Request('http://flights.ctrip.com/', headers={"Connection": "keep-alive"})     # 构造请求对象
response = opener.open(request)     # 请求
cookie_jar.save(filename=cookie_file, ignore_discard=True, ignore_expires=True)     # 保存cookie到文件

for item in cookie_jar:
    print('{}={}'.format(item.name, item.value))
```
发现打印出的cookie少于浏览器中的，因为浏览器中还有js的请求,暂时不用在意
![](1.png)
可以在脚本同级的目录看到生成的文件，里面的cookie的详细记录。
## 从文件读取cookie
读取上个例子中的cookie键值，再请求网页。适用场景，有些网站有安全措施，需要先获取session_id 、token等cookie值再去请求网页数据。
```python
import urllib.request
import http.cookiejar
import os

print(__file__, __name__)
cookie_file = os.path.join(os.path.dirname(__file__), 'cookie.txt')
cookie = http.cookiejar.MozillaCookieJar()
cookie.load(filename=cookie_file, ignore_discard=True, ignore_expires=True)
handler = urllib.request.HTTPCookieProcessor(cookie)
opener = urllib.request.build_opener(handler)
request = urllib.request.Request('http://flights.ctrip.com/')
response = opener.open(request)
html = response.read().decode('gbk')  # gb2312<gb18030<gbk
print(html)
```

## license
转自 [CSDN-Huang-ZC的博客](https://blog.csdn.net/kk185800961/article/details/80644479)
