---
title: python-scrapy安装（windows环境下）
comments: true
toc: false
date: 2018-07-31 15:21:29
updated:
tags:
- python
- 爬虫
- 三方包
- 环境
categories:
- python
banner:
---
## 问题
windows下安装一些偏底层系统调用或调用C写的三方模块时，pip非常容易安装失败，因为缺少VC编译器。这一点windows不如mac或ubuntu自带编译环境。
## 本文环境
- win10 x64
- py3.6
- pip 18
## 一些解决方案和选择：
1. （不推荐）安装VC编译器。需要安装VisualStudio全家桶，很重。
2. （本文基于这种方式）pip + 专门下载编译后的包的网站[python extentions for windows](https://www.lfd.uci.edu/~gohlke/pythonlibs/),喜欢pip的官方自带和轻量。 
3. （推荐）anaconda。scrapy的官网建议使用anaconda，anaconda我感觉还是挺重了，下载600m装完5G。又试了下"miniconda"，下载100多M装完300Mb，感觉应该不错。  
从最近的趋势来看anaconda呼声挺高，所以你可以直接选择anaconda，而不必看这篇文章。。  
最近注意到pip也在进步，版本从9不知觉到18。安装一些包的时候看日志其实也是先wget编译好的.wheel文件。但在安装scrapy的时候，还是出现问题。  
吐槽：windows上的用户也不少怎么就这么不被待见呢。windows因为镜像过大所以才内置VC编辑器吗。pip作为官方工具为什么不能像conda一样呢。我先不去想这些问题。
## 已过时的内容
一些博文显示需要安装scrapy、lxml、pycrypto、pywin32等的.wheel文件。经测试发现一些包已可以直接pip安装成功。

## 正文
1. 安装twisted
    首先尝试`pip install scrapy`, 发现会卡到twisted包。
    这个网站[python extentions for windows](https://www.lfd.uci.edu/~gohlke/pythonlibs/)的用法，多用ctrl+F找到想要的包。  
    这是twisted的链接[https://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted](https://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted)。  
    因为我的环境，我选择的是“Twisted‑18.7.0‑cp36‑cp36m‑win_amd64.whl”。
    下载好后cd到文件所在目录 `pip install Twisted‑18.7.0‑cp36‑cp36m‑win_amd64.whl`。
2. 安装scrapy
    ```bash
    pip install scrapy
    ```
    然后会安装约十个依赖包，显示安装成功。等于说只安装了一个.whl文件，并不难。如果你遇到某个包出现不能安装，试试上一步的方式解决。
3. 安装pywin32
    运行时发现还少个东西，pywin32不少windows底层调用相关的功能都会用到。原来记得要安装要下载相应的exe文件。  
    这次发现进步不小支持pip安装了。详见项目github主页[pywin32](https://github.com/mhammond/pywin32)。
    ```bash
    pip install pywin32
    ```
    这时scrapy项目已经能运行。
    (选做)安装完pywin32再cd到python解释器下的Scripts目录，进一步安装一个脚本获得更底层的win32api功能。
    ```bash
    cd C:/python36/Scripts
    python pywin32_postinstall.py -install
    ```
    执行完后发现python解释器根目录多了俩dll文件。

## license
原创文章 转载请署名