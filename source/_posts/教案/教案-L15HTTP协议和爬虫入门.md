## 学习爬虫所需知识
1. html、css、js基础
2. http协议
3. 请求与响应
4. 常用包 urllib， requests， 


## http基础
见 《图解http》截图

### 课下了解
http、ip报文结构
持久连接
更多的状态码

## 包
json urllib基本使用
### 课下xml
xml市场渐渐减少，课下了解
### py2 urlib、urlib2与py3 urllib包变化
https://blog.csdn.net/duxu24/article/details/77414298

## 实战：百度地图api  
1. 引题：计算机各系统各编程语言之间，实现各不相同，py的字典与java的map虽然看上去一样，但内部实现不同，不能互相理解。网络需要有统一的格式才能互相理解。
2. urllib包初步使用，接口 api的基本概念。
示例：请求百度地图搜索api
(查看谷歌控制台，中文被自动base64转码了)
ak：S2uzcu5og9j5h2tWPrBR9H0Fw8jiq9Wo
http://api.map.baidu.com/place/v2/search?query=ATM机&tag=银行&region=北京&output=json&ak=您的ak //GET请求
```
import urllib.request
import urllib.parse

import json
# http://api.map.baidu.com/place/v2/search?query=%E6%99%AF%E7%82%B9&region=%E9%83%91%E5%B7%9E&output=json&ak=S2uzcu5og9j5h2tWPrBR9H0Fw8jiq9Wo
ak='S2uzcu5og9j5h2tWPrBR9H0Fw8jiq9Wo'

url_api = 'http://api.map.baidu.com/place/v2/search?'
url_args = {'query': '景点', 'region': '郑州', 'output': 'json', 'ak': ak}
url_args = urllib.parse.urlencode(url_args)
url = url_api + url_args
print(url)

response = urllib.request.urlopen(url)
response_json = response.read().decode('utf-8')
response_dict = json.loads(response_json)
print(response_dict)
results = response_dict['results']
print(results)
for result in results:
    print('景点名：' + result['name'])
    print('地址：' + result['address'])
    print('-'*50)
```

练习一：查看获取ip地理位置文档并写出代码
http://lbsyun.baidu.com/index.php?title=webapi/ip-api
练习二： 已知url和ak，打印天气基本情况。
```
url = "http://api.map.baidu.com/telematics/v3/weather?location=郑州市&output=json&ak=TueGDhCvwI6fOrQnLM0qmXxY9N0OkOiQ&callback=?"
```

## 包和模块补充内容