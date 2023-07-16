---
title: python-智游试讲
comments: true
toc: true
date: 2018-07-26 13:18:27
updated:
tags:
categories:
- python
banner:
---
## 第二阶段介绍
### 学习意义
### 目录安排
### 先睹为快
![](1.png)
![](2.png)
学习意义：1.变爬虫窃取到自己创造2.web技术涉及面广整合知识和打基础3.找工作。
![](3.png)
框架对比：我们选用flask前期，转django做大项目。
![](4.png)
网站成品：（后面部分网站采用python技术）csdn博客、百度新闻、知乎、国外版花瓣网、管理后台、爬虫信息统计展示。

## 初识flask
假设学生已有前端基础
### 准备环境
1. 安装包。
方式一终端；方式二ide。
    ```shell
    > pip install flask
    ```
2. flask几个依赖包的作用。
     ```shell
    > pip list 
    ```
    虽然只install flask，但还出现了其它几个包。它们是flask的依赖包，flask的一些功能基于它们。就好像requests包服务http请求一样，
    它们各有各的作用：
    - Flask：flask的核心组成
    - Jinja2：前端模板渲染
    - MarkupSafe：html中的特殊字符转换成python可接受的字符串
    - itsdangerous：密码加解密，安全相关，防CSRF攻击等
    - pycryptodome: 加密
    - Werzeug：德语，意为工具箱脚手架。WSGI通用网关接口（简单理解为web应用）应用工具集。
###  hollow world
1. 创建项目。
2. 新建app.py,代码如下
    ```python
    from flask import Flask     # 引入Flask类
    app = Flask(__name__)       # app是Flask的实例，它接收包或者模块的名字作为参数，但一般都是传递__name__。就想一个普通类操作要先生成一个实例
    
    @app.route('/index')             # 用装饰器定义路由"/" 表示匹配"域名/"或"域名/index"，也可以写作"/index"
    def index():          # 视图接收请求并进行相应
        return 'Hello World! '   # 返回了字符串helloworld
    
    if __name__ == '__main__':      # 这个.py文件自己执行时 __name__就等于'__main__'
        app.run(debug=True)    # 执行app.run就可以启动服务了
    ```
（课下）更深层的解释。
3. 运行,地址栏输入"域名：端口号"。方式一终端；方式二ide
4. 简单理解url-路由（路标）-视图函数-返回html的数据流。简单理解服务端客户端。以后的课程上下文请求与响应一节会深入讲解。
### 模板渲染 render_template()
1. 让函数返回html内容
    ```python
    def hello_world():
        return '<h1> Hello World! </h1>'
    ```
1. 新建"template"目录，目录下新建文件"index.html"并添加代码
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
4. 测试。刷新浏览器，结果与之前一致，但是把html分离了出来，降低了耦合方便修改。
### 渲染传值
1. 传递变量
app.py
```python
@app.route('/')
def hello_world():
    return render_template('index.html', user_name='小明')
```
2. 渲染值，修改template/index.html
```html
<body>
  <h1>你好：{{ user_name }}</h1>
</body>
```
3. 测试。
4. 总结，render_template()第一个参数是模板名，系统会自动在"template"文件下查找，后面可以声明key-value类型的变量。
双大括号是前端模板jinja2的语法，可以将服务端的变量渲染到前端。
### debug模式
```python
if __name__ == '__main__':
    app.run(debug=True)
或
app = Flask(__name__)
app.DEBUG = True
```
小坑：pycharm2018中flask新版本需要修改run配置

## 课下作业
1. 假设把示例代码敲一遍，明天检查
2. 延伸阅读
[hellworld详细解析](https://blog.csdn.net/christopherchen/article/details/54636846)
[Python3 venv 创建虚拟环境](https://blog.csdn.net/geekun/article/details/51325383)