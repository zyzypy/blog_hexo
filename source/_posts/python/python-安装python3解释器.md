---
title: 各平台下包管理器安装python3解释器（windows chocolatey/mac brew/linux apt）
comments: true
toc: true
date: 2018-04-10 11:56:55
updated: 2018-07-12 21:08:55
tags:
- win
- mac
- linux
- python3
- python
- chocolatey
- brew
categories:
- python
banner: 
---
## python2 还是 python3 ？
这个问题有点像xp好还是win7好，几年前有不少xp的拥护者，现在你已经见不到了。  
几年前的教程会告诉你虽然py3好但99%的企业使用python2，所以你要下py2。倒也不是python的人守旧，而是因为后端项目庞大，重构真是个费力不讨好的事。
即使在2017年，我接触的俩家公司仍然使用py2，一个因为项目大重构成本高，一个因为使用的erp框架基于py2，而那个erp框架也大刚刚在最新版本支持py3。  
我们的学习小项目选择py3,unicode编码问题会让你省心并且错误提示更清楚。py3是大势所趋，py2官方已经停止更新了，就好像停产的电子产品一样  
请不要担心，py2 跟py3并不想前端社区变化那么快。平时工作中，py2除了print少个括号，容易打印乱码，文件头部需要生命一行编码方式，其它95%的代码一模一样！
业务逻辑代码几乎100%相同  
20180712目前，主流公司全面拥抱python，甚至一些说绝对不用py3的公司也开始说"真香"了，所以已经不用如上纠结。
结论：`直接下载python3 (￣▽￣)`，不要纠结，差别比你想象中的小。如果你将来的公司用py2，相信你会在两天之内适应。

## windows
环境：windows10 x64 
### 方式一[推荐]：下载安装包 .exe
1. 打开[官网python.org](https://www.python.org/)
2. 菜单/download 选择合适windows平台。 直接点此进入➡️[Python Releases for Windows](https://www.python.org/downloads/windows/)
![](win安装py1.png)
3. 下载页
![](win安装py2.png)

- 版本选择：
    - 前两个是**最新稳定版**py2和py3，没有特殊需求选择即可
    - 后面的是早先的各种版本，时间倒叙排列 
    - 本文书写✍️时选择的是**最新稳定版**"Python 3.6.5" ，如图中箭头所示。当你看到这篇文章时最新稳定版已经不是3.6.5了
    - 版本释义：例如*Python 3.7.0b3 - 2018-03-29*，"3"大版本号python3
    - 版本释义：（版本号后面跟的字母）
        a alpha 内测
        b beta 公测
        rc Release Candidate 正事版备选  
        什么都不带 正式版 
- 安装包格式选择：
    - web-based installer。在线下载器，比如安装腾讯管家时就是这样，很小的几百kb的文件，运行后会在线下载真正的文件（其实就是下面的两种方式），
    完成后续安装
    - executeble installer。常见的exe可执行安装程序
    - embeddable zip file。 zip压缩包，解压后就可python解释器文件，缺点需要手动配置环境变量
    推荐executable installer方式，下载好的exe包可以放U盘分发给小伙伴，向导形式容易理解。
### 下载速度慢？  
加入学习QQ群**775648064**下载群共享文件
可以百度"python3 x64 下载"，选择其它网站或网盘下载。  

### 方式二[尝鲜]：chocolatey包管理器安裝
*沒有安裝chocolatey的详见{% post_link 软件/软件-各系统下包管理器chocolatey-homebrew-apt 各系统下包管理器介绍 %}*
1. （选做）搜索软件名"python3"  
官网仓库中搜索或命令行中搜索  
```powershell
choco search python3    # （推荐）默认安装最新稳定版
choco search python3 --version 3.7.1    # 安装指定版本
```
![](chocolatey安装py1.png)
![](chocolatey安装py3.png)
![](chocolatey安装py4.png)
![](chocolatey安装py5.png)
![](chocolatey安装py2.png)
2. 安装
打开powershell(管理员)，键入👇命令
```powershell
choco install python3
```
![](chocolatey安装py6.png)
从控制台信息可以看到：
下载了python3最新稳定版的安装包  
下载并安装了chocolatey-core.extensions（几乎每个软件都依赖这个，不太清楚安装chocolatey的时候为什么没一块装上，下次安装就不需要了）  
安装python3  
提示需要同意执行chocolateyInstall.qsl的shell脚本，键入"Y"（猜测是一个简单监本，接收choco命令的各种参数，然后把下载好的安装包复制到安装路径，和choco自己的相关设置，
有兴趣的可以键入P打印看一下脚本）  
安装到了C:\Python36下， 指定路径好像是choco install的时候加上参数location D:\example-path  
原来执行的是下载好的exe  
3. 刷新环境变量
安装信息提示用命令刷新环境变量（相当于方法一安装后的重启电脑）
```powershell
refreshenv
```
4. 安装完成 \(^o^)/
输入`python`测试是否进入python交互控制台  
或输入`python -V`打印版本  
![](chocolatey安装py7.png)


## macOS
环境：macOSX 10.13 HighSeirra
### 方式一：下载安装包 .dmg
同上windows步骤
1. [python releases for mac](https://www.python.org/downloads/mac-osx/)
![](mac安装py1.png)
###

### 方式二：通过Homebrew安装
### 安装前提：安装Xcode
（记不清了，具体看报错信息，出问题问我或百度）
homebrew安装时报错提示需要编译  
1. 安装Xcode，appstore安装Xcode下载速度很快  
2. 安装完成后输入一条命令安装Xcode的命令行工具
python官网下的是编译后版本，windows上不需要先安装C compiler之类的，mac dmg未尝试不知道是否也需要先装Xcode。原理就是类似
java中jdk跟jre的关系（这么比喻不太恰当，jdk编译后是字节码而不是二进制【】），开发需要jdk，而电脑中一般只内置了jre、c++ runtime之类的运行时。


## linux
环境：ubuntu 16.04 Lts

