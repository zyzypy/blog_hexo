---
title: L9.ORM和FlaskSQLAlchemy
comments: true
toc: true
date: 2018-06-11 15:01:42
updated:
tags:
- python
- flask
- 插件
- sqlalchemy
- 数据库
- 驱动
categories:
- flask
banner: http://flask-sqlalchemy.pocoo.org/2.3/_static/flask-sqlalchemy-title.png
---
## 介绍
### ORM
我们已经在基础课程学习过sqlite数据库的操作，通过驱动包新建连接、读取数据、增删改查、关闭游标和连接。
然而在后面的postgre数据库和完备复杂的web项目中，数据库读写逻辑将会复杂和频繁，再用之前的方法会有重复代码，为了提高工作效率，对*数据库驱动和增删改查操作封装*，引入orm。  
对象关系映射（Object Relational Mapping，简称ORM）模式是一种为了解决面向对象与关系数据库存在的互不匹配的现象的技术。简单的说，ORM是通过使用描述对象和数据库之间映射的元数据，将程序中的对象自动持久化到关系数据库中。
orm的主要优点是不用手写sql，使代码清晰整洁，读写数据库方便，提高编码效率。缺点是封装了一层，效率、出问题的排错不如不使用orm方便。
### ORM选择和SQLAlchemy介绍
不同框架有不同的ORM框架，比如django集成了自己的ORM框架。flask灵活可以与多种ORM框架搭配，其中最流行的是"**SQLAlchemy**"，其它的ORM如轻量的peewee可以课下尝试。  
"**SQLAlchemy**（alchemy 炼金术）" 为重型驱动包，功能强大支持常见数据库的驱动，数据迁移，多数据库连接等。  
[SQLAlchemy官网](http://www.sqlalchemy.org/)（PS:官方文档近千页🙄，过于详细。sqlalchemy流行的一个原因作者跟flask关系较大）
"flask-sqlalchemy" 是SQLAlchemy在flask框架中集成的版本。  
[flask-sqlalchemy文档](http://flask-sqlalchemy.pocoo.org/2.3/)
### 数据库驱动和选择
sqlalchemy支持多种数据库，ORM框架封装了数据库操作，如果你配置它连接postgre数据库，那么它就会调用相应的数据库的驱动，所以实际上操作数据库的还是数据库驱动（database adapter）。
就像上节我们使用数据库图形管理工具datagrip时先下载了java语言编写的连接postgre数据库的jdbc驱动；就像import sqlite3操作sqlite数据库。
所以还需要安装数据库驱动，常见的数据库驱动如"psycopg2"、"pg8000"、"py-postgresql"等，这里我们选择最流行的"psycopg2"。  
[psycopg官网](http://initd.org/psycopg/)"psycopg2(直译：pg数据库心理医生)"是一个python语言驱动postgresql数据库的工具包。  
[SQLAlchemy/Dialect/PostgreSQL](http://docs.sqlalchemy.org/en/latest/dialects/postgresql.html)sqlalchemy文档中使用python驱动的相关部分文档。  

## 准备
1. 新建一个项目专门来练习数据库驱动，例如取项目名为"demo4"
```bash
.
├── app.py
├── static
└── templates

2 directories, 1 file
```
2. 安装postgre数据库驱动包 "psycopg2"
（todo win下安装未测试 可能因为调用c库需要去找编译好的whl）
```bash
pip install psycopg2
```
3. 安装flask ORM扩展 "Flask-SQLAlchemy"
```bash
pip install Flask-SQLAlchemy
```
![](1.png)
4. 新建测试数据库"testdb"
命令行方式：
终端登陆后到数据库终端中操作
```
$ demo4 yangzheng$ psql -U postgres
输入密码
postgres=# CREATE DATABASE testdb OWNER postgres;
```
图形工具方式：
使用了pycharm集成的数据库工具，跟上节datagrip使用一样。还尝试了数据库自带的 pgAdmin4，这个工具有问题😠详见文末。
![](10.png)
![](11.png)


## 传统写法
下面将使用psycopg2包写一个 从数据库连接、游标操作、sql增删查改、到关闭游标和数据库连接的基本脚本。
**请一定重视基本写法，理解基本写法**。不然使用ORM框架SQLAlchemy后虽然代码少了但会不理解卡住。
  
新建 "testdb.py ",代码如下
```python
"""
psycopg2驱动数据库示例 （不使用ORM）
"""
import psycopg2


# 连接数据库，传出 要操作的数据库名、用户名、密码、数据库服务所在的ip和端口号
connect = psycopg2.connect(database='testdb',
                           user='postgres',
                           password='postgres',
                           host='127.0.0.1',
                           port=5432)

# 获得游标
cursor = connect.cursor()

# 创建表
cursor.execute(
    '''
        DROP TABLE IF EXISTS public."employee";
        CREATE TABLE public."employee"
        (
        id serial NOT NULL,
        username varchar(20) NOT NULL,
        password varchar(20) NOT NULL,
        nickname varchar(20),
        CONSTRAINT employee_pkey PRIMARY KEY (id)
        );
    '''
)


# 插入
row = cursor.execute(
    ''' INSERT INTO public."employee" (username, password, nickname) 
        VALUES ('用户1', 'test', '用户1');
    ''')

# 更新
row = cursor.execute('''
    UPDATE employee set nickname='昵称已修改' WHERE username='用户1';
''')

# 提交事务
connect.commit()

# 查询
cursor.execute(''' SELECT * FROM employee; ''')

# 结果集
rows = cursor.fetchall()
for row in rows:
    print(row)

# 关闭游标和数据库连接
cursor.close()
connect.close()
```
pycharm中 testdb.py 中空白处右键run运行这个脚本，这个脚本是单独运行试验的，与我们建立的flask项目并无直接关系，也可以写单独的py文件测试。  
运行结果和数据库工具中查看结果，脚本中的sql语句都执行成功了。
![](12.png)
![](13.png)

下面详细分析一下过程：
1. 引入 psycopg2 包
    数据库能被不同语言驱动，所以数据库有自己的一套通用API接口，驱动包做的事情就是建立python语言与数据库驱动之间的桥梁。
2. 连接数据库
    几个主要参数database、user、password、host、port。  
    database使用刚才创建的"testdb"。user和password使用"postgres"用户名和密码，因为创建数据库时已指定owner为postgres用户，习惯上来说创建一个数据库同名的用户比较好，这里为了简单直接指定owner为超管用户postgres。  
    host是服务器地址，现在数据库装在本机电脑上所以是"localhost"，将来部署在服务器上就是ip地址。  
    port使用默认端口5432。  
    另一种写法URI的形式 `postgresql+psycopg2://user:password@host:port/dbname[?key=value&key=value...]` ，下面flask-sqlalchemy将使用。  
    连接失败的同学先试试命令行能不能登陆，数据库的拥有者是否正确。  
    方法返回一个数据库会话，表示程序跟数据库连接中。
3. 获得游标
    从连接获得游标，打比方就像鼠标，我们在操作excel时，点到那一行才能进行操作。cursor比较关键的方法是execute()执行sql和fetchall()获得查询的数据。
4. 创建表
    可以放在准备过程中做，使用图形化工具建表。
5. 增
    数据库操作主要是"增删查改"，英文缩写"CURD"。这里insert了一条测试数据。
6. 改
    修改刚刚插入的那条测试数据。
7. 提交事务
    打比方就想"保存"和"确定对话框"。游标进行的所有修改，都在暂存区中，"commit 提交"之后，才真正写入数据库。"提交"机制的作用是如果写入失败，就会"回滚"，这是数据库的事务性和原子性。
    查询操作不需要提交。增、改、删操作需要提交生效。
8. 查
    查询数据表
9. 获取结果
    主要方法有`cursor.fetchone()`,`cursor.fetchall`,fetchone的话一条tuple类型,fetchall的话list类型，元素是tuple。无值返回`None`。
    返回值list，元素是tuple类型，内容是表中一行的结果。结果集形如[(1, '用户1', 'password', '昵称' ),(2, '用户2', 'password', '昵称' )] 循环打印出来。
    如果想返回元素是dict类型的结果，设置参数 第2步 psycopg2.connect(cursor_factory=psycopg2.extras.DictCursor, **kwargs)则返回字典结果集形如 "[({'id':1, 'username':'用户1', 'password':'password', 'nickname':'昵称'}),()]"。
    这样循环后字典语法取值更方便。
10. 关闭连接
    与数据库的会话结束，释放资源。可以直接关闭连接，游标也会关闭。不关闭会导致资源占用上升。





## Flask-SQLAlchemy
### URI形式连接数据库
URI(Uniform Resource Identifier)，统一资源标识符，比url的含义更为广泛。url是uri的子集，比如 "static/css/main.css" 也属于uri定位。  
修改app.py ,引用包和配置URI,修改后的代码如下
    ```python
    from flask import Flask
    from flask_sqlalchemy import SQLAlchemy
    
    
    app = Flask(__name__)
    app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://postgres:postgres@localhost:5432/testdb'
    app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = True
    db = SQLAlchemy(app)       
    
    
    @app.route('/')
    def hello_world():
        return 'Hello World!'
    
    class User(db.Model):
      id = db.Column(db.Integer, primary_key=True)
      username = db.Column(db.String(80), unique=True, nullable=False)
      email = db.Column(db.String(80), unique=True, nullable=False)
    
      def __repr__(self):
          return '<User {}>'.format(self.username)
    
    if __name__ == '__main__':
        app.run()
    ```
详细解释：
0. 由于下节会讲大型项目组织架构，所以model类并没有写在单独的文件夹下，如果新建model.py会涉及无法`from app.py import app`的问题，为了方便暂时写在app.py中。  
1. 首先从扩展包中引用SQLAlchemy类。还可以写成`from flask.ext.sqlalchemy import SQLAlchemyy`，但**不推荐**，这是flask扩展引用的老式写法，其它的扩展包可能已经不支持这种写法。
2. 配置URI，URI中包含了"什么数据库"、"用户名"、"密码"、"数据库服务的ip地址"、"端口号"、"用哪个库"等连接必要信息。
可以看出，数据库连接的语法其实是psycopg中的。  
主流数据库URI配置语法如[flask-sqlalchemy/connection-uri-format文档](http://flask-sqlalchemy.pocoo.org/2.3/config/#connection-uri-format) ：  
```python
# Postgres:
postgresql://scott:tiger@localhost/mydatabase    
# MySQL:
mysql://scott:tiger@localhost/mydatabase
# Oracle:
oracle://scott:tiger@127.0.0.1:1521/sidname
# SQLite  Unix/Mac (note the four leading slashes)
sqlite:////absolute/path/to/foo.db
# SQLite  Windows (note 3 leading forward slashes and backslash escapes)
sqlite:///C:\\absolute\\path\\to\\foo.db
# SQLite  Windows (alternative using raw string)
r'sqlite:///C:\absolute\path\to\foo.db'
```
解释：基本形如 **"[数据库]://[用户名]:[密码]@[ip地址]:[端口号]/[要使用的那个数据库名]"**   
scott和tiger起源自oracle数据库内置的学习用测试数据库，指代"用户名 username"和"密码 password"。  
postgre的URI还可以写成"postgresql+驱动名://scott:tiger@localhost/mydatabase", 驱动名为 psycopg2(默认)或pg8000等，默认psycopg2可以省略。
3. SQLALCHEMY_TRACK_MODIFICATIONS配置项指传统写法的"commit"自动提交，设置True可以少写行代码，但你的业务逻辑需要事务回滚的话就应该设置False。  
3. 注册flask app实例到flask-sqlalchemy扩展上，还可以写成`SQLAlchemy.init(app)`。传入flask app实例，得到了db实例。flask和SQLAlchemy两者绑定到了一起。
4. 新建一个User类并继承SQLAlchemy类，并从flask-sqlalchemy包引入了字段和验证相关的类如Column String Required等，这里是通过db实例引入。  
5. \_\_repr\_\_ 不重要，只是打印类信息时多一点信息。
6. 🤔回顾之前的传统写法，思考SQLAlchemy做了什么事情。想想写的类对应的sql是什么  
这个扩展的大概原理：跟之前学习的flask-WTForm包很像，都是先从包引用基本类，然后引用一些字段属性类，这些包中的代码会把python类翻译成想要的结果，比如wtform翻译成了前端html，而sqlalchemy翻译成了sql。

### 生成数据库
- 方法一：从交互式命令行
    生成数据库和增删改查的代码当然可以写在app.py里面，
    但只要获取db实例后，就已经可以进行数据库操作，在交互式命令行我们可以更快地练习和验证想法。  
    在普通终端下app被系统判断为找不到路径或判断为其它东西，下面是失败情况。
    ![](14.png)
    所以建议在pycharm自带的 "python console"中操作，pycharm已经帮我们把项目路径添加到环境变量中去。打开pycharm自带的"python console"并写入下面代码  
    ```bash
    >>> from app import db
    >>> db.create_all()
    ```
    ![](15.png)

- 方法二：写在代码中
    如果你在交互式命令行引用db不成功，也可以将`db.create()`写在app.py中，运行一次后再注释掉。注意pycharm2018.1.3版本采取了` python -m flask run` 和添加环境变量的方式运行项目，
    导致不走` if __name__ == '__main__'`中的代码，所以我把这句话放到了实体类的后面。或采用`python app.py`方式运行。
    ```python
    class User(db.Model):
      id = db.Column(db.Integer, primary_key=True)
      username = db.Column(db.String(80), unique=True, nullable=False)
      email = db.Column(db.String(80), unique=True, nullable=False)
    
      def __repr__(self):
          return '<User {}>'.format(self.username)
    
    db.create_all()
    
    if __name__ == '__main__':
        app.run()
    ```
两种方法最终都调用`db.create_all()`生成了数据库表
![](16.png)
删除所有表的方法是`db.drop_all()`。

### 进行一些CURD操作
以交互式命令行中演示为例
1. 引入db实例和User类
```bash
>>> from app import db
>>> from app import User
```
2. 实例化两个User类的对象
```bash
>>> admin = User(username='admin', email='admin@example.com')
>>> guest = User(username='guest', email='guest@example.com')
```
3. 添加并提交
```bash
>>> db.session.add(admin)
>>> db.session.add(guest)
>>> db.session.commit()
```
session表示会话，commit()表示提交，不提交的话add是不会生效的，如果某一个add语句插入错误或失败，那么一个session下的所有语句都不生效。  
数据库的这种回滚操作叫做"事务"，为了确保数据的正确性。  
`app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = True` 这个设置表示是否自动commit， True为自动提交，所以这里可以省略`db.session.commit()`。 
4. 查找
```bash
>>> User.query.all()
[<User u'admin'>, <User u'guest'>]
>>> User.query.filter_by(username='admin').first()
<User u'admin'>
```
5. 🤔结果和思考：ORM框架SQLAlchemy替我们做了什么？
打开数据库表，查看建表sql和已保存的数据，结合过程结果，结合pycharm的python console为我们提供了变量查看功能。
![](17.png)
![](18.png)
![](19.png)
可以看到：
- 后端db.Column 代表 数据库表的一列  
- 后端db.String(80) 转换为 sql建表语句中的 varchar(80)
- 后端unique  转换为 sql中的constraint唯一约束
- 后端nullable 转换为 sql中的 not null
- 每一个User实例 代表 数据库中的一行数据
- 实例.add()  代表 执行insert语句，实例的数据和值会转换成insert语句的 列名=value。
- 实例.commit() 代表 数据库的事务提交
- 类.query 转换成 sql中的查询语句 select * from "User"。
- 类.query.filter_by(username='admin') 转换成查询语句中的where条件。

### 外键关系
下面是"一对多"的例子
app.py 添加Post类和如下代码
```python
import datetime

...

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(80), nullable=False)
    body = db.Column(db.Text, nullable=False)
    pub_date = db.Column(db.DateTime, nullable=False, default=datetime.datetime.now())

    category_id = db.Column(db.Integer, db.ForeignKey('category.id'), nullable=False)
    category = db.relationship('Category', backref=db.backref('posts', lazy=True))

    def __repr__(self):
        return '<Post {}>'.format(self.title)

class Category(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(50), nullable=False)

    def __repr__(self):
        return '<Category {}>'.format(self.name)
```
"python console"中依次执行语句
```bash
>>> from app import db, Post, Category
>>> db.create_all()
>>> py = Category(name='Python')
>>> Post(title='Hello Python!', body='Python is pretty cool', category=py)
>>> p = Post(title='Snakes', body='Ssssssss')
>>> py.posts.append(p)
>>> db.session.add(py)
>>> py.posts
>>> Post.query.with_parent(py).filter(Post.title != 'Snakes').all()
```
结果
![](20.png)
![](21.png)
知识说明
1. 新建了"目录类Category"和"文章类Post",一篇文章post属于一个目录category,在Post类中设置外键。引入datetime，db.Column()中default可以设置默认值。
2. 新建model并在交互式命令行中导入和创建表。
3. 一对多的关系，一个目录下有多篇文章。文章类（多的一方）定义category_id, 通过db.ForienKdy('category.id')指定关联的表（一的一方）的外键。
相信到这里为止，大家理解都没问题，跟sql的思路基本一致。
4. "反向查询"，这个功能ORM框架基本都提供。  
下面开始理解`category = db.relationship('Category', backref=db.backref('posts', lazy=True))`这句话。   
如果我们的category、post数据表有以下数据
![](22.png)
![](23.png)
需求是查询一篇文章，把文章的各个信息（包括所属目录名）显示出来。为了取Category的name，传统sql思维做法如下
```bash
post = Post.query.filter(Post.title == 'Hello Python!').first()
category = Category.query.filter(Category.id == post.id)
print(post.title, post.body)
print(category.name)
```
那么现在有了`category = db.relationship('Category', backref=db.backref('posts', lazy=True))`这句话后，
得到一个post对象后可以根据`post.category.name` 反向查（多的一方查一的那一方），就不用再写`category = Category.query.filter(Category.id == post.id)`这句话了，
取值更方便。  
Category查Post List（一的一方查多的一方）：py.posts 列出了Category.name == 'Python'下的所有文章列表，与第4部正好相反。其实Category类还有一行就是`posts = db.Column()`,只不过省略了，应该可以显示地写出来，不过我还没查到方法叫什么。
思考🤔: 其实在处理外键关系中，传统sql我们要先取关联的id再查询、或是使用join。而orm帮我们做的其中一件事情就是把有一对多、多对多关系的表通过对象的形式关联起来。  
本质还是拼sql、执行多次sql、拼好sql再批量insert、执行join等。  
懒加载lazy=True的作用就是用到反向查询的时候再查数据库返回关联的表的信息，没用上就不差，有利于节省资源。  

### （课下）更多的语法
还有很多语法，对应不同的sql功能，与上面的语法相似，请课下继续学习或遇到业务逻辑时再查询文章。  
- 多对多、一对一、多对一
- 执行原生sql
- join
- 关系运算符，大于小于、与或非、in 
- group by、聚合运算 
- meta信息，表明、结果集默认排序

详见文档和速查手册[csdn博文 SQLAlchemy 几种查询方式总结](https://www.cnblogs.com/shenckicc/p/6797990.html)
（todo 一篇专门的文章介绍sqlalchemy常用语法）

## (课下)Flask-Migrate
数据库迁移插件，一些教程有介绍这个插件。
当表结构需要修改时，如果删除表再建表会导致数据丢失。这个插件会在项目根目录下生成migrates文件夹，里面是迁移脚本。
原理其实还是sql语句，新建temp表，把旧数据插入到temp表，建新表数据复制过去，再删除temp表。
**实际使用并不稳定**，数据迁移失败时会卡住，需要折腾比如删除之前的脚本。
因此建议使用sql或图形数据库管理工具修改表结构和迁移数据。这个插件放到课下自己尝试。


**最后重申：使用ORM前务必打好sql基础，用sqlalchemy书写语句时想想会翻译成什么sql**
文章以上的内容都很重要，这节内容较多，因为程序信息交互数据库操作是基础中的基础。本文下面的不重要可以不看。


## (课下)课下作业：尝试连接其它主流数据库
1. 连接sqlite数据库，测试简单的增删查改。
2. 自行搜索并选择mysql驱动，安装mysql驱动，连接mysql数据库，测试简单的增删查改。


## (课外)已知bug
### (以下可以不用阅读)使用pgAdmin4 v1.6准备测试数据遇到BUG
pgadmin4 v1.6可能存在bug。使用psychopg2插入会把报错信息查到单元格中，pgadmin无法执行select语句报失去连接或表不存在。但insert中文报不能解码。  
谷歌说v1版本有bug，毕竟软件也提示更新到了v4版本。  
上网查询以为客户端服务端编码问题。查询得知创建数据库客户端和服务端编码集都为utf-8。查询template0模板字符集也正常。ide和mac终端也是utf-8。但感觉pgadmin创建数据库时用的ASCII编码。  
使用命令行创建数据库和datagrip创建都正常。  
数据库命令行\l 发现新建的数据库和有问题的编码集一致。  所以最终还是不理解出问题的数据库原因。  

下面是pgadmin v1.6操作过程，也许v4或更新版本已正常。供参考。原文这部分改成了datagrip创建数据库，传统写法中使用代码创建表。
### 准备测试数据
1. 新建测试数据库
这里用pgadmin演示，跟datagrip操作几乎一样。  
使用"postgres"用户和设置的密码（上节我这里设置的是"postgres"）登录数据库图形工具。  
2. 新建"testdb"数据库
![](2.png)
![](3.png)
3. 新建"user"表，字段包括 用户名username、 密码password、 昵称nickname
![](4.png)
![](5.png)
![](6.png)
或者执行sql语句创建表。
```sql
DROP TABLE IF EXISTS public."employee" ;
CREATE TABLE public."employee" encoding = 'UTF8'
(
id serial NOT NULL,
username "char" NOT NULL,
password "char" NOT NULL,
nickname "char",
CONSTRAINT user_pkey PRIMARY KEY (id) 
);
COMMENT ON TABLE public."employee"
    IS '测试用户表';
```
4. 创建完成可以看到新建的user表
![](7.png)
5. 查看表数据，手动插入几条测试数据。编辑后记得点保存（提交）按钮。
或执行sql
```sql
INSERT INTO public."employee" (username, password, nickname) VALUES ('王一', 'wangyi', '小王');
INSERT INTO public."employee" (username, password, nickname) VALUES ('刘敏', 'liumi', '');
INSERT INTO public."employee" (username, password, nickname) VALUES ('杨铮', 'yangzheng', 'canaan');
INSERT INTO public."employee" (username, password, nickname) VALUES ('用户1', 'test', '测试用户1');
```
![](8.png)
![](9.png)

## license
🚫禁止商用 转载注明作者