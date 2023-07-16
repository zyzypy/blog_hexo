---
title: 各系统下软件包管理器Chocolatey/Homebrew/Apt-get
comments: true
toc: false
date: 2018-04-09 12:12:32
updated:
tags:
- Windows
- Mac
- Linux
categories:
- 软件
banner:
---

## Chocolatey （Windows) 
windows的包管理器是个新东西，因为Linux的命令行包管理器足够通用强大，mac上也有不错的homebrew，程序员们问：windows
上有没有一款命令行下载软件的工具？chocolatey应运而生
### 介绍
官网[https://chocolatey.org/](https://chocolatey.org/)
![](chocolatey1.png)
Windows上管理软件的明智方法（The sane way to manage software on Windows）
### 安装chocolatey
[官方安装文档Installing Chocolatey]()https://chocolatey.org/install)
没有网上下载的.exe图形界面安装程序哦，现在开始习惯命令行😀
1. 打开cmd(管理员)或powershell(管理员)：  
    方式一：开始菜单（左下windows图标）/所有程序/Windows系统/命令提示符。其实是C:\windows\System32\cmd.exe的快捷方式  
    ![](chocolatey2.png)
    方式二：开始菜单/右键菜单
    ![](chocolatey3.png)
    已打开：
    ![](chocolatey4.png)
    注意：
    - 管理员模式，因为安装软件对于操作系统来说是敏感操作，需要权限。只需在想要打开的程序上**右键**就能看到**以管理员方式运行**  
    管理员模式下窗口标题有提示，而且路径是C:\windwos\System32,如上图
    非管理员shell下安装(不推荐看上去麻烦)见[官方文档Non-Administrative install](https://chocolatey.org/install#non-administrative-install)
    - cmd还是powershell？都行，推荐powershell。powershell是cmd的替代品，已日趋完善。powershell美观度、一些命令行工具的信息排版好，其它各种好不是搞win系开发的体会不到，简单使用没区别。
    - 本文系统为win10_x64_1709版本。 较老的版本中，cmd在附件中，开始菜单右键打开的菜单中为cmd而不是powershell这些小区别  
2. 拷贝安装命令到cmd或powershell
    - 如果你打开的是cmd  
    ```shell
    @"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
    ```
    - 如果你打开的是PowerShell
    ```shell
    Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
    ```
    *解释：cmd的那条命令其实是先调用的powershell，powershell先设置了下权限，然后从[https://chocolatey.org/install.ps1](https://chocolatey.org/install.ps1)
    下载了安装脚本并执行*
3. 粘贴命令，回车
powershell中快捷键仍然是鼠标右键复制或`ctrl`+`V`，另外记得`shift`+`ins` `shift`+`del`在cmd中表示粘贴复制  
![](chocolatey5.png)
可以看到从指定网址下载安装包，下载速度较慢，100kb/s，不过不大一分钟就好。。
有一些warning，提示可能需要重新关闭打开powershell窗口，提示检查**win环境变量**中是否添加了powershell，如果没有需要手动添加。否则choco命令
会报错不是有效的win32程序。环境变量相当于命令行程序的快捷方式，不懂的具体百度，后面安卓工具也会用。虽然现在大多数工具都会自动添加环境变量了，不过
个别时候如果没加上要会自己添加。
4. 安装完成
输入命令查看版本和帮助，看到信息即为成功。*choco命令不是有效的win32程序*的，重启下powershell或重启电脑。
```shell
choco -V
choco -?    # 帮助信息一堆，往上翻，常用命令在前面
```
5. 其它
本地安装（不建议）:官方文档[Completely offline install](https://chocolatey.org/install#completely-offline-install),出了下载zip包还要配置一个脚本文件看上去也麻烦

### 使用
常用命令
```
choco list  # 已安装软件
choco search [software name]    # 查询想要安装的软件
choco install [software name]  # 安装软件
choco install [software name]  --version 3.5.4 # 安装特定版本 安装预览版 具体上官方仓库查询然后点击小版本新页面有完整命令
upgrade [software name]     # 升级软件到最新版
uninstall [software name]   # 卸载
```
其余的pack push应该是上传软件到官方仓库，未深度使用，未尝试

### 官方仓库
[packages](https://chocolatey.org/packages)
可以自己更新自己，还看到了chrome、office、TIM、wechat、docker等常用软件。信息详尽舒适，命令，各种版本，依赖
![](chocolatey安装py1.png)

### 安装python3
{% post_link python/python-安装python3（win-mac-linux） 安裝python3 %}

### 提高下载速度
服务器保存的只是软件下载源地址，而不是binary，所以不能指望换国内源来提高下载速度。  
[官方文档Installing Chocolatey behind a proxy server](https://chocolatey.org/docs/proxy-settings-for-chocolatey#installing-chocolatey-behind-a-proxy-server)，
未尝试，一个用户名一个密码好像是~~vp和谐n~~的方式，命令行帮助信息里也有相关参数。  
已测试~~s和谐sr~~**全局代理**可以提高速度  

### 优缺点
- 官方仓库信息详尽舒适，命令，各种版本，依赖都很清楚  
- （没研究肯定有这个功能）把常用软件写到一个requirement.txt，换电脑的时候执行一个脚本自动安装所需软件，省力气

- 目前算是尝鲜的东西。使用者较少，网上有反馈存在一些安装错误、误删除等bug，因为未深度使用不做评论  
- 大多数开发者已经习惯win的图形化和linux的命令行，win上的命令语法不习惯  
- 网速一般，本人网速不挂代理200kb左右  


---

## Homebrew  (MacOS)

1. 打开终端  
lunchpad/terminal 或 `cmd`+`空格`搜搜"terminal"  
![](homebrew1.png)
2. 复制命令粘贴到终端，回车
```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
![](homebrew2.png)
已经装过了，没截过程图,记得过程提示很详细
3. 完成
```bash
brew help
```
可以看到命令说明
![](homebrew3.png)
安装过程中需要输入管理员密码
*解释*从命令中看到，这个工具是基于ruby开发的
据说homebrew作者不会不写翻转二叉树被谷歌拒了，最后去苹果。。😂
### 使用 常用命令
wget是一个网页小工具，用它来做例子，指代你想安装的软件如python mysql-server nodejs
```bash
brew install wget       # 安装软件
brew uninstall wget     # 卸载软件
brew list               # 已经安装的软甲列表
brew search wget        # 查找想要的软件包
brew update             # 更新软件列表，如果一段时间没用，安装软件前会先更新自己
brew upgrade wget       # 升级已经安装的软件的版本
```
### 安装后的文件在哪
homebrew(自酿啤酒🍺)安装后的文件在Callar(酒窖)中，😅冷笑话
Homebrew 会将软件包安装到独立目录，访达（Finder）快捷键`cmd`+`shift`+`G`输入`/usr/local/cellar`进入（因为finder默认隐藏了usr目录），
并将其文件软链接至 /usr/local
```bash
$ cd /usr/local
$ find Cellar
Cellar/wget/1.16.1
Cellar/wget/1.16.1/bin/wget
Cellar/wget/1.16.1/share/man/man1/wget.1

$ ls -l bin
bin/wget -> ../Cellar/wget/1.16.1/bin/wget
```
![](homebrew4.png)

### 提高下载速度
brew的速度也挺慢，还算稳定100kb/s（联通50mbps)
参考其它文章，未测试[更换Homebrew的更新源](https://blog.csdn.net/u010275932/article/details/76080833)


## Apt-get  (Linux debian系)

yum类似，不叙述了
### 提高下载速度 换源
编辑
- 阿里
- 

## 理解包管理器
解决依赖： 软件越来越复杂，人们写代码是需要服用别人的代码，比如安装python中的flask包会安装几个其它的包Werkzeug(http)、Jinja2(前端渲染)、
MarkupSafe(防止跨站请求攻击)。安装A包, A依赖B、C， B依赖D。如果手动安装的话，经常安装失败，缺少xx依赖之类的。  
客户端和服务端：服务端根据kernel版本号和cpu版本号分别编译发布，ftp或http形式提供下载  
快捷：当你需要安装几十个软件时，百度-到达官网-点下载页-点下载-解包-安装，将会花费大量时间。命令行根据requirement.txt或package.json能批量
安装。软件官网教程直接放一条命令就行  

*备注：主要介绍各OS系统下的软件包管理器，python的pip，nodejs的npm等各自框架生态下的开发包管理器不在本文讨论*  

## license
未完成 原创文章