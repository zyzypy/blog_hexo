---
title: L7.[预习]flask插件机制介绍
comments: true
toc: true
date: 2018-06-08 08:46:07
updated:
tags:
- python
- pip
- flask
- 插件
categories:
- flask
banner:
---
## 去哪找包
- [flask官方文档的插件列表](http://flask.pocoo.org/extensions/)。 这里有常用扩展。
![](1.png)
- [pypi](https://pypi.org/)。 这里有所有的python包，其中当然包括flask的插件包。（ps:pypi网站不知道什么时候改版了变好看了）
![](2.png)
![](3.png)
- pip search
实质搜索的就是pypi
```bash
pip search [keyword] | grep [filter word]
```
![](4.png)
- 编译后的包
问题产生：windows端安装跟科学计算和图像相关的包或个别包时可能会报错。
比如`pip install mysql-python`时
```bash
_mysql.c:29:20: fatal error: Python.h: 没有那个文件或目录
     #include "Python.h"
                        ^
    compilation terminated.
    error: command 'x86_64-linux-gnu-gcc' failed with exit status 1
```
或这种情况
![](6.png)
解决：原因是windows一些底层功能是靠.dll文件实现功能的，python源代码转换成dll需要VC编译器。而window默认没有安装VC编译器。一种方法是
安装Virtualstudio开发套件，但是它下载安装太麻烦。所以可以下载别人编译好的.wheel文件。
[这理有编译后的二进制包](https://www.lfd.uci.edu/~gohlke/pythonlibs/)， 包很多可以ctrl+F搜索
![](5.png)
注意选择对应的python版本（例如cp36表示python3.6.x）和windows系统位数（win32表示32位，amd64表示64位。  
安装，假设pandas‑0.23.0‑cp37‑cp37m‑win_amd64.whl文件下载到了D:盘根目录，那么安装命令是 
```
cd D:
pip install pandas‑0.23.0‑cp37‑cp37m‑win_amd64.whl
```
- pycharm自带的图形界面中操作
前面的课程说明过，不再叙述。

## 使用扩展
引入和注册到flask app实例：一般形如下
```python
from flask import Flask
from flask_admin import Admin

app = Flask(__name__)

admin = Admin(app, name='microblog', template_mode='bootstrap3')
# Add administrative views here

app.run()
```
老式写法 from flask.ext.admin import admin

## (延伸)插件扩展原理
todo
## (延伸)开发插件
todo
