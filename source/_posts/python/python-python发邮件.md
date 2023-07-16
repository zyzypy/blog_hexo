---
title: python发邮件
comments: true
toc: true
date: 2018-08-05 20:08:03
updated:
tags:
- python
- 邮件
- mail
- TCP/IP
categories:
- python
banner: Dribbbleinbox_by_JustasGalaburda.png
---
## 场景
- 通信
普通写信沟通，公司汇报工作
- 安全类验证
修改密码、动态验证码、异地登录
- 广告类
各个网站活动通知、广告，自动群发
- 邮件客户端
outlook、foxmail、mac邮件等，代收邮件，多平台邮件all_in_one代收，提升工作效率，提升逼格
- 活动
生日自动祝福
- 项目集成
爬虫、自动化运维、web项目中集成

电子邮件在软件项目使用中和日常工作中 非常广泛，本节我们学习使用python来发邮件。

## 准备工作：开启邮箱协议和获得权限密钥
*邮箱协议：就好像支付宝快捷支付，支付宝初次要绑定银行卡，我用outlook代收QQ邮箱的邮件或用python代码发送QQ邮件都需要用户名
因为QQ邮箱用户群体大，下面选用QQ邮箱作为演示，其它邮箱如google、163、火狐、雅虎大同小异。
权限密钥：账户名还是QQ邮箱名，“但密钥不是QQ密码”，（想到自己初学python时，网上有些教程不详细，直接说qq账号和密码，密码还打码，结果用qq密码试了半天失败。。）需要下面的步骤申请。*
步骤：
1. 通过QQ客户端或点击QQ邮箱网址[](https://mail.qq.com)登录
![](1.png)
2. 点击 设置
![](2.png)
3. 菜单点击 账户
![](3.png)
4. 向下翻到协议处
![](4.png)
5. （重要）开启**IMAP/SMTP**协议
6. 安全验证
毕竟代收，安全验证比较严，点击开启时会弹出验证。
（选做）如果之前qq未绑定手机，会先要求qq号开通二代密保，根据提示一步一步来。
![](5.png)
如果已绑定手机，会看到如下验证
![](6.png)
发短信内容"配置邮件客户端"到1069070069。  
![](7.png)
发送后回到网页点击“我已发送”
7. (重要)生成密码
验证成功后生成密钥。（建议把密钥先保存到txt备用，以免忘记再来一次验证）
![](8.png)
8. (选做)其它设置
勾选“SMTP发信后保存到服务器”。最后点击“保存更改”。

## 邮箱协议解释
什么是协议，就好像http的三次握手，邮件协议定义了邮件该怎么组装数据、传输、发送、管理。
我们不需要知道细节，只需要了解下面协议的大概作用。
- SMTP（发）：Simple Mail Transfer Protocol）即简单邮件传输协议，属于TCP/IP协议簇，控制邮件发送到目的地
- POP3（收）：Post Office Protocol 3）即邮局协议，允许用户从服务器上把邮件存到本机
- IMAP（收增强版）：Internet Mail Access Protocol，即交互式邮件存取协议，跟POP3类似。不同的是，开启了IMAP后，在客户端上的操作都会反馈到服务器上，如：删除邮件，标记已读等，让客户端与服务端保持一致。
- ssl（加密）：安全套接层，传输层加密，区别于上面三种。比喻像暗号：天王盖地虎,小鸡炖蘑菇。现在你只要知道它是加密用的，不用管细节。


## 内置包
smtplib 、poplib、 imaplib
### 示例
（todo 未测试）
```python
import smtplib
from email.mime.text import MIMEText
from email.header import Header

# 发送邮箱服务器
smtpserver = 'smtp.sina.com'
# 发送邮箱用户/密码
user = 'username@sina.com'
password = '123456'
# 发送邮箱
sender = 'username@sina.com'
# 接收邮箱
receiver = 'receive@126.com'
# 发送邮件主题
subject = 'Python email test'

# 编写HTML类型的邮件正文
msg = MIMEText('<html><h1>你好！</h1></html>','html','utf-8')
msg['Subject'] = Header(subject, 'utf-8')


# 连接发送邮件
smtp = smtplib.SMTP()
smtp.connect(smtpserver)
smtp.login(user, password)
smtp.sendmail(sender, receiver, msg.as_string())
smtp.quit()
```
## （课下）第三方包zmail
### 介绍
zmail是一个邮件收发包。相比内置包，封装程度更高，使用更简单。缺点，有bug，不够流行。
[zmail-github主页](https://github.com/ZYunH/zmail)
### 安装
```pip install zmail```
### 发邮件(简单例子)
```python
import zmail

# 账户名、密码（填你自己的）
user = '1403117405@qq.com'      # 发送者
password = 'oavvmkjhi****had'   # 刚才申请的密钥
target = '969501808@qq.com'     # 接收者，可以用

# 生成邮件操作对象
server = zmail.server(user, password)

# 邮件内容
mail = {
    'subject': '测试-yangzheng',      # 标题
    'content': '用python和zmail包发邮件 yeah',    # 正文
}

server.send_mail(target, mail)
```
### 发邮件(含附件和html)
```python
# 发送复杂一点的例子
import zmail
import os

# 账户名、密码（填你自己的）
sender = '1403117405@qq.com'
password = 'oavvmkjhibhtjhad'
receiver = '969501808@qq.com'

# 生成邮件操作对象
server = zmail.server(sender, password)

# 邮件内容
mail = {
    'subject': '测试-复杂一点-yangzheng',      # 标题
    # 'content': '',      # zmail源码有点问题
    'content_html': zmail.get_html(os.path.join(os.path.dirname(__file__), 'test.html')),    # 正文
    'attachments': os.path.join(os.path.dirname(__file__), 'test.png')
}
print(os.path.join(os.path.dirname(__file__), '/test.png'))
server.send_mail(receiver, mail)
print('发送完成')
```
![](10.png)
### 错误和异常
- 报错，认证失败
```cmd
smtplib.SMTPAuthenticationError: (535, b'Error: \xc7\xeb
```
检查qq号和申请得到的密码和协议是否开了
- 报错，解码错误
```cmd
UnicodeDecodeError: 'gbk' codec can't decode byte 0x80 
```
原因是zmail包的get_html方法直接open文件，默认使用了系统默认的gbk解码。
- 报错，类型错误
```cmd
    _combine_message += i
TypeError: must be str, not NoneType
```
原因zmail源码问题，mail对象下没有content字段也会走if判断，不知道在哪里赋值了，我在构造mail时添加了content=''
- 请求超时
网络很差或使用被墙的谷歌邮箱
- 发送成功没看到
等一会

## 第三方包yagmail
### 介绍
[yagmail](https://github.com/kootenpv/yagmail)是一个收发邮件的包。非常简单只能，只用把精力放在关注内容上。
### 安装
pip安装 （写此文时包的版本v0.11.214）
```powershell
pip install yagmail
```
或conda安装（需要管理员权限的终端例如powershell(管理员)）（版本比较老）
```powershell
conda install -c jrkerns yagmail
```
或源码安装
### 发邮件（简单）
```python
import yagmail
import os

user = '1403117405@qq.com'
password = 'oavvmkjhibht****'
reciever = '969501808@qq.com'


yag = yagmail.SMTP(user=user,password=password,host='smtp.qq.com',smtp_ssl=True)
# contents = [subject, html, attachment]  # 参数可选
contents = '测试test'
yag.send(to=reciever, subject='测试yagmail-simple', contents=contents)
print('发送完成')
```
### 发邮件（带html和附件）
```python
import yagmail
import os

user = '1403117405@qq.com'
password = 'oavvmkjhibhtjhad'
reciever = ['969501808@qq.com', '1403117405@qq.com']

html = """
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="GB18030">
    <title>一个复杂点的例子</title>
</head>
<body>
    <h1>测试</h1>
    <p>lalalalal</p>
    <img src="https://www.baidu.com/img/bd_logo1.png?where=super"/>
</body>
</html>
"""

yag = yagmail.SMTP(user=user,password=password,host='smtp.qq.com',smtp_ssl=True)
# contents = [subject, html, attachment]  # 参数可选
contents = ['测试test', html, os.path.join(os.path.dirname(__file__), 'test.png')]
yag.send(to=reciever, subject='测试yagmail-复杂', contents=contents)
print('发送完成')
```
优点：这个包很智能，很多参数都是可选的，可以是字符串或列表，下面的写法任选其一
```python
yag.send(to = to, subject = subject, contents = body)
yag.send(to = to, subject = subject, contents = [body, html, img])
yag.send(contents = [body, img])
yag.send(to, subject, [body, img])
```
### 常见错误
- 检查权限 SMTPAuthenticationError: Application-specific password required
- 超时
测试发现，其实上面两个错误并不是账户密码错误或网络不好，而是因为QQ邮箱需要`smtp_ssl=True`和`host='smtp.qq.com'`参数，
毕竟不是国人写的包，参数不定义全可能请求不到正确的邮件服务器。

## 邮件客户端
下面以outlook为例子。一般安装office套件时自带。
根据提示一步一步来。
![](21.png)
选择开启的协议pop3或imap
![](22.png)
![](23.png)
windows凭证管理会记住，相当于记住密码
![](24.png)
qq邮箱用了ssl加密，端口默认常用端口，连接失败的话上网查查各平台的邮件服务器信息再填
![](25.png)
![](26.png)
设置成功后可以用outlook来接发邮件。可以配置更多的邮件账户。

## reference
课外参考 
1. python内置包收发邮件[廖雪峰的教程](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432005226355aadb8d4b2f3f42f6b1d6f2c5bd8d5263000)
1. python内置包收发邮件[gxyz的博客](https://www.cnblogs.com/itogo/p/5910681.html)

## license
原创文章，转载注明作者
封面图[Dribbble inbox by Justas Galaburda](https://dribbble.com/shots/4849612-Dribbble-inbox)