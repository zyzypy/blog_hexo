---
title: 硬件-树莓派ssh
comments: true
toc: false
date: 2018-05-07 22:33:53
updated:
tags:
- 树莓派
- ssh
- vnc
- 远程控制
categories:
- 硬件
banner:
---
## 意义
远程控制树莓派，ssh和vnc能让树莓派摆脱连接外设，
## ssh
### 意义
建议前期树莓派接键鼠和显示器，方便进行设置。电脑远程连接成功后就可以撤掉树莓派外设了。  
实在没有外设的利用树莓派默认已开启ssh服务和拥有默认用户名密码的特点进行登录。  
传输文件，提高调试和工作效率。
### 准备工作
1. 获得树莓派ip
连接有线网或启动树莓派后右上角菜单连接wifi。  
确保pc和树莓派在同一个路由器下。  
方式一： 连接键鼠显示器外设。左上角菜单栏打开terminal（终端）
```bash
ifconfig
```
出来一堆信息，长得像192.168.x.xxx的是ip地址，记下这个地址
方式二：进入路由器管理界面，查看已连接的设备，各品牌路由界面大同小异  
[](0.png)
2. 开启ssh服务。默认已开启。可以在rasp-config/advanced/ssh 中打开关闭服务
3. 准备ssh client工具。windows端的putty等。笔者用的pycharm自带的工具，因为不用新下载安装软件，jetbrain家的ide基本大家都有一个吧，工具启动方式 菜单>tools>start ssh session。
### 连接登陆
1. 登陆：打开工具，填上树莓派的ip地址，端口（默认22），用户名（默认 pi），密码（默认 raspberry）
不能登陆的检查准备阶段第1步
2. 启用root账户，设置root密码： （为了以后连接和操作方便）
```bash
pi@raspberrypi:~$
pi@raspberrypi:~$ sudo passwd root
pi@raspberrypi:~$ 设置新的密码:
pi@raspberrypi:~$ 重复密码：
pi@raspberrypi:~$ su root
pi@raspberrypi:~$ 输入root的密码
root@raspberrypi:~# 
```
初次使用linux的小伙伴注意输入密码时屏幕无变化，不会像平时一样出现 *****  
su root成功后 **$** 变成 **#**
5. 修改配置文件，允许root登录
处于安全考虑，ssh服务默认禁止root用户连接  
编辑配置文件
```bash
nano /etc/ssh/sshd-config
```
注释掉下面这句话允许root登录，并更改值为**yes**允许密码登录
```bash
# PermitRootLogin prohibit-password
```
修改后为
```bash
PermitRootLogin yes
```
ctrl+O 保存 ，ctrl+X 退出编辑器 
6. 尝试用root再次登陆
用户名（root）密码（上面设置的）
[](0-2.png)
登陆失败的检查第5步
## vnc
0. 查找ip（同上ssh准备工作第1步）
1. 树莓派开启vnc服务。默认关闭
```bash
sudo rasp-config
```
advance>VNC>yes  
配置菜单首页>finish  
提示是否reboot（重启）  
重启后桌面右上角显示vnc图标，服务已开启
1. 下载vnc client工具
realvnc官网[realvnc](https://www.realvnc.com/en/)，下载电脑对应平台版本的软件
[](1.png)
2. 安装
[](2.png)
[](3.png)
3. 连接
输入树莓派ip地址，软件会自动补齐默认vnc端口5900。
服务运行正常和连接正确的话第一次连接会让输入用户名和密码（同上ssh）  
树莓派未开启vnc服务的话会提示找不到服务，无法输入用户名密码，检查第0、1步  
4. 成功
显示界面
[](4.png)
嫌分辨率小的去rasp-config中调整分辨率

## license
原创文章，转载注明出处