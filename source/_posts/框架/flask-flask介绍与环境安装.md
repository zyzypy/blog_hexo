---
title: L1. Flask介绍与准备开发环境
comments: true
toc: true
date: 2018-05-29 14:59:09
updated:
tags:
- python
- web
- flask
- pycharm
- helloworld
- virtualenv
categories:
- flask
banner:
---
## 介绍
### flask
Flask（直译：烧杯）是一个使用 Python 编写的轻量级 Web 应用框架。其http部分来自Werkzeug，模板引擎则使用Jinja2 。
Flask也被称为 “microframework” ，因为它使用简单的核心，用（扩展）extension 增加其他功能。Flask没有默认使用的数据库、表单工具。

“微”(micro)并不表示你需要把整个Web应用塞进单个Python文件，也不意味着 Flask 在功能上有所欠缺。微框架中的“微”意味着 Flask 旨在保持核心简单而易于扩展。
Flask 不会替你做出太多决策——比如使用何种数据库。而那些 Flask 所选择的——比如使用何种模板引擎——则很容易替换。除此之外的一切都由可由你掌握。
默认情况下，Flask 不包含数据库抽象层、表单、表单验证等功能。然而，Flask 支持用扩展来给应用添加这些功能。
众多的扩展提供了数据库集成、表单验证、上传处理、开放认证技术等功能。Flask 也许是“微小”的，但少即是多，它已准备好在需求繁杂的生产环境中投入使用。

### 其它流行框架与对比

- django 
    应该是最出名的Python框架，GAE甚至Erlang都有框架受它影响。Django是走大而全的方向，它最出名的是其全自动化的管理后台：只需要使用起ORM，做简单的对象定义，它就能自动生成数据库结构、以及全功能的管理后台
- flask
    Flask是一个使用Python编写的轻量级Web应用框架。基于Werkzeug WSGI工具箱和Jinja2
    模板引擎。Flask也被称为“microframework”，因为它使用简单的核心，用extension增加其他功能。Flask没有默认使用的数
    据库、窗体验证工具
- web2py
    Web2py是一个为Python语言提供的全功能Web应用框架，旨在敏捷快速的开发Web应用，具有快速、安全以及可移植的数据库驱动的应用，兼容Google App Engine。
- Tornado
    特点是异步Web框架，适合做长连接，它不仅是Web框架，还是一个Web Server，同时提供了异步库。
- Sanic
    它是一个类似Flask的异步web框架，基于Python3.5的 async/await 原生异步语法实现的。根据官方文档所说，性能非常高 

### 选择
其它框架的缺点：tornado异步web框架虽然理论上效率高一点，但实际中差别不大，而且代码并不pythonic。sanic尚年轻，性能提升主要依赖python3.5的新特性，还不够完整稳定。
综合最推荐的是flask和django框架。一个小而微，一个大而全。但他们两者原理是非常相似的，学会一个再学另一个非常迅速。
Flask在github start数已居第一位，足够流行。核心简单，扩展可以复杂，适应从简单到复杂的需求，适合web学习入门和进阶，因此作为本课程的主题🎉。

## 环境安装
### 本课程使用环境
- 操作系统：windows10 x64  
- python解释器：python3.6.5 x64 
- 集成开发工具：pycharm 2018 Professional

最好与课程环境保持一致，这样出现问题容易解决。mac用户提前沟通。
下载缓慢的同学自行搜索其它网站下载或从本人搭建的文件服务下载[download.ngrok.zyzypy.com](http://download.ngrok.zyzypy.com)

### python解释器
python官网[python.org](https://www.python.org/)
本课程默认学习者已具备了python基础部分的知识：类、函数、操作数据库、包的概念，如果不熟悉请移步至基础课程[python基础课程](#)  
相信基础课程之后已安装过python解释器，请确认python解释器工作正常。

### pycharm
#### 介绍
web开发中，将涉及前端编码、静态资源、操作数据库、web框架、配置、运行服务等多方面内容。普通文本编辑器和单个.py文件不能满足需求，需要一个集成开发工具帮助我们。  
pycharm是由jetbrain公司打造的python开发专用工具，具有语法检查、代码提示、数据库可视化操作、部署运行等功能，是python开发领域最流行的编辑器。
#### 下载安装
1. 打开官网[jetbrains官网pycharm](https://www.jetbrains.com/pycharm/download/#section=windows)
2. 选择对应平台和版本下载，请选择professional专业版
 ![](pycharm2.png)
3. 安装过程
一路next，勾选桌面快捷方式和下载jre
![](pycharm3.png)
![](pycharm4.png)
4. 安装完成后打开  
没有settings文件,点击ok  
![](pycharm5.png)
看到激活窗口  
![](pycharm6.png)

#### 激活
有能力请支持正版。虽然community社区版免费，但专业版的数据库、前端编辑功能很方便。  
1. 百度"lanyu" 打开网页 [IntelliJ IDEA 注册码](http://idea.lanyus.com/) 复制激活码
2. 使用前请将**“0.0.0.0 account.jetbrains.com”**添加到hosts(C:\Windows\System32\drivers\etc\hosts)文件中。  
![](pycharm10.png)
3. 填入ide active_code
![](pycharm7.png)
4. 激活成功后随意选择一种界面风格，程序启动
![](pycharm8.png)
![](pycharm9.png)


## Hello World
### 创建一个新项目
1. 创建项目  
    ![](pycharm11.png)
    左侧目录选择Pure Python
    起项目名：`hello_world`
    选择解释器：选择existing interpreter(已存在的解释器)单选🔘按钮 ，如果ide未识别手动选择路径"python解释器目录/bin/python.exe"
    ![](pycharm12.png)
2. 新建 hello.py 文件
    项目生成了一个空文件夹，External Library是刚才选择的解释器引用  
    编辑器左侧资源导航 项目根目录右键>new>python file 新建一个.py文件
    ![](pycharm14.png)
    ![](pycharm15.png)

### 安装flask包
1. cmd或powershell命令行输入（也可以使用ide自带的终端）
    ```shell
    > pip install flask
    ```
    或使用ide自带的图形工具安装
    ![](pycharm16.png)
    ![](pycharm20.png)
    ![](pycharm21.png)
2. 安装过程
    可以看到下载并安装了几个包，无报错，最后"succefuly installed flask ...."为成功
    ![](pycharm17.png)
    ![](pycharm18.png)
3. flask包解释
    查看已安装的包  
    ```shell
    > pip list 
    ```
    ![](pycharm19.png)
    虽然只install flask，但还出现了其它几个包。它们是flask的依赖包，flask的一些功能基于它们。它们的大概作用是：
    - Flask：flask的核心组成
    - Jinja2：前端模板渲染
    - MarkupSafe、itsdangerous：安全相关，防CSRF攻击等
    - pycryptodome: 加密
    - Werzeug：http封装

### 代码
编辑hello.py
```python

from flask import Flask     # 引入Flask类
app = Flask(__name__)       # app是Flask的实例，它接收包或者模块的名字作为参数，但一般都是传递__name__。就想一个普通类操作要先生成一个实例

@app.route('/')             # 用装饰器定义路由"/" 表示匹配"域名/"或"域名/index"，也可以写作"/index"
def hello_world():          # 视图接收请求并进行相应
    return 'Hello World! '   # 返回了字符串helloworld

if __name__ == '__main__':      # 这个.py文件自己执行时 __name__就等于'__main__'
    app.run(debug=True)    # 执行app.run就可以启动服务了
```
![](pycharm22.png)

详细释义（课下阅读）  
```python
#!/usr/local/bin/python  
# coding=utf-8  
  
from flask import Flask  
app = Flask(__name__)  
 
@app.route('/')  
def hello_world():  
    return 'Hello World!'  
  
if __name__ == '__main__':  
    app.run(host='0.0.0.0',port=5000)  
    
"""
第4行，引入Flask类，Flask类实现了一个WSGI应用 
第5行，app是Flask的实例，它接收包或者模块的名字作为参数，但一般都是传递__name__。 
    让flask.helpers.get_root_path函数通过传入这个名字确定程序的根目录，以便获得静态文件和模板文件的目录。 
第7~9行，使用app.route装饰器会将URL和执行的视图函数的关系保存到app.url_map属性上。 
    处理URL和视图函数的关系的程序就是路由，这里的视图函数就是hello_world。 
第11行，使用这个判断可以保证当其他文件引用这个文件的时候（例如“from hello import app”）不会执行这个判断内的代码，也就是不会执行app.run函数。 
第12行，执行app.run就可以启动服务了。默认Flask只监听虚拟机的本地127.0.0.1这个地址，端口为5000。 
    而我们对虚拟机做的端口转发端口是9000，所以需要制定host和port参数，0.0.0.0表示监听所有地址，这样就可以在本机访问了。 
    服务器启动后，会调用werkzeug.serving.run_simple进入轮询，默认使用单进程单线程的werkzeug.serving.BaseWSGIServer处理请求， 
    实际上还是使用标准库BaseHTTPServer.HTTPServer，通过select.select做0.5秒的“while TRUE”的事件轮询。 
    当我们访问“http://127.0.0.1:9000/”,通过app.url_map找到注册的“/”这个URL模式,就找到了对应的hello_world函数执行，返回“hello world!”,状态码为200。 
    如果访问一个不存在的路径，如访问“http://127.0.0.1:9000/a”,Flask找不到对应的模式，就会向浏览器返回“Not Found”，状态码为404 
"""

```
### 运行
1. hello.py空白处右键 run 。其实相当于命令行的`python hello.py`。
![](pycharm23.png)
2. 监听服务已启动。
![](pycharm24.png)
3. 打开浏览器。地址栏输入[域名：端口号] ，看到"hello world"🎉。 本课结束。
![](pycharm25.png)


## virtual env（课下）
命令行操作和virtual env的概念对新手并不友好。后面的学习中我们只有一个项目，公司团队许多也主要一种环境。所以virtual env的学习作为选修。
### 介绍
介绍：venv模块提供了创建轻量级“虚拟环境”，提供与系统Python的隔离支持。每一个虚拟环境都有其自己的Python二进制（允许有不同的Python版本创作环境），
并且可以拥有自己独立的一套Python包。他最大的好处是，可以让每一个python项目单独使用一个环境，而不会影响python系统环境，也不会影响其他项目的环境。
原理：其实就是又copy了一份python文件夹，把新位置的python解释器路径添加到环境变量中。
### 操作
1. 在当前目录创建虚拟环境，终端输入
```python
python -m venv      # venv是虚拟环境的目录名 任意起名
```
或者使用pycharm自带的图形工具  
file>settings>project>interpreter  设置⚙ > add local 新建一个venv  
![](virtualenv1.png)
![](virtualenv2.png)
2. 激活环境
```python
$ source <venv>/bin/activate        # mac linux
C:> <venv>/Scripts/activate.bat     # windows cmd
PS C:> <venv>/Scripts/Activate.ps1  # windows PowerShell
```
激活成功后命令行前面有括号，名字是virtual env的名字。  
这时`python`进入交互终端或`pip list`用的是新建的venv环境，可以看到库中只有pip和setuptools，没有flask，编辑器也报没有找到包的红线错误。
在新虚拟环境下可以安装包，这样跟原来的本地环境就互相独立了。在有多个环境差别较大的项目时virtual env就有用了。
![](virtualenv3.png)
![](virtualenv4.png)

## 其它
本课目标：
配好环境、初识flask、一般了解virtual env环境
关于http请求与响应、域名 端口、flask深层代码原理，暂时不用考虑。对`app = Flask(__name__)`、`@app.route('/')`这些代码有疑问不用担心，
后面会逐渐习惯语法和深入理解。

## 延伸阅读

[windows修改hosts文件](https://jingyan.baidu.com/article/c45ad29c1dd610051653e25c.html)
[hellworld详细解析](https://blog.csdn.net/christopherchen/article/details/54636846)
[Python3 venv 创建虚拟环境](https://blog.csdn.net/geekun/article/details/51325383)

## license
🚫禁止商用  转载注明作者
