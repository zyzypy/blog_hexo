---
title: python-HelloWorld
comments: true
toc: true
date: 2018-04-10 12:04:52
updated:
tags:
- python
- 教程
categories:
- python
banner: hello_world_byGuzmán Barquín.gif
---
## 1.打开终端
- windows
开始》所有程序》PowerShell  
或 快捷键**Win+R**，键入*powershell*
![](终端1.png) 
![](终端2.png) 
![](终端3.png) 
- mac
launchpad(启动台) 》 terminal(终端)
或 快捷键 **Cmd+Space**,键入**terminal**
![](终端4.png) 
- linux（ubuntu）
快捷键 **Ctrl+Alt+T**

## 2.打开python交互命令行
终端是操作系统自带的命令行工具，python交互命令行是专门解释python语言的命令行工具，前者相当于操作系统后者相当于操作系统上装的一个特定功能的软件。  
下面的学习在python交互命令行中完成  

从终端输入`python`进入python交互命令行：会打印一些python版本信息，前面的符号变成了`>>>`  
或直接打开安装python时自带的官方工具IDLE  
两个方法效果一致
![](交互命令行1.png)
![](交互命令行2.png)
![](交互命令行3.png)

## 3.HelloWorld
初学者完成下面的练习，输入命令后回车⬅️  
注意手动输入，不要粘贴复制，肌肉记忆和语言感觉也是很重要的，实践才能学会  

目标：  
1. print() 函数输出信息。
2. 算数加减
3. 引入datetime包，输出今天的日期  
3. 声明变量和赋值 a=1;b=2,然后计算a+b的值
4. 注意不要输入中文引号和括号`（）‘’`，要输入英文`() ''` ，中文符号会导致语法错误
暂时不要考虑为什么是括号（），为什么helloworld被引号''括住这些细节  
暂时不要纠结包引用、变量、赋值这些新名词  

代码：  
```bash
print('Hello World')
100 + 200.3
10/3
import datetime
datetime.datetime.today()
a = 1
b = 2
a + b
```
![](helloworld1.png)

下面是精心录制的过程😀:
[@asciinema](helloworld.cast)

## 你完成了吗 
🎉🎉🎉 
ongratulations😀，完事开头难


{% raw %}
<!--
注意hexo-filter-asciinema插件需要npm安装cnpm安装的无效
raw标签使用
放到同名资源文件夹下
<link href="asciinema-player.css" rel="stylesheet" />
<script type="text/javascript" src="asciinema-player.js"></script>
<asciinema-player src="testasciinema.cast"></asciinema-player>
-->
{% endraw %}






