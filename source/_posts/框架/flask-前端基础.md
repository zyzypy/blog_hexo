---
title: L3.前端基础
comments: true
toc: true
date: 2018-05-31 22:09:03
updated: 2018-06-25 14:44:29
tags:
- flask
- 前端
- html
- css
- js
categories:
- [flask]
- [前端]
banner: conver.jpeg
---
## 前言
html/css/js是网页前端技术，负责网页架构、视觉、交互。所以学习它有以下几点原因：
- 即使是后端程序员，多少也是需要写一些前端页面的
- 会前端又会后端，向全栈工程师迈进
- 将来再工作中跟前端开发同事配合
前端语法不难，但内容很多细碎，要求同学们掌握前端基础，有时间进行更多的练习，不会的地方查询文档。  
本课知识独立，可以放在任意阶段学习。本节重点是HTML，css和js作为后端程序员掌握基础即可。后面的flask课程，主要用到HTML和基本css。  
## HTML
### 介绍
HTML(Hyper Text Markup language)超文本标记语言，俗话😂就是不止有文字还有图片、连接、音乐等。
### 新建项目
新建一个文件夹"test—html"，将在这个文件夹下建立.html文件来练习本课内容，编辑器可以使用sublime或pycharm甚至txt。
### 基本结构
项目文件夹下新建 base.html 
[](2.png)
**如何测试运行**：资源管理器双击.html文件，会用默认浏览器打开，也可以用ide自带的功能打开浏览器。
[](3.png)
如果想编辑代码或换用浏览器，.html文件右键更换打开方式。  
base.html写入代码
```html
<!DOCTYPE html>
<html>
    <head>
        <title>这是标题</title>
    </head>

    <body>
        <p>这是段落</p>
        <p>这是段落</p>
    </body>
</html>
```
这是html文件的基本结构。  
整个html文件被`<html></html>`括住，html下包括head和body两大部分，head放一些原信息和引用外部文件，body是放主要内容的地方。  
`<title> <div>`这些叫标签，尖括号括住名字，各有各的功能。  
标签总是成对儿出现，结尾的是含`/`的标签表示结束。
### 常用标签
1. 新建 mystyle.css ，内容先为空。
2. 项目文件夹根目录下随意放一张测试图片 myimage.png 。
3. 新建 base2.html 并写入代码
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="keywords" content="html, 教程">
  <meta name="author" content="YangZheng">
  <title>基础例子</title>
</head>
<body>
  <h1>春晓</h1>
  <p>
  春眠不觉晓，
  处处闻啼鸟。
  夜来风雨声，
  花落知多少。
  </p>
  <p>又一个段落</p>
  <img src="myimage.jpg" />
  <hr>
  <!--这是注释-->
  <a src="www.baidu.com" >点击打开百度</a>
  <ul>
    <li>第一项</li>
    <li>第二项</li>
  </ul>
</body>
</html>
```
运行结果
![](4.png)
- `<!DOCTYPE html>`告诉浏览器这是一个html文件，因为还有xhtml等格式，不过可以省略
- `<meta>`元信息。搜索关键字、作者信息等。
- `<title>`网页标题。会作为浏览器标签页名字。
- `<h1>`一级标题。还有`<h2>`到`<h6>`等标签，标题字号越来越小。
- `<p>`段落paragraph。段落会另起一行。
- `<img src="myimage.jpg" />` 图片标签。src表示source引用的图片路径，可以是相对路径、绝对路径、网络路径。标签中间不需要写值的可以`/>`结尾。
- `<hr>`水平分割线 horizontal。
- `<!--`开头`-->`结尾是注释。
- `<a>`锚链接Anchor link。src定义跳转地址。点击锚链接可以跳转到其它地址。
`<a src="www.baidu.com" >点击打开百度</a>`其中`<a>`叫"标签"，"点击打开百度"叫"值（value）"，src叫这个标签的一个"属性"，"www.baidu.com"叫src这个属性的"值"，请区分好概念。
- `<ul>`无序列表unorder list。`<li>`是其中的每一项。
### 表单
表单form用于搜集不同类型的用户输入。表单是网页中常见的交互形式，管理系统表单占据前端半壁江山，要求好好掌握。  
新建 myform.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>form</title>
</head>
<body>
  <h3>联系我们</h3>
  <form method="post" action="www.baidu.com">
    <label>用户名：</label><input type="text" name="username" placeholder="请输入用户名"><br>
    <label>密码：</label><input type="password" name="password" placeholder="请输入密码"><br>
    单选:
    男<input type="radio" name="sex" value="male">
    女<input type="radio" name="sex" value="female"><br>
    复选框：
    体育<input type="checkbox" name="hobby" value="sport">
    阅读<input type="checkbox" name="hobby" value="reading"><br>
    下拉列表:
    <select name="work" >
      <option value="student">学生</option>
      <option value="programmer">程序员</option><br>
    </select><br>
    文本域：
    <textarea>请输入详细信息</textarea><br>
    按钮：
    <button type="button" onclick="alert('hello world')">点我</button><br>
    <input type="submit"  value="提交">
  </form>
</body>
</html>
```
结果
![](6.png)
表单标签是`<form>`，每一行是`<input>`,不同的type代表不同功能：
- text 文本框
- password 类似文本框但不显示内容
- radio 单选 只能同时选一个
- checkbox 复选框 可以选多个
- `<select>`标签 下拉列表，有点像`<ul>`
- textarea 文本域 可输入大量文本
- button 按钮 可以绑定js事件实现特定功能
- submit 提交 触发action、传递数据到后台  
一些属性的意思：
- action 请求目的地址，点击submit时触发
- method 表单一般包含敏感信息，一般为post请求
- name 后端取值一般根据这个值取
- placeholder 可以作为文本框输入前的提示
- value 这行的值，比如`<option value="student">学生</option>`你在浏览器看到的是"学生",传给后台的是"student"
### div布局
网页复杂之后，为了易于维护，需要根据功能内容划分一个一个逻辑区域，把html代码分块，后面通过css控制div的排列。  
新建 layout.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>layout</title>
  <style>
  div {
    border: blue 1px solid;
  }
  #header {
      background-color:black;
      color:white;
      text-align:center;
      padding:5px;
  }
  #nav {
      line-height:30px;
      background-color:#eeeeee;
      height:300px;
      width:100px;
      float:left;
      padding:5px;
  }
  .section {
      width:350px;
      float:left;
      padding:10px;
  }
  #footer {
      background-color:black;
      color:white;
      clear:both;
      text-align:center;
      padding:5px;
  }
  </style>
</head>
<body>
  <div id="header">
    <h1>xx新闻网</h1>
  </div>

  <div id="nav">
    热点<br>
    体育<br>
    娱乐<br>
  </div>

  <div id="section" class="section">
    <h1>体育</h1>
    <p>北京时间6月25日22点，世界杯小组赛第三轮，A组的东道主俄罗斯对阵南美劲旅乌拉圭，两队均已经拿到16强资格，这将是A组的头名之争。穆斯莱拉及戈丁将迎来国家队第100场及第120场，苏亚雷斯进球乌拉圭世界杯必胜的定律能否延续？
    </p>
    <p>两队在历史上交战过8场，俄罗斯队（包括前苏联）5胜2平1负占据优势，进14球失5球
    </p>
  </div>

  <div id="footer">
    Copyright 2018 yangzheng
  </div>
</body>
</html>
```
**网页右键检查打开谷歌浏览器的开发者工具有利于理解。**  
结果
![](5.png)
`<style>`标签负责定义样式，下面css中将细讲，这里可以先复制。
可以看到`<div>`标签把页面分的很清晰。  
id属性整个页面唯一，在定义样式时和js交互式时有用。

## css
CSS 指层叠样式表 (Cascading Style Sheets)，定义标签的定位和外观。  
下面以上面layout.html例子中的样式语句为例讲解。
### 选择器
选择器表示要对哪种哪个标签进行样式定义，
```css
/*这是注释*/
#header {}  
.section {} 
```
"div"标签名开头的指网页中所有这种标签。  
"#"开头的是id选择器，对应html标签中的`id`值。  
"."开头的是类选择器，对应html标签中的`class`值。定义样式推荐使用class。  
"{}"大括号中写具体的样式定义语句。  
### 常用语句
语法形如 "属性:值;"
- background-color 背景颜色
- color 字体颜色
- height div块高度
- width  div块宽度
- float 多个div块向左依次排列
- padding div中内容距离边界的距离
- margin 距离上一个div边界的距离
内容很多，建议根据单词意思理解。div相关的理解根据谷歌浏览器开发者工具中的div图示理解。
### 引用形式
- 外部样式表
html和css分开写，一般正规和大型项目中都采用这种形式。  
新建 mystyle.css, 把上个例子layout.html中的样式内容**剪切过去**  
mystyle.css
    ```css
    div {
    border: blue 1px solid;
    }
    #header {
      background-color:black;
      color:white;
      text-align:center;
      padding:5px;
    }
    #nav {
      line-height:30px;
      background-color:#eeeeee;
      height:300px;
      width:100px;
      float:left;
      padding:5px;
    }
    .section {
      width:350px;
      float:left;
      padding:10px;
    }
    #footer {
      background-color:black;
      color:white;
      clear:both;
      text-align:center;
      padding:5px;
    }
    ```
    修改 layout.html
    ```html
    <head>
      <meta charset="UTF-8">
      <title>layout</title>
      <link href="mystyle.css" type="text/css" rel="stylesheet">
    </head>
    ```
`link`标签负责引用.css文件，需要制定加载的文件类型为样式表stylesheet。
刷新浏览器，结果跟之前一致。  
- 内部样式表
第一个例子html文件内`<style>`中写的就是内部样式。
- 行内样式
修改layout.html
```html
<body>
  <div id="header" style="background-color: gray;">
    <h1>xx新闻网</h1>
  </div>
  
  ...
  
</body>
```
发现行内样式覆盖了引用的外部样式。  
生效优先级：行内样式 > 内部样式 > 外部样式
容易维护程度：行内样式 < 内部样式 < 外部样式
我们的flask实战项目将会采用外部样式


## js
### DOM概念
当网页被加载时，浏览器会创建页面的文档对象模型（Document Object Model）。
![](js1.png)
也就是网页上各个元素组成的一个逻辑上的树，js可以操作这个document中的元素、属性、css样式、事件反应。  

作为后端程序员能基本看懂js即可，下面表单验证，可以认识基本的js语法和特性，理解表单验证的原理。  
查找匹配元素、属性、修改css样式、各种事件等更深入的js学习，这里不探讨。  
(课下)[w3cschool-js教程](http://www.w3school.com.cn/js/index.asp)
(课下)[阮一峰-javascript诞生记](https://www.baidu.com/link?url=V3XjRzifgSVzhRmUVntzCm9Q5tou4AIHpfabditUhhGDTj1OAjFicJu9tOq3NoLbpxS_Lm6wWj22rtOro80up8ZpxJzoe7YZENowAcUGsG7&wd=&eqid=a784837d000192a7000000025b31fccb)
### 表单验证
js引用跟css一样有 行内引用、内部引用、外部引用，这里采用外部引用，其它课下了解。  
以上面的 **myform.html**为例子，为已写好的表单添加表单验证功能，要求用户名不能为空。
1. 新建 validate.js
```javascript
function check_required()
{
    var username = document.getElementsByName('username')[0];
    if (username.value.length == 0){
        alert('用户名不能为空')
        return false
    }
}
```
js有自己的一套语法，声明变量、关系比较、数组下标取值等。如果你有其它语言基础的话，应该能看懂大半。function是函数关键字，相当于python的def。大括号包围语句块。
2. 修改myform.py 
```python
<head>
  <meta charset="UTF-8">
  <title>form</title>
  <script type="text/javascript" src="validate.js"></script>
</head>
<body>
  <h3>联系我们</h3>
  <form method="post" action="www.baidu.com" onsubmit="return check_required()">
    <label>用户名：</label><input type="text" name="username" placeholder="请输入用户名"><br>
    
    ...
```
`<script>`标签引用js，src属性是文件位置。  
onsubmit是"事件"，"事件"是满足一定调节然后出发js函数，onsubmit是表单提交时出发，还有onclick鼠标点击，onblur获得焦点等等。
用户名空时点提交，测试代码生效
![](js2.png)  


## 前端框架bootstrap
bootstrap对html、css、js进行了封装。只要引入和设置class的值，就能改变空间的样式，而不用关心具体的css是什么样，提高开发效率和更美观。  
flask实战课程中将使用封装它的插件。语法比较多，作为后端开发简单了解即可。

## 参考文档
[w3cschool](http://www.w3school.com.cn/)

