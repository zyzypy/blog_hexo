---
title: 爬虫代理简单例子
comments: true
toc: false
date: 2018-07-25 14:06:22
updated:
tags:
categories:
- python
banner:
---
## 场景
同一ip频繁访问一个网站，往往被网站服务器认为恶意访问而封ip禁止访问。爬虫项目可以通过切换不同ip对策服务器的反爬限制。
## 准备ip代理
访问 [xicai代理](http://www.xicidaili.com/nn/)
![](1.png)
选一个速度好的记住 IP地址、端口、类型。没仔细研究过ip代理协议，看上去连接ip代理简单，不需要用户名密码。
## 简单代码示例
### 基于py3 urllib库
```python
from urllib import request

if __name__ == "__main__":
    #访问网址
    url = 'https://www.baidu.com/s?wd=ip'
    #这是代理IP
    proxy = {'https':'115.28.100.127:80'}
    #创建ProxyHandler
    proxy_support = request.ProxyHandler(proxy)
    #创建Opener
    opener = request.build_opener(proxy_support)
    #添加User Angent
    opener.addheaders = [('User-Agent','Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36')]
    #安装OPener
    request.install_opener(opener)
    #使用自己安装好的Opener
    response = request.urlopen(url)
    #读取相应信息并解码
    html = response.read().decode("utf-8")
    # html = response.read().decode("gb2312")
    #打印信息
    print(html)
```
1. 找一个测试ip的网址。我暂时不知道什么好的，网上的一篇教程里网址失效了，还有的要付费api。所以我用的百度，想法来源
![](2.png)
url很长，但测试发现只要有wd=ip这个参数就够了
2. proxy = {'https':'115.28.100.127:80'} 把之前网站上看的免费代理信息填上去
3. request.build_opener(proxy_support)设置代理
4. request.install_opener(opener)方法覆盖了urllib本身的urlopen()方法。如果只是想临时使用一下，可以使用opener.open(url)，这样就不会对程序默认的urlopen有影响。
5. 照常请求，收到结果，我在pycharm的终端信息里 ctrl+F查找到信息，你可以用正则匹配出来。
![](3.png)
ip已经变成代理的ip。
### 基于requests库
todo
### py2 urllib urllib2库
py2的urllib、urlib2库与py3的urllib库相仿，请自行查找库的不同书写代码或查找相关示例代码，这里不再书写。

## 更复杂的例子
结合列表、api、可以做代理池。
todo

## license
参考文章[CSDN-Jack-Cui的博客](https://blog.csdn.net/c406495762/article/details/60137956) 并在其基础上修改。