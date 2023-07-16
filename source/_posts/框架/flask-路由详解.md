---
title: L4. flask路由详解
comments: true
toc: true
date: 2018-05-31 23:09:03
updated:
tags:
- python
- flask
- web
- 路由
categories:
- flask
banner:
---


## 前言
《L1.flask介绍和helloworld》和《L2.最简单的flask应用》两节课中已见识了普通的url路由
比如*@app.route(\'/index\')*,*@app.route(\'/about\')*，但这些路由都是常量地址访问,
比如`127.0.0.1:5000/about`这节课将深入"路由"，讲解更多的路由语法以适应不同场景。

## 路由
打开上节课的"demo"项目。 上节课程地址{% post_link 框架/flask-最简单的flask应用 最简单的flask应用 %}
### 地址固定的路由 （Unique URLs）
```python
@app.route('/about')
def about():
    return '关于'
```
### 带参数的路由
写一个带参数的路由，语法是路由部分尖括号括住变量，对应的视图方法中定义参数来接收符合规则的参数，如下
app.py 
```python
@app.route('/user/<user_name>')
def user(user_name):
    return '你好，{}'.format(user_name)
```
浏览器请求，输出可变的user_name
![](1.png)
![](2.png)

### 参数转换器（convert type）
再写一个获取页码的路由,就像新闻网站一样，第一个参数目录，第二个参数页码。
app.py 
```python
@app.route('/category/<category_name>/<int:page_no>/')
def new_list(category_name, page_no):
    print('page_no type', type(page_no))
    content = 'category_name {}， page_no {}'.format(category_name, page_no)
    return content
```
可以看到参数前可以`int:`转型。本来参数是 str类型，添加`int:`后相当于
相当于`int(page_no)`强转型，print的结果是类型int。  
下面的代码可以获得所有转换器
app.py 
```python
from flask import Flask

if __name__ == '__main__':
    app = Flask(__name__)
    pprint(app.url_map.converters)
```
类型 | 解释
:- | :-
string | (默认)接受任何斜杠之外的文本
int     | 接受正整数
float   | 接受正浮点数
path    | 类似string但接受斜杠 '/'
uuid    | 接受 UUID(通用唯一识别码)
转换器`path:`接收带`/`的参数, 定义路由*@app.route('/get_url/<path: url>')*,请求`127.0.0.1:5000/get_url/http:google.com`
会返回`http://google.com`
### 默认值和多个装饰器
app.py 
```python
@app.route('/category/', defaults={'category_name':'news', 'page_no':1})
@app.route('/category/<category_name>/<int:page_no>/')
def new_list(category_name, page_no):
    print('type(page_no): ', type(page_no))
    content = 'category_name {}， page_no {}'.format(category_name, page_no)
    return content
```
多个装饰器可以修饰一个视图函数。  
defaults可以设定默认值，字典形式。
### 正则匹配
flask本身没有提供正则匹配，下面来实现这个个性化的功能。flask的路由、匹配、http部分基于werkzeug(德语:工具)包实现, 
我们基于它写一个类传递正则表达式参数，注册到flask的转换器中，最后写一个路由测试：
```python
from werkzeug.routing import BaseConverter

class RegexConverter(BaseConverter):
    def __init__(self, map, *args):
        self.map = map
        self.regex = args[0]

app = Flask(__name__)
app.url_map.converters['regex'] = RegexConverter

...

@app.route('/regex/<regex("[a-z]+"):user_name>')
def regex(user_name):
    return user_name
```
测试结果"127.0.0.1:5000/regex/alice"可以通过路由，"127.0.0.1:5000/regex/123"报404未匹配到。  
![](5.png)
![](6.png)
> self.regex到底是什么？
  Flask（其实是Werkzeug）使用Converter把URL中特殊部分（<regex("\[a-zA-Z0-9\]+"):uuid>）转换为Python变量，通用格式是<converter(args):var_name>。在这个例子中，一个叫regex的converter把URL中相应字段转换为view()中的uuid变量。
  因此，converter的regex就是用来判断这串字符是否符合转换格式，ok就转换，否则跳过。对于IntegerConverter来说，"abc"显然无能为力。也就是说，其实Werkzeug的路由本来就支持用正则表达式。string、int、float等都是从它派生出来的（可以看看IntegerConverter等built-in Converter的regex）。
  至于为什么不显式地支持，我猜可能是因为正则表达式不容易写好，buggy。 ----来自segmentfault.com的网友imwilsonxu

### 匹配POST请求
#### 准备login页面
1. 新建/templates/login.html 并写一个简单表单
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Login</title>
</head>
<body>
  <form method="post" action="{{ url_for('login_submit') }}">
    <input type="email" placeholder="用户名">
    <input type="password" placeholder="密码">
    <input type="submit">
  </form>
</body>
</html>
```
2. 添加两个路由，一个展示login页面，一个接受表单提交请求
app.py 
```python
@app.route('/login')
def login():
    return render_template('login.html')
    
@app.route('/login_submit')
def login_submit():
    return '表单的http请求是{}类型'.format(request.method)
```
#### 测试
1. 访问login界面
![](7.png) 
2. 填写表单用户名密码 然后提交
![](8.png) 
![](9.png) 
结果：请求失败"405 MethodNotAllowed"  
原因：表单请求默认为post类型请求，在谷歌浏览器开发者工具中看提交时的请求的headers的"Request Method"项值为"POST"。
3. 路由添加参数，接受post类型的请求
修改刚才写的路由, 添加methods参数并引入request打印请求的类型。  
路由装饰器默认匹配"GET"类型的请求，添加参数"methods=['POST']"以接受post请求，其余的请求类型还有"PUT" "DELETE" "OPTION"等。
```python
from flask import Flask, render_template, url_for, request

...

@app.route('/login_submit', methods=['POST'])
def login_submit():
    return '表单的http请求是{}类型'.format(request.method)
```
4. 修改后测试成功
![](10.png) 
#### HTTP中请求类型介绍
- GET 让浏览器返回信息，最常见的类型，参数可以在url中看到 形如/get_news/page_no=1&category=sport
- POST  告诉服务器要存储数据，场景例如注册登录表单，参数为了安全不可见，只发送一次请求 我们填写表单时刷新浏览器会提示我们如果继续已填写的数据会丢失
- PUT   类似POST, 目的是让服务器存储或更新数据，考虑到传输可能失败，会发送多次请求
- DELETE 删除
- OPTION  http三次握手的第一步，请求向服务器发送请求，服务器如果同意，那就是get、post之类了。flask已为你打理好平时见不到。
- HEAD  只关心头部不实际渲染内容，比如检查资源超链接的有效性。flask已为你打理好平时见不到。


## 本节结束后的代码
```bash
.
├── app.py
├── static
│   └── css
│       └── main.css
└── templates
    ├── index.html
    └── login.html

3 directories, 4 files
```
app.py
```python
from flask import Flask, render_template, url_for, request
from werkzeug.routing import BaseConverter

class RegexConverter(BaseConverter):
    def __init__(self, map, *args):
        self.map = map
        self.regex = args[0]

app = Flask(__name__)
app.url_map.converters['regex'] = RegexConverter

@app.route('/')
def hello_world():
    return render_template('index.html', user_name='小明')


@app.route('/service')
def service():
    return '服务'


@app.route('/about')
def about():
    return '关于'


@app.route('/user/<user_name>')
def user(user_name):
    # 一个普通的路由
    return '你好，{}'.format(user_name)


@app.route('/category/', defaults={'category_name':'news', 'page_no':1})
@app.route('/category/<category_name>/<int:page_no>/')
def new_list(category_name, page_no):
    # 一个普通的路由 多个参数
    print('type(page_no): ', type(page_no))
    content = 'category_name {}， page_no {}'.format(category_name, page_no)
    return content


@app.route('/regex/<regex("[a-z]+"):user_name>')
def regex(user_name):
    return user_name


@app.route('/login')
def login():
    return render_template('login.html')

@app.route('/login_submit', methods=['POST'])
def login_submit():
    return '表单的http请求是{}类型'.format(request.method)


if __name__ == '__main__':
    app.run(debug=True)

```

login.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Login</title>
</head>
<body>
  <form method="post" action="{{ url_for('login_submit') }}">
    <input type="email" placeholder="用户名">
    <input type="password" placeholder="密码">
    <input type="submit">
  </form>
</body>
</html>
```
其它文件未修改，参考上节


## license
🚫禁止商用 转载注明作者



