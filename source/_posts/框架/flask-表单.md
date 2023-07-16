---
title: L7.flask表单
comments: true
toc: true
date: 2018-06-08 19:08:57
updated:
tags:
- python
- flask
- 表单
- 插件
categories:
- flask
banner:
---
## flask-WTF介绍
wtform（what the form）是一个基于python的灵活的表单验证和渲染库。
"flask-WTF"是wtform的第三方集成，作为flask插件的形式。它可以从request对象中自动加载数据，使用flask模板标签渲染，除此之外提供csrf安全防护等功能。  
简而言之就是会把后端写的类翻译成前端html的form，后端写法实现前端表单。  
PS: 有点跟上节的jinja2相似，jinja2是前端实现后端的东西，这个是后端实现前端的东西，我的建议是跟前端有关的东西都交给html、css、js来做，
前端基础不牢的话会不理解原理，修改样式也很麻烦，多记一套语法。但是学会本节内容处理基本表单也是很快的。  
## 准备
1. 新建一个flask项目来研究表单使用，项目命名比如为"demo3"。
```bash
.
├── app.py
├── static
└── templates

2 directories, 1 file
```
![](1.png)
2. 安装"flask-WTF"包
```bash
pip install Flask-WTF
```
![](2.png)
## 语法
### 基本用法
1. 新建forms.py  书写表单类。
```python
from flask_wtf import Form
from wtforms import StringField, PasswordField, SubmitField
from wtforms.validators import DataRequired, Email, Length

class LoginForm(Form):
    email = StringField('注册邮箱', validators=[DataRequired(), Email()], render_kw={'placeholder':u'请输入邮箱'})
    password = PasswordField('密码', validators=[DataRequired()])
    nickname = StringField('昵称', validators=[Length(1, 10, message='长度1到10之间')])
    submit = SubmitField('提交')
```
从flask_wtf扩展包引入基类Form，写一个登陆表单类LoginForm并继承基类Form。字段属性和验证器则从wtforms包中引入。  
注意验证类要实例化否则在后面调用form.validator_on_submit()函数时报错。  
支持的字段类型详见[wtform basic-fields](https://wtforms.readthedocs.io/en/stable/fields.html#basic-fields)
下面是wtform包中常用的Field(字段)和validator(表单验证)：

字段类型 | 说明   | 对应前端input标签的type属性
:- | :- | :-
StringField     |  字符串 | text 
TextAreaField        |   文本框 | textarea
PasswordField       | 密码 | password  
HiddenField     |   隐藏字段 | input属性hidden="true"
DateTimeField       |   日期时间  | 日期选择插件
IntegerField        |   整数字      | integer
BooleanField        |   布尔      |  checkbox
RadioField      |   单选🔘     | radio
SelectmultipleField     |   复选框  | checkbox
FileField       |  把表单作为字段嵌入另一个表单  |
 
验证函数 | 说明
:- | :-
Email   |   验证电子邮件
EqualTo |   比较两个字段的值，比如确认密码时用
IPAddress   | IP地址
Length  | 字符串长度
NumberRange | 值在整数范围内
Required | 必须填写数据
Regexp | 使用正则验证
AnyOf | 输入值在可选值列表中
URL | 是URL

2. app.py书写路由
```python
from flask import Flask, request, render_template, redirect, url_for
from forms import LoginForm

app = Flask(__name__)
app.config['SECRET_KEY'] = 'BALAb@3iu'

@app.route('/')
def hello_world():
    return 'Hello World!'

@app.route('/login', methods=['GET', 'POST'])
def login():
    form = LoginForm()
    if form.validate_on_submit():
        pass
    return render_template('login.html', form=form)

if __name__ == '__main__':
    app.run(debug=True)
```
配置项"SECRET_KEY"是一个自定义的字符串，可以随便写一个复杂的长字符串，SECRET_KEY将用来加密生成一个字符串csrf_token，这样表单提交时可以跟后台比对token判断是否被劫持。  
表单默认启用csrf保护，如果不配置SECET_KEY会报如下错误：
![](3csrf_error.png)
form.validate_on_submit()函数会在request.POST中讲表单信息赋值到LoginForm实例化对象中，第一次GET请求这个函数将返回False直接走后面的渲染页面方法。
*本课中为了说明简单，密钥直接在app.py文件中设置。而正是环境中为了密钥安全应该设置到环境变量中，这一技术将会在部署课程时介绍。*  
3. 新建templates/login.html 
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Login</title>
</head>
<body>
  <form method="post">
    {{ form.hidden_tag() }}
    {{ form.csrf_token }}
    {{ form.email.label }}{{ form.email() }}
    {{ form.password.label }}{{ form.password() }}
    {{ form.nickname.label }}{{ form.nickname() }}
    {{ form.submit() }}
  </form>
</body>
</html>
```
从后端传来的form变量渲染字段属性。
4. 访问 /login路由查看结果
![](4.png)
注意观察forms.py与login.html的对应关系，例如StringField被转换成了text类型的input标签，wtforms包的各种字段类型会被渲染为对应的前端html input标签的各种type。  
表单类的属性名转换成了input标签的id和name。  
可以看到用于安全防护的csrf_token,这个值是隐藏的，会随表单提交到后台时供验证。
![](5验证.png)
可以看到如果未满足验证条件会弹出提示，DataRequired被转换成前端input标签中的required="True"。  

### 接收表单参数和会话
回顾 L3.前端知识 和 L5.上下文请求和响应中获取表单参数的知识 {% post_link 框架/flask-请求与响应 flask请求上下文与响应 %} 。
接收参数。先假设一个账户名和密码，然后判断用户名密码是否正确，如果登陆成功，跳转到首页并显示欢迎信息。  
app.py
```python
from flask import Flask, request, render_template, redirect, url_for, session
from forms import LoginForm

app = Flask(__name__)
app.config['SECRET_KEY'] = 'BALAb@3iu'

@app.route('/')
@app.route('/index')
def index():
    email = session.get('email', None)
    print(email)
    return render_template('index.html', email=email)

@app.route('/login', methods=['GET', 'POST'])
def login():
    form = LoginForm()
    if form.validate_on_submit():
        email = form.data['email']
        password = form.password.data
        nickname = form.nickname.data
        if email=='test@qq.com' and password=='test':
            # 为了测试账户名和密码先写死
            session['email'] = email
            return redirect(url_for('index'))
    return render_template('login.html', form=form)

if __name__ == '__main__':
    app.run(debug=True)
```
![](6.png)
![](7.png)
结果分析：
#### 接受参数
wtforms包的form实例跟flask的request对象不同，取值是"form.字段属性.data"方式取值。
#### 重定向
如果post请求最后直接render一个页面的话，当请求失败或刷新浏览器时，浏览器会提示一个警告"提交前请确认，否则表单信息丢失"，
并且看不到登陆表单，因为浏览器一直在走post请求不再走get请求。这样有损用户体验。所以在登陆成功后重定向到首页避免这种情况。另一个方法
是把表单页和提交请求分成两个route写。
#### session
session（会话）。session以key-value字典形式存储在**服务端**，会话的作用是跟踪用户状态。我举个例子就明白了：我们登陆淘宝成功，服务器知道这个用户登陆了
就存储一个由客户信息生成的session_id，下次再登陆时客户端请求带着这个session_id,服务器就能根据这个值判断出用户已登录过。
所以我们登陆淘宝后，关闭网页再次打开网页显示的还是已登录状态。  
本例中session用来存储已登录用户的账户名，已用户再次打开首页时能看到欢迎信息和自己的账户信息。  
session是有时间限制了，所以过一段时间后还需要重新登录，过期时间通过参数改变。  
与cookie的区别：
- session在服务端存储；cookie在浏览器端存储。
- session强调客户端跟服务器正在沟通，一种持续的状态，放一些信息供下次请求调用。
- 主要应用场景，session 检测客户已登录，统计在线人数；cookie 存放权限token随请求时发送 存一些信息比如记住用户名密码。
- session一般几分钟到几个小时；cookie时间比较长几天或几个月。
- session的信息安全敏感；cookie存的不太重要安全。

#### flash消息闪现
下面想在登陆成功后提示消息。
flash()由flask引入，作用是提供反馈信息。
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Index</title>
</head>
<body>
  {% with messages = get_flashed_messages() %}
    {% if messages %}
      <ul class=flashes>
      {% for message in messages %}
        <li>{{ message }}</li>
      {% endfor %}
      </ul>
    {% endif %}
  {% endwith %}

  {% if email %}
    <h3>欢迎 账户邮箱为" {{ email }} "的用户您已登陆</h3>
  {% else %}
    <h3><a href="{{ url_for('login') }}">点击这里去登陆</a></h3>
  {% endif %}
</body>
</html>
```
app.py
```python
@app.route('/')
@app.route('/index')
def index():
    email = session.get('email', None)
    print(email)
    return render_template('index.html', email=email)

@app.route('/login', methods=['GET', 'POST'])
def login():
    form = LoginForm()
    if form.validate_on_submit():
        email = form.data['email']
        password = form.password.data
        nickname = form.nickname.data
        if email=='test@qq.com' and password=='test':
            # 为了测试账户名和密码先写死
            session['email'] = email
            flash('登陆成功')
            return redirect(url_for('index'))
    return render_template('login.html', form=form)
```
![](8.png)
与render时返回变量不同的是变量渲染的时间点不同，比如redirect时没有专递参数但消息还是出来了。另外比较友好，会出现一个可关闭渐入渐出的提示框。
从前端模板看出，flash的原理可能是先把信息存到文件中，最后再读取。跟render时返回值的不同是render的值再加载html前就已经完成了，而flash在生命周期的最后才加入。

## flask-bootstrap
todo 考虑是否加入这块内容

## 本课结束后的代码
```bash
.
├── app.py
├── forms.py
├── static
└── templates
    ├── index.html
    └── login.html

2 directories, 4 files
```
app.py
```python
from flask import Flask, request, render_template, redirect, url_for, session, flash
from forms import LoginForm

app = Flask(__name__)
app.config['SECRET_KEY'] = 'BALAb@3iu'

@app.route('/')
@app.route('/index')
def index():
    email = session.get('email', None)
    print(email)
    return render_template('index.html', email=email)

@app.route('/login', methods=['GET', 'POST'])
def login():
    form = LoginForm()
    if form.validate_on_submit():
        email = form.data['email']
        password = form.password.data
        nickname = form.nickname.data
        if email=='test@qq.com' and password=='test':
            # 为了测试账户名和密码先写死
            session['email'] = email
            flash('登陆成功')
            return redirect(url_for('index'))
    return render_template('login.html', form=form)

if __name__ == '__main__':
    app.run(debug=True)

```
forms.py
```python
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, SubmitField
from wtforms.validators import DataRequired, Email, Length


class LoginForm(FlaskForm):
    email = StringField('注册邮箱', validators=[DataRequired(), Email()], render_kw={'placeholder':u'请输入邮箱'})
    password = PasswordField('密码', validators=[DataRequired()])
    nickname = StringField('昵称', validators=[Length(min=1, max=10, message='长度1到10之间')])
    submit = SubmitField('提交')

```
index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Index</title>
</head>
<body>
  {% with messages = get_flashed_messages() %}
    {% if messages %}
      <ul class=flashes>
      {% for message in messages %}
        <li>{{ message }}</li>
      {% endfor %}
      </ul>
    {% endif %}
  {% endwith %}

  {% if email %}
    <h3>欢迎 账户邮箱为" {{ email }} "的用户您已登陆</h3>
  {% else %}
    <h3><a href="{{ url_for('login') }}">点击这里去登陆</a></h3>
  {% endif %}
</body>
</html>
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
  <form method="post">
    {{ form.hidden_tag() }}
    {{ form.csrf_token }}
    {{ form.email.label }}{{ form.email() }}
    {{ form.password.label }}{{ form.password() }}
    {{ form.nickname.label }}{{ form.nickname() }}
    {{ form.submit() }}
  </form>
</body>
</html>
```

## 参考文档
[wtforms的github主页](https://github.com/wtforms/wtforms)  
[wtforms文档](https://wtforms.readthedocs.io/en/stable/)  
[flaskWTF官方文档-英文](https://flask-wtf.readthedocs.io/en/stable/)  
[flaskWTF文档-中文老版本](http://docs.jinkan.org/docs/flask-wtf/index.html)

## 课外阅读
[CSRF攻击与防御](https://www.cnblogs.com/phpstudy2015-6/p/6771239.html)  
[CSRF 攻击的应对之道](https://www.ibm.com/developerworks/cn/web/1102_niugang_csrf/)  

## license
🚫禁止商用 转载注明作者