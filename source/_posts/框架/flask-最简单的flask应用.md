---
title: L2. 最简单的flask应用
comments: true
toc: true
date: 2018-05-31 12:07:58
updated:
tags:
- python
- flask
- 博客
- 项目
categories:
- flask
banner:
---
## 回顾上节代码
新建一个"demo"项目，可以用ide自带的工具生成
![](1.png)
```bash
.
├── app.py
├── static
└── templates
```
```python
from flask import Flask

app = Flask(__name__)


@app.route('/')
def hello_world():
    return 'Hello World!'


if __name__ == '__main__':
    app.run()
```
运行后浏览器访问


## 开始本节内容
### 开启debug模式
![](2.png)
运行后谷歌浏览器空白处右键>检查 查看源代码  
发觉其实'Hello World'被自动渲染到了完整的html页面结构中。  
现在我们想让这句话作为大标题，用`<h1>`标签括住内容
```python
def hello_world():
    return '<h1> Hello World! </h1>'
```
刷新浏览器无变化，因为内存中的代码无变化，需要重启项目。
"ctrl+C"停止项目再`python app.py` 运行项目或点击ide的restart按钮。  
为了修改后服务启动重启和获得更详细的信息，修改代码以debug调试模式运行：  
```python
if __name__ == '__main__':
    app.run(debug=True)
或
app = Flask(__name__)
app.DEBUG = True
```
### 模板渲染 render_template()
如果页面的html和功能很复杂，这么return `<h1> Hello World! </h1>` 这种方式就不好办了，那么就应该把html模板抽象出来。  
1. "template"目录下新建文件"index.html"并添加代码
![](3.png)
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
  <h1>Hello World</h1>
</body>
</html>
```
2. 引入"render_template"方法
app.py
```python
from flask import Flask, render_template
```
3. 修改视图函数
template/index.html
```python
def hello_world():
    return render_template('index.html')
```
刷新浏览器，结果与之前一致，但是把html分离了出来，降低了耦合方便修改。
4. 传入变量然后渲染
app.py
```python
@app.route('/')
def hello_world():
    return render_template('index.html', user_name='小明')
```
template/index.html
```html
<body>
  <h1>你好：{{ user_name }}</h1>
</body>
```
render_template()第一个参数是模板名，系统会自动在"template"文件下查找，后面可以声明key-value类型的变量。
双大括号是前端模板jinja2的语法，可以将服务端的变量渲染到前端。

浏览器结果
![](4.png)

### url构造 url_for()
1. 再写两个路由和视图函数
app.py
```python
...

@app.route('/service')
def service():
    return '服务'


@app.route('/about')
def about():
    return '关于'

```
2. 浏览器测试url生效
![](5.png)

3. 修改模板添加导航，超链接指向刚才写的路由规则
```html
<body>
  <h1>你好：{{ user_name }}</h1>
  <nav>
    <a href="http://127.0.0.1:5000/service">服务</a>
    <a href="">关于</a>
  </nav>
</body>
```
4. 如果域名和端口变化，那么需要重复修改超链接地址，flask提供了url_for函数构造地址
app.py 引入url_for
```python
from flask import Flask, render_template, url_for
```
template/index.html 改为
```html
<nav>
  <a href="{{ url_for('service') }}">服务</a>
  <a href="{{ url_for('service') }}">关于</a>
</nav>
```
url_for()第一个参数为路由对应的def 方法名，注意不是路由名,注意用双大括号渲染出来。  
原理：url_for其实是后端python语句，可以写在app.py文件中，它相当于os.path.join(domain, port, 'service')。
render_template函数会先把变量值插入到html中，再返回整个结果html。
5. url_for另一个作用，引用静态文件
新建目录static/css  
新建static/css/main.css 样式文件
```css
h1{
  color: red;    
}
a, a:visited{
  color: #494dab;
}
```
template/index.html
```html
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <link rel="stylesheet" href="{{ url_for('static', filename='css/main.css') }}">
</head>
```
结果
![](6.png)
引用静态资源时，url_for的第一个参数是静态资源文件夹的名字，第二个参数为filename=要引用的文件名。  
本课到此结束

## 本课所有代码
git地址 todo[]()
目录
```bash
.
├── app.py
├── static
│   └── css
│       └── main.css
└── templates
    └── index.html
```
index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <link rel="stylesheet" href="{{ url_for('static', filename='css/main.css') }}">
</head>
<body>
  <h1>你好：{{ user_name }}</h1>
  <nav>
    <a href="{{ url_for('service') }}">服务</a>
    <a href="{{ url_for('about') }}">关于</a>
  </nav>
</body>
</html>
```
main.css
```css
h1{
    color: red;
}
a, a:visited{
    color: #494dab;
}
```
app.py
```python
from flask import Flask, render_template, url_for

app = Flask(__name__)


@app.route('/')
def hello_world():
    return render_template('index.html', user_name='小明')


@app.route('/service')
def service():
    return '服务'


@app.route('/about')
def about():
    return '关于'

if __name__ == '__main__':
    app.run(debug=True)

```

## 数据流程图 http原理
浏览器url请求> flask路由匹配> 路由对应的视图函数 处理数据、把数据塞进模板、返回html代码> 浏览器加载html并展示在我们眼前


## license
🚫禁止商用 转载注明作者

<!--
### http理论基础
通过上面的例子理解整个请求响应流程
1. http 概念 三次握手
2. 流程图
3. ip地址 端口号

## flaskr
### 介绍
本教程将帮助您创建一个名为FLASKR的基本博客应用程序。  
功能需求：
1. 用户能注册登录。只支持一个用户。
2. 当用户登入后，可以向页面添加条目。条目标题是纯文本，正文可以是一些 HTML。因信任这里的用户，这部分 HTML 不做审查。
3. 页面倒序显示所有条目（后来居上），并且用户登入后可以在此添加新条目
技术栈：  
1. flask框架
2. 数据库SQLite3 。因为它足以应付这种规模的应用。对于更大型的应用，就有必要使用 SQLAlchemy ，将会在后面的课程涉及。
3. 基础html   。专门的前端课程会在下节课讲。
这是最终效果截图
![login]()
![article]()

### 0. 项目组织
使用pycharm建立一个工程项目"flaskr"。
在根目录下新建"static"和"templates"文件夹。  
创建"app.py" 文件。
目录结构如下
```bash
/flaskr
    /static         # 静态文件css js image
    /templates      # 模板文件 html
    app.py          # flask主脚本
    schema.sql      # sql脚本
```
flaskr是文件夹，项目最上层的组织，不是一个Python包所以没有__init__.py。
在接下来的步骤中，我们会直接把数据库模式和主模块放在这个目录中。
用户可以通过 HTTP 访问 static 文件夹中的文件，也即存放 css 和 javascript 文件的地方。
Flask 会在 templates 文件夹里寻找 Jinja2 模板（模板即HTML文件），之后教程中创建的模板将会放在这个文件夹里。
![]()

### 2. 应用设置
导入和引用配置
编辑app.py
```python
# all the imports
import os
import sqlite3
from flask import Flask, request, session, g, redirect, url_for, abort, \
     render_template, flash
```

### 2. 
首先我们要创建数据库模式（模式相当于一个厂房，表相当于厂房里的房间，房间里放着"数据"）。
对于这个应用来说，一张表就足够了，而且只需支持 SQLite，所以会很简单。
项目根目录下新建 `schema.sql` 文件，写入下面sql语句：   
```sql
create table entries (
    id integer primary key autoincrement,
    title string not null,
    text string not null
);
```
这个模式包含一个名为 entries(日记) 的表，该表中的每行都包含一个id 、一个title（标题）和一个text（内容）。 
id是一个自增的整数，也是主键；其余的两个是字符串，且不允许为空。
![]()



## 参考阅读
flask英文文档>tutorial[http://flask.pocoo.org/](http://flask.pocoo.org/docs/1.0/tutorial/)
本课程示例源码[flaskr](https://github.com/pallets/flask/tree/master/examples/tutorial/)
flask中文文档>教程[http://docs.jinkan.org/](http://docs.jinkan.org/docs/flask/quickstart.html)
-->
