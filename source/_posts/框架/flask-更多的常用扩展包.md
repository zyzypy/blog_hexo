---
title: flask-更多的常用扩展包
comments: true
toc: true
date: 2018-06-23 15:10:32
updated:
tags:
categories:
banner:
---
## 取舍
在一些教程中，你会见到下面要讲解的一些flask扩展。  
但我对于扩展包的观点和建议是：
1. 要先懂原理
要先回用基础代码写出来。否则扩展包是对较低一层代码的封装，不理解原理的话，出问题不会改。
2. 取舍
叉号代表不采用，对号代表采用。综合🤔项目需求、插件学习成本、使用插件后的方便程度、照顾学员、本人对插件的掌握程度等几方面考虑。下面仅代表我个人的判断和推荐：
- ✅flask-bootstrap
直接在前端书写会更易控制和查错。但前端html太复杂大家不太容易跟视频教程。我们的课程目的是培养后端工程师，大家前端水平平均不高。另外flask-bootstrap能跟flask-wtform连用。能为后端开发者提供便利，所以采用这个插件。
- ✅flask-wtform: 
原因同上。
- ✅flask-sqlAlchemy
极大减少代码量，提高程序可读性，推荐使用ORM框架。复杂情况使用原生sql即可。
- ✅flask-mail
web项目中最常用的验证方式是邮箱和手机发送动态验证码或验证链接。手机验证码需要购买运营商短信。所以项目中还打算用邮箱方式。这个插件能简化代码，只用配置就够了。
- ❌flask-moment
python已内置time和datetime模块，ORM框架会自动转换时间存储UTC时间或时间戳时间。如果遇到个性化的问题，那么用time和datetime模块进行简单的时区加减也可以办到。  
- ❌flask-restful
一些公司项目前后端分离，flask项目只返回数据而不是返回html，一般为json格式。return render_template()改成`return jsonify(dict_context_data)`即可。这个框架提供了专为写接口的一些封装，可以工作后公司用再用。  
- ❌flask-migrate
我在使用过程中发现并不稳定，出错还是要使用sql操作，想看懂这个插件还要看这个插件生成的sql脚本，等于说还要会sql。  
- ❌flask-script
可以制作命令行工具，功能非必需，为了简化项目所以不使用。
- flask-admin
其它插件将会在实战项目中用到再介绍。

**同学们务必掌握插件基础原理，切莫为了少写两行代码而不理解原理**

## flask-Bootstrap

## flask-mail

## (课外)flask-migrate

## (课外)flask-moment

## (课外)flask-script

