---
title: pycharm解释器使用docker
comments: true
toc: true
date: 2018-08-03 14:09:11
updated:
tags:
- docker
- linux
- pycharm
- 解释器
categories:
- [服务器]
- [软件]
banner:
---
### 0.前置条件
本文环境：
- Windows10 专业版 1803 x64
- 已安装Docker for Windows 社区版(server 18.06.0ce)。 安装教程详见{% post_link 服务器/服务器-dockerForWindows安装 服务器-dockerForWindows安装 %}
- pycharm 专业版 2018.1
 
### 1.准备新镜像
需要已会docker基本操作：详见{% post_link 服务器/服务器-docker常用命令 docker常用命令 %}
前期我们在终端powershell或cmd中操作，我这里用的是pycharm内置的终端。后面用pycharm的图形工具接手docker命令行的功能。
1. 下拉python镜像:
    ```shell
    docker pull python:3.6.6
    ```
    此文书写时python3.7.0刚发布，所以我选用python3.6.6，在docker-hub看到python仓库下有多种tag，有3.6.6-alpine、3.6.6-stretch。
    后面测试时发现3.6.6-alpine run 容器直接进入python交互命令行，无法pip安装其它包（应该是我操作不对），pycharm配置失败。而3.6.6等同于3.6.6-stretch，大小为920M，拥有正常的linux目录，可以pip，pycharm配置成功。
    ![](1.png)
    你也可以下载ubuntu镜像再装python，配置自己的镜像。
2. run 容器，进入容器：
    ```shell
    docker run -it python:3.6.6 /bin/bash
    ```
    -i 交互式 -t 伪终端 /bin/bash打开终端，总之这句话表示进入容器终端，跟ssh远程服务器一个感觉。
3. 在容器中进行修改
    在容器的bash终端下操作，修改运行环境为自己想要的。  
    比如我要运行爬虫坏境，所以我先确认一下容器的环境确实是py3.6.6，目录也是普通的linux目录，然后安装了一些python包：
    ```bash
    root@d81614c6e82a:/# python -V      
    root@d81614c6e82a:/# ls
    root@d81614c6e82a:/# pip install requests lxml breautifulsoup4 scrapy
    ```
    修改完成后exit退出容器。
4. 提交
    语法`docker commit [options] [container_ID] [new_image_name]`。
    本例中我这里是
    ```shell
    docker commit -a "yangzheng" -m "my spider env" d81614c6e82a myenv:spider
    ```
    可选参数 -a --author作者，-m --message信息，空格后跟引号跟值，不能写成 -a=""yangzheng"这种形式。  
    容器ID为上一步的修改的容器。后面可以直接跟镜像名，我这里写的完整点为 仓库名:镜像名。
5. (选做)push远程
    todo
6. (选做)如果需要再次修改和提交
    基于上次提交的镜像重复上述步骤
    或基于上次修改后的容器从第3步重复
    ```bash
    docker ps -a    # 查看之前运行的容器，复制ID
    docker start [container ID]     # 运行之前的容器
    docker attach [container ID]    # 进入容器
    ```
### 2.docker设置
1. 暴露docker进程以跟pycharm交互通信。
系统右下工具图标右键打开docker settings。左侧菜单选daemon，勾选export daomon on tcp。 
![](5.png)
不操作此步骤会导致pycharm配置解释器时无法connect docker failed。
2. 共享磁盘
左侧目录sharedisk，勾选要共享的磁盘。
![](6.png)
在老版本的docker windowsTools中需要在VMbox虚拟机中允许共享本地目录，然后容器 -v 本地目录：容器里目录 实现run容器挂载代码目录。
但我的代码在D盘，后面pycharm没有配置其它的映射运行成功，所以疑问这一步是否可以省略。如果后面pycharm中配置磁盘映射，这里没配的话，运行项目时会请求分享磁盘。
![](7.png)
共享磁盘会让输入windows账户名密码，密码不能为空，如果没有给账户设置密码需要到控制面板-账户中设置，设置密码不详述。

### 3.pycharm设置
1. pycharm左上角菜单File-Settings或快捷键`Ctrl+Alt+S`打开设置，点击左侧菜单"project:[projectname]",点击interpreter
![](10.png)
2. 点击右侧 齿轮图标> add 添加新的解释器。
对话框左侧选择docker，server下拉框没有的话点击**new**
![](11.png)
点击ok。
重要：如果之前没有在docker settings中暴露tcp进程的话，这里pycharm将会无法连接docker。
如果之前没有在docker settings中共享磁盘，点击ok会请求共享。共享磁盘这一步可以试试省略，因为发现不影响后面运行。
3. 配置好server后images下拉菜单就可以选择存在的镜像，选择刚才准备的镜像。解释器路径为命令python，也可能为python3，根据之前制作镜像时在容器中的操作判断。
![](12.png)
选好后点OK。
4. 解释器变更，显示的包是docker image里的三方包。点击OK，解释器更换完毕。
![](13.png)
5. 测试。
可以看到运行配置里的解释器指向docker image。实际上，运行就是run了一个容器。
![](14.png)
我这里是一个scrapy项目，运行begin.py,项目运行成功。
![](15.png)

### 4.其它
pycharm配置docker成功后有一个可视化图形管理工具，run容器start容器看log之类的，用处不大。
![](16.png)
pycharm终端需要重启软件后生效。

## Mac/ Unbuntu
todo
大体相似，做到再更

## 整体评价
不建议使用以前的Tools kit for windows，尽量用win10和docker for windows，但仍然步骤繁多。    
虚拟机一开，开俩项目，2G内存是吃定了。个人还是不太喜欢docker：程序员开发完最后用docker跑下项目测试就行了，平时本地环境就挺好。
可能docker适合批量部署服务器和熟练shell的运维人员才能发挥出他的优势。

## license
原创文章，转载注明出处，禁止商用。