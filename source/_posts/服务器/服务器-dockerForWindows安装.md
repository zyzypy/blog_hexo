---
title: dockerForWindows安装
comments: true
toc: false
date: 2018-08-01 17:29:45
updated:
tags:
- docker
- 容器
- 安装
- 软件
categories:
- 服务器
banner: docker_pride_by_Sarah_Park.png
---
## 谈谈docker
看安装步骤的请直接移到下一部分。
### 第一次接触
docker如火如荼。然而也有人觉得是舍近求远。
第一次接触docker时是15年工作时，win7+dockerToolKit，我细数一下它的罪过：
- 安装VMbox虚拟化软件（类似VMWare的一款虚拟化软件）
- 桌面新增三个图标，看着迷惑，然而工作中它们没用，还是用命令行操作
- VMbox中设置虚拟共享目录
- 可能会缺少一个linux booter 的iso，需要自己去找
- 下载一个不小的公司提供的开发环境image
- 因为中间多了一层，所以docker-machine的ip地址、端口映射要想清楚
- run容器（加上挂载的目录、端口映射、可能还要安装三方包）
- 同事没有commit维护容器的习惯
- 与pycharm结合不好，虽然解释器有相关选项，但没试成功，最后查到外国网友说pycharn那个功能也是新开发的有bug。
- 电脑内存飙升，前后端分离开三项目加上ide就90%了（当时8G内存）
项目终于跑起来了，当时我就想，这还不是要虚拟机，（虽然看文章明白容器化跟虚拟机不一样，说它优点更轻量），但这折腾一番还不如用VM搭个虚拟机。如果linux和其它方面基础不牢的话，会经常出现问题。公司里基本都是两年经验的开发者，仍然经常崩溃甚至重装才能解决，
当然跟时代有关，当时docker刚兴起，公司里大部分人也不理解原理，我当时的基础也不好，所以大家用的都不太好。
当时搜索了一些资料，为什么这东西这么难用还这么火，我也尝试睡服自己，好处和一些客观原因是
- 有基于系统虚拟机的封装的技术，（我忘了叫什么名字也懒得找了，pycharm支持，据说与docker火热程度一样），但因为虚拟机镜像太大不适合天朝国情，加上那个软件的官网被墙，所以国内就没发展起来。
- 有人比喻，docker不就是几年前金山毒霸（这个估计很多年轻人已经没听说过了，类似360安全卫士和腾讯管家的软件）的沙箱功能吗。
沙箱功能确实好用，可能docker偏命令行，如果有GUI的话就好用了，这让我继续观望。
### docker for Mac/windows 发布
然后，docker确实在进步，16年的时候已经有了 docker for Mac，半年后docker for Windows10出来了。然后试了下docker for Win10。  
优点：
- GUI。降低了操作门槛，思路上更清晰，更像一个“产品”了。这不是说逼格降低了，而是由半成品想成品发展。
- docker for win10依赖win10自带的Hyper-v虚拟化软件，不用再安装VMBox（VMBox 界面真丑）了。自带linux booter。
- GUI。设计专业美观，交互提升，平时在系统右下角工具栏，安静地待在那里。通知调用系统通知。共享磁盘在设置里。去掉了docker-machine简化了网络配置。
缺点：
- 刚出来不久，教程较少，还不太稳定，官方的多容器管理可视化工具安装后启动失败。
### 2018年08月
现在又看了一次，发现官网改版了，大大的download找不到了。想必docker越来越火，也开始了商业化，分成了社区版、商业基础、商业中级、商业高级等几个版本。看来，软件其实都要赚钱的。

## 安装 Docker For Windows
### 0.系统要求
windows10 x64 ，不支持win10以下版本
### 1.（略过）开启cpu虚拟化功能
任务管理器-性能，查看是否开启
![](0.png)
一般电脑都会支持并默认开启，可以略过这一步，后面如果虚拟机报错再回头设置。只要不是太老的电脑一般都支持，个别主板默认关闭需要进BIOS开启。参考文章
[百度经验-bios开启虚拟化](https://jingyan.baidu.com/article/ab0b56305f2882c15afa7dda.html)
### 2.开启Hyper-V
Hyper-V是win10自带的一款虚拟化软件，类似VMWare，默认是关闭的，下面去打开它。
1. 点击系统左下windows徽标搜索或打开控制面板搜索"启用或关闭Windows功能"
![](1.png)
![](2.png)
2. 勾选上Hyper-V。
主要是启动服务，资源监控服务和power shell命令行工具和gui工具看个人需要把。我这里都勾选了。
![](3.png)
3. 确定后重启电脑。
可以稍后重启，安装完docker后再重启
![](4.png)
4. 重启后快捷启动里能看到Hyper-V的快捷方式（桌面没有）
(见安装完docker的图)  

### 3.下载和安装docker
1. 下载
    - 首先想到了 [DaoCloud](http://get.daocloud.io/#install-docker-for-mac-windows) ，国内领先的docker平台，提供了国内镜像加速下载，速度挺好。
    - 第二想到了阿里云，用过阿里云的镜像加速，[阿里云-容器镜像服务-镜像加速器](https://cr.console.aliyun.com/cn-beijing/mirrors)，在文档里找到了相应系统的ftp镜像。
    - 第三官网的，原来放到docker-store里了，[docker-store](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=resources)，点击Edition获取其它平台的，缺点需要梯子和登录docker-store账号。  
2. 安装
一个不好的地方，安装文件都叫InstallDocker.msi，不知道版本新不新。  
DaoCloud下载的是100多MB的完整安装文件，易于周围人分发。
阿里云下载的是1MB的引导程序，启动后显示正在配置中，其实是在线下载。但遗憾的是过了一会没有任何提示引导框就消失了，我以为失败了，结果在启用hyper-v重启电脑后发现已经安装上去了。
所以用任一平台下载的文件安装都可以。
3. 重启后可以看到Hyper-V的快捷方式和桌面的docker快捷方式
![](5.png)
### 4.（略过）测试启动docker和hyper-v看看是否正常
如果你启动正常，可以略过这部分。
启动docker提示没有权限（之前没出现过，难道是管理员名字不是Administrator的缘故？），当前用户需要加入docker-users权限组。
![](6.png)
进入**桌面-计算机（右键）-管理-本地用户和组-组**
![](7.png)
查看当前登录的用户，一般为“Administrator”或装系统时起的名字。系统锁屏，windows徽标，用户中能看到当前登录的用户名。我这里叫“win10”。
![](8.png)
双击docker-users用户组，调出对话框，把用户名添加到用户组里
![](9.png)
再次启动Docker for Windows，提示需要注销，刚才变更用户组才能生效。注销在windows菜单用户头像那里单击。
![](10.png)
再次启动，提示docker is runing，工具栏的小鲸鱼图标在变动，表示启动中。过一会弹窗提示需要重启，刚开始就是麻烦ε=(´ο｀*)))唉，这个时候就显得不如linux方便了。
![](11.png)
重启后docker已自动启动，成功后鲸鱼小图标不变，一个漂亮的欢迎页面（没截上图不过没用处），右键可以调出菜单，即为成功。下图是菜单里的setting首页。
![](12.png)
启动Hyper-V，正常，并且可以帮助理解docker技术：**（虽然windows有自己的容器化技术但不完善，）大家用的docker主要是管理linux上的容器化技术，mobyLinux就是一个专门运行docker的linux发行版。
在旧版本Windows使用的WindowsTools中，VMBox中运行的是boot2docker（记不清名字了）的linux系统。
总之，就是先运行了一个Linux虚拟机，然后运行linux的容器化。（怪不得Windows搞起docker来这么麻烦，中间多了一个虚拟机。）**
![](13.png)

## 使用
基础语法 todo
与pycharm集成和可视化工具
## license
原创文章 转载注明出处
封面图来自[docker_pride_by_dribbble_Sarah_Park](https://dribbble.com/shots/2811017-Docker-Pride)