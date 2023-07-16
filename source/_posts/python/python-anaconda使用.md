---
title: python-anaconda使用
comments: true
toc: false
date: 2018-08-05 09:42:22
updated:
tags:
categories:
banner:
---
todo 未完成


### (小坑)安装找不到的包
发现很多包都找不到，比如我想安装一个发邮件的包，yagmail ，1.1k star。执行search或install如下：
```powershell
PS> conda install yagmail
PackagesNotFoundError: The following packages are not available from current channels:

  - yagmail

Current channels:

  - https://repo.anaconda.com/pkgs/main/win-64
  - https://repo.anaconda.com/pkgs/main/noarch
  - https://repo.anaconda.com/pkgs/free/win-64
  - https://repo.anaconda.com/pkgs/free/noarch
  - https://repo.anaconda.com/pkgs/r/win-64
  - https://repo.anaconda.com/pkgs/r/noarch
  - https://repo.anaconda.com/pkgs/pro/win-64
  - https://repo.anaconda.com/pkgs/pro/noarch
  - https://repo.anaconda.com/pkgs/msys2/win-64
  - https://repo.anaconda.com/pkgs/msys2/noarch

To search for alternate channels that may provide the conda package you're
looking for, navigate to

    https://anaconda.org

and use the search bar at the top of the page.
```
报错：包没找到。    
(网上一些教程显示旧版本的提示是"anaconda -t search conda yagmail",测试已不可用).
提示去[官网](https://anaconda.org)搜索,搜索框键入"yagmail",搜到结果有人上传这个包(版本有点老)，键入提示安装命令
```powershell
conda install -c jrkerns yagmail 
```
安装成功。