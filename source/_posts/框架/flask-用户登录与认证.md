---
title: L12.用户登录与认证
comments: true
toc: true
date: 2018-06-28 12:57:13
updated:
tags:
- flask
- python
- 注册
- 扩展
categories:
- flask
banner:
---
(todo  本课未完成  flask系列教程缓慢更新)
本节基于上节的项目架构，开始开发博客😀。  
本课内容较多，建议先跟着把代码敲出来运行。以后用flask框架第二遍写项目时，尽量不看之前项目自己想着写着。  
很多标注"课下"的资料适合进一步理解，第一遍不用看太花时间，项目运行出来再回头理解。除了使用插件，尽量使用python内置库和flask自带的功能完成项目，又助于理解原理。
## 准备
基于上节课程{% post_link 框架/flask-大型项目组织和蓝图 L11大型项目组织和蓝图 %}的架构,新建项目"**myblog**"，  
（这里我去掉了上节用来举例子的app/admin包和migration文件夹）。  
```bash
.
├── app
│   ├── __init__.py
│   ├── auth
│   │   ├── __init__.py
│   │   └── views.py
│   ├── main
│   │   ├── __init__.py
│   │   ├── forms.py
│   │   ├── models.py
│   │   └── views.py
│   ├── static
│   └── templates
├── config.py
├── manage.py
├── requirements.txt
├── test
│   └── __init__.py
└── venv

7 directories, 11 files
```
## auth蓝图
回忆上节学过的蓝图，分模块开发，新建 **app/auth** 包和下面的文件，结构与app/main包类似。  
1. 创建蓝本
app/\_\_init\_\_.py
```python
from flask import Blueprint

auth = Blueprint('auth', __name__, url_prefix='/auth')

from . import views
```
2. 写一个login路由和相应的视图函数
app/views.py
```python
from flask import render_template
from . import auth

@auth.route('/login')
def login():
    return render_template('auth/login.html')
```
因为考虑到分模块，不同蓝图的模板文件可能重名，模板路径不像蓝图有url_prefix前缀。
所以我们加上蓝图名指定路径为"auth/login.html",（默认模板文件夹是flask app实例化所在的包下的templates文件夹）也就是会查找 app/templates/auth/login.html文件。
3. 模板文件
app/templates/auth/login.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Login</title>
</head>
<body>
login
</body>
</html>
```
4. 测试
访问刚才写的路由 `http://127.0.0.1:5000/auth/login` 成功渲染
![](1.png)
## 数据库和建表
回顾 {% post_link 框架/flask-数据库基础 L8数据库基础%} {% post_link flask-flaskSQLAlchemy L9flaskSQLAlchemy %}两节课的内容。  
可以用数据库图形管理工具操作，也可以打开终端命令行操作。  
1. 用超级管理员postgres(我这里密码是postgres)登陆数据库
```bash
psql -U postgres
```
2. 新建数据库用户、新建库、赋权限
创建数据库，我这里是"myblog"，并制定"myblog"库的所有者是"myblog"。
```bash
CREATE USER myblog;
CREATE DATABASE myblog OWNER myblog;
```
![](2.png)
新建一个库，一是因为数据库超级管理员账户有风险，一些python框架会禁止使用postgres用户登陆的数据库。二是为了方便管理。
我习惯上起了一个跟项目名同名的用户和库，并让这个用户拥有操作这个库的权限。
3. 修改程序配置文件
config.py
```python
class DevelopmentConfig(Config):
    DEBUG = True
    SQLALCHEMY_DATABASE_URI = os.environ.get('DEV_SQLALCHEMY_DATABASE_URI') or \
        'postgresql+psycopg2://myblog:myblog@127.0.0.1:5432/myblog'
```
注意这几个"myblog"的含义分别是用户名、密码、要连接的数据库名。
4. 书写Roles、Users类
需求设计：
- 用户可以注册、登陆、登出、修改密码、邮箱认证
- 用户有不同类型和权限比如：一般用户、有发文权限的、有审核权限的管理员、超级管理员等。
- 文章发布、审核、删除  
因此设计 角色类Roles和用户类Users。Roles一对多Uses。  
app/models.py
```python
from . import db

class Roles(db.Model):
    """角色表"""
    __tablename__ = 'roles'
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(50), unique=True)
    users = db.relationship('User', backref='role', lazy=True)

    def __repr__(self):
        return '<Role {}>' % self.name

class Users(db.Model):
    """用户表"""
    __tablename__ = 'users'
    id = db.Column(db.Integer, primary_key=True)
    email = db.Column(db.String(100), unique=True, index=True)
    username = db.Column(db.String(100), unique=True, index=True)
    password_hash = db.Column(db.String(128))

    role_id = db.Column(db.Integer, db.ForeignKey('roles.id'))

    def __repr__(self):
        return '<User {}>'.format(self.username)
```
postgreSQL数据库内置user表，为了避免关键字冲突，我起表名"users",你也可以起"members"之类的。ORM框架能把\_\_tablename\_\_的值设置为表名，所以可以
class USER 但\_\_tablename\_\_设置为users，这里我个人习惯类名表名统一所以类名起的Users。
4. 创建表
在pycharm的python console中操作
```python
>>> from app.models import Roles,Users
>>> from app import db ,create_app
>>> db.create_all(app=create_app('default'))
```
注意需要引入上下文对象app、Roles、Users、db。（个人理解 Users类继承在SQLAlchemy类上，sqlalchemy的实例是db，db跟flask app实例绑定。所以先引入app实例，db就跟着有了，还需要让程序知道有Users、Roles这些类。）
可以看到表建好了
![](3.png)
（课下）另一种方法：写在代码中运行。参考文档[flask-sqlalchemy/引入上下文](http://www.pythondoc.com/flask-sqlalchemy/contexts.html?highlight=create_all)
## 注册
需求分析：一般的注册表单相信大家都体验过：用户名、邮箱、密码、重复密码、验证码、手机号之类的。  
(课下)现在，手机已开始取代邮箱作为注册用户名，因为手机号更容易记，安全性更高，更容易使用。但邮箱仍然举足轻重，作为账号绑定或安全验证和广告途径。我查了下暂时没找到免费的短信接口api，而邮箱是人人都有的，所以下面的教程依然采用邮箱作为验证方式。
这两者很相似，只是邮箱要用到邮件协议发送，短信需要请求api接口，相信学会一种另一种也没问题，课下可以改造项目以支持手机注册。数字验证码或拼图验证码请课下自己思考，这里为了简化项目不考虑此功能。
1. 书写注册表单
app/auth/forms.py
```python


```















## 登陆
### 哈希密码
[^1](课下)什么是哈希算法？参考链接（通俗 看前一分钟）[秒懂百科/什么是哈希算法](https://jingyan.baidu.com/article/1876c8524a5e2c890b1376fc.html)，(学术)[伯乐在线/常见hash算法的原理](http://blog.jobbole.com/106733/)
(课下)python内置hashlib库可以做hash运算 参考[廖雪峰/hashlib](https://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/0013868328251266d86585fc9514536a638f06b41908d44000)
1. 功能实现思路：密码为了安全需要hash加密后再存到数据库。用户登录时先将用户输入的密码hash，再到数据库查找比对。  
Werkzeug封装了两个简便的方法来完成密码加盐、加密、校验操作。
2. 为Users类添加加密、校验方法
```python
from werkzeug.security import generate_password_hash, check_password_hash

...

class Users(db.Model):
    ...
    
    password_hash = db.Column(db.String(120))

    role_id = db.Column(db.Integer, db.ForeignKey('roles.id'))

    @property
    def password(self):
        raise AttributeError('password_hash is not readable')

    @password.setter
    def password(self, password):
        self.password_hash = generate_password_hash(password)

    def verify_password(self, password):
        return check_password_hash(pwhash=self.password_hash, password=password)

    def __repr__(self):
        return '<User {}>'.format(self.username)
```
(课下)@property 不熟悉的参考[廖雪峰/使用@property](https://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001386820062641f3bcc60a4b164f8d91df476445697b9e000)
个人理解@property相当于java中的属性字段和字段的get方法二合一。这段代码新增方法"password"，被@property修饰后成为类的属性，为了安全，我们禁止访问"password"属性。
"password"的setter方法会用generate_password_hash加密用户密码，再存入password_hash属性中。  
(课下)如果你对这段代码不理解，也可以把业务逻辑操作放到视图函数中写。实现思路还是："新注册用户表单输入密码，后台接收参数，加密存入hash_password字段。登陆，先根据用户名从数据库取hash_password再根据用户输入hash后的值比对是否一样。"
3. (选做)在命令行中验证刚才的代码
打开系统终端，引入环境变量，进入flask终端 在交互式终端试验下代码
```bash
$ export FLASK_APP=manage.py        # windows下是 set FLASK_APP=manage.py
$ flask shell
```
如果你不想像上面 在终端创建表 先引入相关类再实例化app、db实例那么麻烦。安装flask包时已在系统注册了一个全局命令"flask",运行flask shell可以打开包含项目上下文环境的交互终端。  
确保先引入了环境变量。  
在flask shell中验证刚才的代码
```
>>> from app.models import *
>>> u = Users()
>>> u.password = 'aaa'
>>> u.password
Traceback (most recent call last):
  File "<console>", line 1, in <module>
  File "/Users/yangzheng/PycharmProjects/myblog/app/models.py", line 26, in password
    raise AttributeError('password_hash is not readable')
AttributeError: password_hash is not readable
>>> u.password_hash
'pbkdf2:sha256:50000$Zk72Vi9l$b8c45ff4563196db85fc1684526aefd5ad85137584ded621c899ee33c56de28c'
>>> u.verify_password('aaa')
True
>>> u.verify_password('bbb')
False
```
(课下)[为什么每次generate_pasword_hash值不一样](https://stackoverflow.com/questions/23432478/flask-generate-password-hash-not-constant-output#)
## 管理登陆会话 flask-login
flask-login插件为flask框架提供用户会话管理，包括登陆、登出、一定时间内记录用户会话等功能。
[flask-login中文文档](http://www.pythondoc.com/flask-login/)
[flask-login英文中文](http://flask-login.readthedocs.io/en/latest/)
1. 安装flask-login  
```bash
pip install flask-login
```
2. 修改 app/models.py
```python

```


[flask/doc/tutorial/views](http://flask.pocoo.org/docs/1.0/tutorial/views/)