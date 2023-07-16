---
title: git中fatalAuthenticationFailed的问题
comments: true
toc: false
date: 2018-07-24 09:57:12
updated:
tags:
categories:
- 软件
banner:
---
### 问题产生
一些git server需要验证用户名密码，比如我在使用gitee的时候想pull代码。  
git pull,windows显示表单弹窗（命令行的话会让输入用户名密码）， 输错了一次用户名密码，然后就“ 15:25	Fetch failed: Authentication failed for 'https://gitee.com/xxx.git/'”了。  
再次git pull，仍然没有权限，问题是，没有向第一次一样弹出让输入用户名的弹窗。

### 解决
原来windows记忆了用户名密码并自动填写，验证失败存什么啊。
1. 打开windows的凭据管理。点击左下windows徽标或控制面板中搜索“凭据”。
![](1.png)
2. 删除相关网站的凭据。我这里是 www.gitee.com这条。
![](1.png)
3. 执行git操作，会重新弹出用户名密码的表单（这里就不截图来了），输入正确的凭证。

其它操作系统下未测试。