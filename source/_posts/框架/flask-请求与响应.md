---
title: L5.flask请求、上下文、响应
comments: true
toc: true
date: 2018-06-04 13:16:51
updated:
tags:
- python
- flask
- web
- http
categories:
- flask
banner:
---

## flask请求到响应的整体流程
[](1.jpg)

## 从request对象获取参数
flask的request对象包含了请求的信息，有很多属性，下面介绍它的form和args属性
### 表单提交形式
1. 修改login.html，为input标签添加`name`属性
```html
<body>
  <form method="post" action="{{ url_for('login_submit') }}">
    <input type="email" name="username" placeholder="用户名" >
    <input type="password" name="password" placeholder="密码">
    <input type="submit">
  </form>
</body>
```
app.py 改写"/login_submit"路由，用`request.form`获取表单对象，然后通过字典形式取值，字典的key对应html页面的name属性。
```python
@app.route('/login_submit', methods=['POST'])
def login_submit():
    form_dict = request.form
    username = form_dict['username']
    password = form_dict['password']
    return '表单的请求方式{}，username:{}，password:{}'.format(request.method, username, password)
```
2. 测试
![](2.png)
3. 整理一下，将"/login"和"/login_submit"两个路由功能合二为一,删除"/login_submit"这个路由，
初次访问"/login"时是GET请求获取表单页面，提交表单submit时走POST请求处理数据。这个路由函数具有渲染页面和处理逻辑两种功能。  
login.html 修改提交时的请求地址
```html
<body>
  <form method="post" action="{{ url_for('login') }}">
  ...
```
app.py
```python
@app.route('/login', methods=['GET', 'POST'])
def login():
    print(request.method)
    if request.method == 'GET':
        return render_template('login.html')
    elif request.method == 'POST':
        form_dict = request.form
        username = form_dict['username']
        password = form_dict['password']
        return '表单的请求方式{}，username:{}，password:{}'.format(request.method, username, password)
    else:
        pass
```
*如果测试中刷新浏览器有时一直走post请求导致看不到login表单页面，关闭标签页再重新打开*
### 问号传参形式
除了表单，请求时通过参数传参更为常见，常见于api接口。  
比如请求新闻列表页，参数有目录和页码，形如`http://news?category=sport&page_no=1`。  
app.py 写一个路由，用`request.args`获取参数对象
```python
@app.route('/news')
def news():
    args = request.args
    category = args['category']
    page_no = args['page_no']
    return 'category:{}, page_no:{}'.format(category, page_no)
```
访问`http://localhost:5000/news?category=sport&page_no=2`结果
![](3.png)
## 上传文件
1. 新建template/upload.html ,写入如下代码
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>upload</title>
</head>
<body>
<h3>文件上传</h3>
<form action="{{ url_for('upload') }}" method="post" enctype="multipart/form-data">
  <input type="file" name="file">
  <input type="submit" value="上传">
</form>
</body>
</html>
```
注意form里的enctype="multipart/form-data"，上传文件时必须声明。  
指明了这个表单submit时请求的路径，请求类型为post，会上传二进制文件。  
2. 引入os包, 为了处理文件路径。
```python
import os
```
2. templates/static 目录下新建"files"文件夹，用来存放上传的文件。  
```bash
.
├── app.py
├── static
│   ├── css
│   │   └── main.css
│   └── files
└── templates
    ├── index.html
    └── login.html
4 directories, 4 files
```
3. 写一个upload路由，类似上面写的表单，初次访问时get请求获取upload.html,提交时走post请求让服务器存储图片。  

```python
@app.route('/upload', methods=['GET', 'POST'])
def upload():
    if request.method == 'GET':
        return render_template('upload.html')
    else:
        file = request.files['file']
        base_path = os.path.dirname(__file__)
        save_path = os.path.join(base_path, 'static/files/', secure_filename(file.filename))
        print('save_path:{}'.format(save_path))
        file.save(save_path)
        return 'upload done'
```
save方法来自werkzeug， dts（destination）参数表示 目标文件夹或目标文件
werzeug.uril中的secure_name方法可以对文件名中的特殊字符转换以免存储时存错位置,
例如 "secure_filename(u'i contain cool \xfcml\xe4uts.txt')" 会转化为 'i_contain_cool_umlauts.txt'。

## cookies
cookie(直译：饼干)是无状态跟踪技术，存储在客户端浏览器。cookie的适用场景例如记住登陆状态。本次主要初步了解cookie和
练习make_response函数使用。后面的课程会专门讲解cookie、session以及它们的作用。  
cookie的设置需要make_response来做，简单理解make_response比render_template函数更偏底层。  
引入make_response
```python
from flask import Flask, render_template, url_for, request, make_response
```
修改"/login"路由
```python
@app.route('/login', methods=['GET', 'POST'])
def login():
    print(request.method)
    if request.method == 'GET':
        return render_template('login.html')
    elif request.method == 'POST':
        form_dict = request.form
        username = form_dict['username']    # 或者写成 username = request.form['username']
        password = form_dict['password']
        # 先假设登陆成功,设置cookie
        response = make_response(render_template('index.html'))
        response.set_cookie('username', username)
        return response
        # return '表单的请求方式{}，username:{}，password:{}'.format(request.method, username, password)
    else:
        pass
```
测试login页面提交，服务端设置了cookie，最后渲染了index页面。  
可以在谷歌浏览器开发者工具中看到刚刚设置的cookie。  
![](4.png)
![](5.png)

## errorhandler、abort和重定向
### abort
引入abort
```python
from flask import abort
```
写一个路由，假设场景，非登陆用户禁止访问后台管理页面
```python
@app.admin('/admin')
def admin():
    # 假设未登陆管理员账户就试图访问此页面
    abort(401)
```
![](6.png)
abort参数可以返回状态码
### errorhandler
会了提升访问体验，如果404,提供一个友好提示页面。  
新建templates/404.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>404</title>
</head>
<body>
<h5>抱歉，您访问的页面不存在</h5>
</body>
</html>
```
errorhandler装饰器使用方便，接受参数状态码，渲染一个页面
```python
@app.errorhandler(404)
def page_not_found(error):
    return render_template('404.html'), 404
```
![](7.png)
### 重定向（redirect）
引入redirect, 改写return语句，重定向到百度首页或我们的项目首页
```python
@app.errorhandler(404)
def page_not_found(error):
    # return render_template('404.html'), 404
    return redirect(url_for('hello_world'))     # return redirect('https://baidu.com')
```
访问`http://localhost:5000/balabala` 浏览器加载中，url变为`http://localhost:5000/`并显示渲染后的页面。

### context processors
不详叙述，课下自学[context processors](http://flask.pocoo.org/docs/1.0/templating/)

## 本节代码
```bash
yangzheng-macmini:demo yangzheng$ tree
.
├── app.py
├── static
│   ├── css
│   │   └── main.css
│   └── files
│       └── test.png
└── templates
    ├── 404.html
    ├── index.html
    ├── login.html
    └── upload.html

4 directories, 7 files
```
app.py
```python
import os
from flask import Flask, render_template, url_for, request, make_response, abort, redirect
from werkzeug.routing import BaseConverter
from werkzeug.utils import secure_filename

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
    print('type(page_no): ', type(page_no))
    content = 'category_name {}， page_no {}'.format(category_name, page_no)
    return content


@app.route('/regex/<regex("[a-z]+"):user_name>')
def regex(user_name):
    return user_name


@app.route('/login', methods=['GET', 'POST'])
def login():
    print(request.method)
    if request.method == 'GET':
        return render_template('login.html')
    elif request.method == 'POST':
        form_dict = request.form
        username = form_dict['username']    # 或者写成 username = request.form['username']
        password = form_dict['password']
        # 先假设登陆成功,设置cookie
        response = make_response(render_template('index.html'))
        response.set_cookie('username', username)
        return response
        # return '表单的请求方式{}，username:{}，password:{}'.format(request.method, username, password)
    else:
        pass

@app.route('/news')
def news():
    args = request.args
    category = args['category']
    page_no = args['page_no']
    return 'category:{}, page_no:{}'.format(category, page_no)


@app.route('/upload', methods=['GET', 'POST'])
def upload():
    if request.method == 'GET':
        return render_template('upload.html')
    else:
        file = request.files['file']
        base_path = os.path.dirname(__file__)
        save_path = os.path.join(base_path, 'static/files/', secure_filename(file.filename))
        print('save_path:{}'.format(save_path))
        file.save(save_path)
        return 'upload done'

@app.route('/admin')
def admin():
    # 假设未登陆管理员就试图访问此页面
    abort(401)

@app.errorhandler(404)
def page_not_found(error):
    # return render_template('404.html'), 404
    return redirect(url_for('hello_world'))

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
  <form method="post" action="{{ url_for('login') }}">
    <input type="email" name="username" placeholder="用户名">
    <input type="password" name="password" placeholder="密码">
    <input type="submit">
  </form>
</body>
</html>
```
upload.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>upload</title>
</head>
<body>
<h3>文件上传</h3>
<form action="{{ url_for('upload') }}" method="post" enctype="multipart/form-data">
  <input type="file" name="file">
  <input type="submit" value="上传">
</form>
</body>
</html>
```
404.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>404</title>
</head>
<body>
<h5>抱歉，您访问的页面不存在</h5>
</body>
</html>
```
其余代码未修改见上节
git仓库(todo)[]()

## license
🚫商用 转载注明作者
