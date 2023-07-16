---
title: L6.jinja2模板引擎
comments: true
toc: true
date: 2018-06-07 12:53:56
updated:
tags:
- python
- flask
- jinja2
- 模板
categories:
- flask
banner:
---
## 介绍
安装flask的时候会安装jinja2依赖，jinja2是一个模板引擎，把变量和逻辑插入到html中并最终渲染出静态html。
一节helloworld中写过`return '<h1>helloworld</h1>'`，如果页面复杂和包含循环，拼这个含有html的字符串将会十分复杂，
前几节我们已使用过jinja的一两种基本语法，本节将会深入jinja语法。
流程：render_template > jinja2模板引擎处理 > 返回HTML。  
jinja2官网：[jinja.pocoo.org/](http://jinja.pocoo.org/)。  

## 准备
1. 新前几节使用的项目"demo"放到一边，建一个新的项目专门研究jinja2，例如"demo2"。
```bash
.
├── app.py
├── static
└── templates

2 directories, 1 file
```
app.py
```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/index')
def index():
    return render_template('index.html', hello="hello world")

if __name__ == '__main__':
    app.run(debug=True)
```
新建templates/index.html 
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
{{ hello }}
</body>
</html>
```
## 基本语法
### 渲染单值
`{% raw %} {{ }} {% endraw %}` 双大括号，又称胡子表示法，表示渲染变量或返回语句的值。
### 赋值
`{% raw %} {% set variable=value %} {% endraw %}`  标签语法{% raw %} {% tag_name %} {% endraw %},不同标签标签是不同功能，这里讲的是"set"标签。
index.html
```html
<div id="set">
{% set links=[{'label': '百度', 'url':'baidu.com'}, {'label': '博客', 'url':'zyzypy.com'}]%}
{{ links[0] }}
</div>
```
### 控制语句
类似后端的 if elif else。
```html
<div id="control">
{% if hello == 'hello world' %}
  {% print(True) %}
{% elif hello == '' %}
  {% print('null') %}
{% else %}
  {% print(False) %}
{% endif %}
</div>
```
ps：现在流行前后端分离，要吗后台数据处理好再返回，要么一些需要写死的值直接写html里，所以这个标签暂时没什么用。
### 循环
1. 写测试路由
app.py
```python
@app.route('/index')
def index():
    hello = "hello world"
    name_list = ['小明', '小黄', 'canaan']
    return render_template('index.html', hello="hello world", name_list=name_list)
```
index.html
```html
<body>
<div id="single variable">{{ hello }}</div>
<div id="loop">
{% for name in name_list %}
  <p>{{loop.index}} {{ name }}</p>
{% endfor %}
</div>
</body>
```
2. 语法：`{% raw %} {% %} {% endraw %}` 标签的特殊系记号是一个大扩招加一个百分号。标签开始和标签end成对出现。
for标签与后端语法非常类似，不同的是有前端模板的标记，还有标签闭合。
![](base2.png)
![](base3.png)
![](base4.png)
浏览器访问结果结果，将list循环出单值并渲染。可以看到，循环里面的 p标签也被重复了。
3. 循环内置的一些方法
loop.index是内置的方法，翻遍获取当前循环下标、list长度、第一行最后一行等。结合控制语句，方便实现业务功能。  

variable | description  
:- | :-
loop.index  | The current iteration of the loop. (1 indexed)
loop.index0   | The current iteration of the loop. (0 indexed)
loop.revindex | The number of iterations from the end of the loop (1 indexed)
loop.revindex0 | The number of iterations from the end of the loop (0 indexed)
loop.first | True if first iteration.
loop.last | True if last iteration.
loop.length | The number of items in the sequence.

同学们可以思考下怎么用循环和控制语句，实现表格的隔行变色功能。

### 注释
语法：`{% raw %}{# 被括住的这些看不到 #}{% endraw %}` 注释的语法是一个大扩招加一个井号。
与html前端注释`<!-- --> `不同的是，html注释可以在查看网页源代码中看到；jinja2语法注释的部分压根不会返回那一块的html，浏览器查看源代码也看不到。

### safe过滤器
1. 写一个简单的路由,返回中包含html字符
```python
@app.route('/filter')
def filter():
    return render_template('filter.html', text="<h1>hello world</h1>")
```
新建 templates/filter.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>filter</title>
</head>
<body>
<div id="test safe">
  {{ text }}
</div>
</body>
</html>
```
![](safe1.png)
`<h1>`作为字符串展示了出来，因为jinja会自动转义，为的是防止**XSS(跨站请求攻击)**。  
2. safe过滤器。如果内容自己网站的内容，因为业务逻辑需要动态拼一部分含html的内容，可信任的，那么可以使用safe过滤器
```html
<div id="test safe">
  {{ text | safe }}
</div>
```
![](safe2.png)
### 其它常用过滤器
过滤器可以连用。常用过滤器有
- safe 渲染受信任的标签
- e  不转义，可以用在url后（todo 不太清晰它跟safe的区别）
- abs(number)   绝对值
- batch(3, '&nbsp;')  作用循环，每循环3次后加个空格
还有很多。

### 自定义过滤器
过滤器除了内置的还可以根据个人需要自定义，下面写一个自定义过滤器，实现字符串反向。  
jinja2文档[Custom Filters](http://jinja.pocoo.org/docs/2.10/api/#custom-filters)的方法更偏底层，flask的封装好的装饰器使用方便
```python
@app.template_filter('reverse')
def reverse_filter(string):
    return string[::-1]
```
filter.html
```html
<div id="custom filter">
  {{ text | reverse }}
</div>
```
![](filter3.png)
过滤器的string参数指text变量，如果需要更多参数 `def reverse_filter(string, arg1)`,前端 `\{\{ text | reverse(text, arg1) \}\}`。
PS：个人建议后台处理好所有逻辑，再返回前端，前端逻辑太多会使耦合度增加。


## 模板继承
模板集成的主要作用是将前端公共部分如header footer提到一个公共基础html内，把个性化的地方如title和body中的content作为变量让子模板修改，
公共模板改一处比如头部资源链接地址变了而其它页面就不用每个都改。最终达到减少前端代码量，减少修改内容耗时，前端模板更清晰更好维护的作用。
1. 新建templates/layout.html
layout.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  {% block head %}
  <meta charset="UTF-8">
  <title>{% block title %}{% endblock %}</title>
  <link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}"/>
  {% endblock %}
</head>
<body>
  <div id="content">{% block content %}{% endblock %}</div>
  <div id="footer">
    {% block footer %}
    &copy;Copyright 2018 by <a href="https://zyzypy.com">canaan</a>
    {% endblock %}
  </div>
</body>
</html>
```
2. 新建templates/about.html
```html
{% extends "layout.html" %}
{% block title %}About{% endblock %}
{% block head %}
  {{ super() }}
  <style type="text/css">
    .important { color: red }
  </style>
{% endblock %}
{% block content %}
  <h1>关于</h1>
  <p class="important">
    大象圈是郑州先进的互联网圈子，QQ群：172948437
    web部分讲师是canaan，他的python交流QQ群：775648064
  </p>
{% endblock %}
```
3. app.py
```python
@app.route('/about')
def about():
    return render_template('about.html')
```
4. 结果
![](extends1.png)
5. 语法："block"标签语法：`{% raw %} {% block head %}{% endblock %} {% endraw %}` 。  
父模板（layout.html）主要负责页面整体框架, 用block标签按页面结构括住，block后面起块的名字，表示括住这一块内容可由子模板改写。  
子模板继承语法："extends"标签先继承`{% raw %} {% extends "layout.html" %} {% endraw %}`父模板。 子模板书写相同的block名字的块，并在书写子模板想要的内容`{% raw %} {% block head %} 子模板追加的个性化内容 {% endblock %} {% endraw %}` ，那么内容就会覆盖填充到父模板中。  
如果想要父模板中的块内容，又想在原先内容后面追加新内容，那么就先`{% raw %} {{ super }}{% endraw %}`引用父模板的内容，再在其后写追加的个性化内容。正如结果中父模板的css样式和子模板中的css样式都生效了。  
如果子模板没有写block块，那么将会展示父模板block块中的默认内容。  
逻辑其实跟后端的继承、调用父类属性、子类重写父类方法这些概念类似。  
6. 备注：学习这一块时注意观察"父模板，子模板，浏览器中最终的html"这三者的对应关系，前两者怎么生成到html。除了extends继承标签还有include包含标签,根据名字大家估计猜出来怎么用它了，因为用的比较少就不叙述了。

## 宏
相当于后端函数，只不过是在前端模板定义的。不再叙述。

## 备注
jinja2本质就是后端语言根据逻辑变量各种东西拼结果html。由于后端擅长处理数据和逻辑，和前后端分离有利于维护，所以本节的set、filter、宏并不建议大量使用，尽量把逻辑和数据准备的过程放后端，然后返回前端整理好的数据，不经过什么处理就能直接渲染或经过简单循环就能出来最好。  
建议大家尽量锻炼html、css等前端知识，因为如果html看不懂的话，写成模板继承的形式你也会看不懂或不知道在哪里设计block。

## 本课结束后的代码
```bash
.
├── app.py
├── static
│   └── css
│       └── style.css
└── templates
    ├── about.html
    ├── filter.html
    ├── index.html
    └── layout.html

3 directories, 6 files
```
style.css
```css
#content {
    color: green;
}
#footer {
    color: blue;
}
```
about.html
```html
{% extends "layout.html" %}
{% block title %}About{% endblock %}
{% block head %}
  {{ super() }}
  <style type="text/css">
    .important { color: red }
  </style>
{% endblock %}
{% block content %}
  <h1>关于</h1>
  <p class="important">
    大象圈是郑州先进的互联网圈子，QQ群：172948437
    web部分讲师是canaan，他的python交流QQ群：775648064
  </p>
{% endblock %}
```
filter.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>test filter</title>
</head>
<body>
<div id="test safe">
  {{ text | safe }}
</div>

</body>
</html>
```
index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
<div id="single variable">{{ hello }}</div>

<div id="control">
{% if hello == 'hello world' %}
  {% print(True) %}
{% elif hello == '' %}
  {% print('null') %}
{% else %}
  {% print(False) %}
{% endif %}
</div>

<div id="loop">
{% for name in name_list %}
  <p>{{ loop.index }} {{ name }}</p>
{% endfor %}
</div>

<div id="set">
{% set links=[{'label': '百度', 'url':'baidu.com'}, {'label': '博客', 'url':'zyzypy.com'}]%}
{{ links[0] }}
</div>
</body>
</html>
```
layout.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  {% block head %}
  <meta charset="UTF-8">
  <title>{% block title %}{% endblock %}</title>
  <link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}"/>
  {% endblock %}
</head>
<body>
  <div id="content">{% block content %}{% endblock %}</div>
  <div id="footer">
    {% block footer %}
    &copy;Copyright 2018 by <a href="https://zyzypy.com">canaan</a>
    {% endblock %}
  </div>
</body>
</html>
```
app.py
```python
from flask import Flask, render_template, url_for

app = Flask(__name__)

@app.route('/index')
def index():
    hello = "hello world"
    name_list = ['小明', '小黄', 'canaan']
    return render_template('index.html', hello="hello world", name_list=name_list)

@app.route('/filter')
def filter():
    return render_template('filter.html', text="<h1>hello world</h1>")

@app.template_filter('reverse')
def reverse_filter(string):
    return string[::-1]

@app.route('/about')
def about():
    return render_template('about.html')

if __name__ == '__main__':
    app.run(debug=True)

```

## license
🚫禁止商用 转载注明出处


