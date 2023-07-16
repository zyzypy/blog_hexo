---
title: L11. 大型项目组织和蓝图
comments: true
toc: true
date: 2018-06-21 18:11:07
updated:
tags:
categories:
- flask
banner:
---
## 大型项目组织
### 介绍和改造思路
我们已经建立了几个项目"demo1 "、... 、"demo4" ,分别研究了"上下文和路由"、"jinja2"、"操作数据库"等方面。一个完整项目中这些功能都是需要的，还会有更多其它功能。如果所有功能逻辑都写在app.py中，代码将会很长难以维护。  
flask是灵活的，目录可以根据业务和个人习惯改变，但最开始，我建议你先按照这样来，下面是经验和最佳实践得出的目录结构，以后的实战项目将会基于这个架构。  
我们将会把这些代码按功能分类，并用包组织起来，项目根目录下的结构如下：
- app包 主要代码逻辑
    - main包 公共模块
        - forms.py 专门写表单的py文件
        - errors.py 异常处理（根据个人情况选择添加）
        - models.py 写数据库model的   
        - views.py 写视图函数和逻辑的 
        - \_\_inti\_\_.py  (实例化蓝图 引入view)
    - static 静态资源文件夹  
    - templates 模板文件夹  
    - \_\_inti\_\_.py （实例化flask app）
- config.py 配置文件 
- manage.py 项目运行入口和管理行为 
- requirement.txt 依赖包导出列表 
- test 单元测试 （根据个人情况选择添加）
- venv 虚拟环境（根据个人情况选择添加）
- migrations 数据库迁移脚本（根据个人情况选择添加）
下面开始改造。回顾之前的几个项目"demo1 "、... 、"demo4"，可以打开它们放在一边参考，适当复制粘贴从前项目的代码到今天的项目中，感受结构变化的过程。在改造的过程中，思考为什么这样变。  

### 1. 准备新建项目和新建文件  
新建一个项目例如"**demo5**"。按照上面所说的新建好包、文件夹、文件。下面的步骤将会填充内容。  
如果你没有用到venv、migrations等，可以不建立它，我为了演示目录结构将会建立内容为空的包、文件夹。
![](1.png)
### 2. 配置项
一种常见场景，公司开发中程序需要配置连接不同数据库，测试时连本地，上线时连服务器上正式的库。如果手动修改配置项，将会麻烦容易出错。  
下面开始书写config.py,之前的课程中我们直接在app.py中通过`app.config[KEY]=value`方式设置配置值。如果插件比较多，程序更复杂，几十个配置项、配置项需要经常变更，那么单独把配置提出来写在一个配置文件中，保持低耦合。
下面我们把配置按开发、测试和线上分类，并封装成类，这样改一个配置变量就能切换全部配置项。
config.py
```python
import os
basedir = os.path.abspath(os.path.dirname(__file__))

class Config:
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'hardToGuessbala@#dkjfi'
    SQLALCHEMY_TRACK_MODIFICATIONS = True

    @staticmethod
    def init_app(app):
        pass

class DevelopmentConfig(Config):
    DEBUG = True
    SQLALCHEMY_DATABASE_URI = os.environ.get('DEV_SQLALCHEMY_DATABASE_URI') or \
        'postgresql+psycopg2://postgres:postgers@127.0.0.1:5432/testdb'

class TestingConfig(Config):
    DEBUG = True
    # 测试和正式环境的配置值暂时假设
    SQLALCHEMY_DATABASE_URI = os.environ.get('DEV_SQLALCHEMY_DATABASE_URI') or \
                              'postgresql+psycopg2://postgres:postgers@192.168.1.101:5432/blog'
    pass

class ProductionConfig(Config):
    DEBUG = False
    SQLALCHEMY_DATABASE_URI = os.environ.get('DEV_SQLALCHEMY_DATABASE_URI')
    LOG_FILE = os.path.join(basedir, 'demo5.log')
    pass

config = {
    'development': DevelopmentConfig,
    'testing': TestingConfig,
    'production': ProductionConfig,
    'default': DevelopmentConfig
}
```
公共配置写在了Config类，下面的几个类继承它并写了自己的个性化配置，最后字典用key值映射到类。  
这样flask app实例生成的地方`from config.py import config`再`app.config.from_obj(config['default'])`就能引入开发配置，需要上线时只需修改`config['default']`为config`['production']`一个地方的值就能切换为线上配置。    
(课下)app.config有多种方法，可以从字典、json等多种形式读取配置。配置文件中也可以加判断来判断环境变量，自动载入相应配置。可以课下思考把config写成不同形式的。
`os.environ.get(key)`可以从环境变量读取值，暂时用不到可以字符串写死，上面用or连接更方便能同时适合两种情况。  
Config下init_app()方法可以写加载配置时要做的动作，比如从某个文件读取配置值，或做一些初始化工作，我们的项目暂时用不到暂时pass。  
我们现在主要在自己电脑上学习，暂时不用考虑TestingConfig和DevelopConfig中的具体内容，暂时pass略过。  
配置的整体结构就是这样，未来随着实战开发会不断往里面添加东西。
### 3. 使用包来管理
一个文件夹下有 \_\_init\_\_.py 那么这个文件夹称为一个"包"package，可以层级访问，用import语句引入我们想要的东西。  
python引用包路径和操作系统终端中相对路径语法基本相同，略有区别：
- 同一级`.`表示。比如app/main/view.py 引用 app/main/\_\_init\_\_.py中main蓝图实例是 `from . import main`  
- 上一级`..`表示。比如app/main/view.py 引用 app/\_\_init\_\_.py中db实例是 `from .. import db`  
- 终端中语法。同一级是 `./xxx.js` ，父级是 `../xxx.css`
可见相同点都是用一个 点表示同级别目录，两个点表示上一层目录；区别是python包引用语法 点后面没有斜杠`/`。  
搞不清楚的同学借助ide提示和先类比本节示例代码比葫芦画瓢即可。  
个人理解包是功能增强的文件夹，可以进行一些行为控制和预处理。  
参考文章（课下）：
[Python模块包中\_\_init\_\_.py文件 精髓](https://blog.csdn.net/huochuangchuang/article/details/49366555)
[Python \_\_init\_\_.py 作用详解](https://www.cnblogs.com/Lands-ljk/p/5880483.html)
### 4. 程序工厂函数
这一步主要解决两个问题：
一.配置是变动的，开发或测试或上线，希望简单地改变传入参数，flask app实例就能加载不同配置。
二.flask-sqlalchemy插件需要接收flask app实例作为参数，如果是之前的项目结构，app.py和model.py同级，model.py import app实例时报错app还未生成。所以app实例化要在model.py引用之前。
工厂函数是软件设计模式的一种思想，参考文章（课下）[工厂方法和工厂函数](https://www.zyzypy.com/flask/flask-flaskSQLAlchemy/#Flask-SQLAlchemy)。
下面为 **app/\_\_init\_\_.py**添加代码：  
```python
from flask import Flask, render_template
from flask_bootstrap import Bootstrap
from flask_sqlalchemy import SQLAlchemy
from config import config

bootstrap = Bootstrap()
db = SQLAlchemy()

def create_app(config_name):
    app = Flask(__name__)
    app.config.from_object(config[config_name])
    config[config_name].init_app(app)
    
    bootstrap.init_app(app)
    db.init_app(app)
    
    return app
```
create_app() 函数解决了问题一，它封装了之前flask生成app实例的代码，它接收config_name参数，加载相应的配置。  
写在\_\_init\_\_.py下解决了问题二，因为运行manage.py服务时import app包会先运行app/\_\_init\_\_.py生成app实例，这样app/models.py里就可以引用app实例生成db实例了。
这个代码的结构：首先引用了flask、flask插件、config文件引入config对象。然后实例化插件。然后实例化flask app。然后绑定插件。最后返回flask app实例。如果以后添加更多功能和插件，也按照这个结构加入。


### 5.👉蓝图Blueprint
蓝图这块教程的1-7步骤要完整做完运行完再慢慢理解，比如第一步的 url_prefix参数直到第六步结果时才好理解。  
蓝图适合更为大型的项目，蓝图寓意功能规划，代码按大功能块划分。  
比如一个大型电商项目，有后台管理、客户管理、商品售卖、进销存管理、供应商管理等多模块。上述的架构依然会难以维护。
如果我们为了扩展功能新建几个与main包平级的包 admin，crm，sale，supplier等来按大块功能区分模块，每个包的结构差不多，下面都有views.py、models.py、forms.py等。  
这样的结构称为蓝图。
1. 引入和蓝图实例化
    app/main/\_\_init\_\_.py
    ```python
    from flask import Blueprint
    
    main = Blueprint('main', __name__, url_prefix='/main')
    
    from . import views
    
    # print(__name__)   # 这个包被引用时__name__的值为"app.main"
    ```
    首先从flask包引入Blueprint类。
    Blueprint()的第一个参数是名字。
    第二个参数是内置变量\_\_name\_\_,可以打印它并在项目最后启动后查看结果。
    第三个参数是url前缀，这个值也可以在注册蓝图的方法中添加。最后运行程序时看结果你就会知道这个url前缀代表什么。
    习惯上，我喜欢蓝图实例名、蓝图第一个参数名、url前缀都起成一样的以免混淆。
    views.py中将会引用蓝图实例，路由最终会注册到flask app实例中，所以这里蓝图实例化后会引用views。
2. 将各蓝图实例注册到flask app实例中去
app/\_\_init\_\_.py
```python
...

def create_app(config_name):
    ...

    from .main import main as main_blueprint
    app.register_blueprint(main_blueprint)
    from .admin import admin as admin_blueprint
    app.register_blueprint(admin_blueprint)

    return app
```
3. 在app/main包下的views中书写路由视图函数
蓝图区分依靠以下机制：
蓝图实例化时的url_prefix。  
蓝图实例化的对象，这里是main，那么路由就变成了"@main.route()"。  
注意url_for()的变化，原来是`url_for('index')`, 现在是 蓝图实例名.视图函数名 即`url_for('main.index')`。  
app/main/views.py
```python
from flask import url_for
from . import main

@main.route('/index')
def index():
    # print(url_for('main.index'))
    return '/main/index'
```
5. 再写一个功能模块，进一步展示蓝图的作用
    假如我们的程序有后台功能，新建并书写一个结构和代码跟app/main包相似的app/admin包。新建"**admin**"包和文件后项目结构如下:
    ```bash
    .
    ├── app
    │   ├── __init__.py
    │   ├── admin
    │   │   ├── __init__.py
    │   │   ├── forms.py
    │   │   ├── models.py
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
    ├── migrations
    ├── requirements.txt
    ├── test
    │   └── __init__.py
    └── venv
    
    8 directories, 13 files
    ```
    app/admin/\_\_init\_\_.py
    ```python
    from flask import Blueprint
    
    admin = Blueprint('admin', __name__, url_prefix='/admin')
    
    from . import views
    ```
    app/admin/views.py
    ```python
    from . import admin
    
    @admin.route('/index')
    def index():
        return '/admin/index'
    ```
    app/\_\_init\_\_.py
    ```python
    ...
    
    def create_app(config_name):
        ...
        
        from .main import main as main_blueprint
        app.register_blueprint(main_blueprint)
        from .admin import admin as admin_blueprint
        app.register_blueprint(admin_blueprint)
    
        return app
    ```
6. (课下)蓝图的静态资源访问
不同蓝图可以有自己的templates和static文件夹，不同路由可以对应完全不同的前端模板样式。但是这种情况不多见，一般一个网站就是一种风格。
另外官方文档这里也不太清楚，很多人迷惑。这里先简单说下就是蓝图里的路由会先查找蓝图里的静态资源文件夹，找不到的话会去公共静态资源文件夹（也就是跟蓝图main包平级的模板文件夹里找），
所以会出现index能看到，蓝图里的模板却找不到的情况。  
（todo 代码示例）
蓝图的内容较多，前面仅介绍基础用法，更多参数设置和功能详见[]()
### 6. server脚本
manage.py
```python
import os
from app import create_app

app = create_app(os.getenv('FLASK_CONFIG') or 'default')

if __name__ == '__main__':
    app.run()
```
实例化flask app或做一些初始化操作。  
运行，ide中manage.py右键run或终端`python manage.py`，本节项目跟之前的小项目一样启动。
访问`/main/index`和`/admin/index`结果
![](2.png)
![](3.png)
所以完整的url是 "**url_prefix + main.route()**"即"/main"+"/index"。  
url_prefix也可以不定义，但为了路由多了之后好维护习惯上每个蓝图功能块指定一个url前缀。  
对照代码和结果，进一步理解蓝图的用法。
### 7. 依赖包列表
终端运行：
先cd到项目根目录
```bash
pip3 freeze > requirements.txt
```
看到依赖包以名字、版本的列表形式列出
![](4.png)
这样线上部署到新机器或别人下载你的代码后就可以快速安装依赖包，而不用一个一个install
```bash
pip3 install -r requirements.txt
```
大于号表示导出，-r表示迭代，具体linux部分会学习。
### 8.单元测试
暂时先不写

## 本节课完成后的代码
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
我们的实战项目将使用这个结构。  
更为大型的项目可以model、templates、static也可以划分到每个蓝图下。  

## 取舍
从轻到重：
1. 所有内容全部写在app.py中，就想之前几节课那样。适合小型项目。
2. 如本节课前半部分讲的架构，如本节课第一部分。适合中型项目。
3. 第二步的基础上加上了蓝图，如本节课后半部分。适合大型项目。
下面的实战将采用第3步这个架构，如果未来你学习到django，你会发现django与使用蓝图时的flask框架项目组织非常相似😂。  
在flask和django用哪个纠结之前，建议你还是先专心学习flask，两个都使用有经验后就能根据实际项目判断用哪个合适了，或是用flask写出正合适自己业务的目录组织。