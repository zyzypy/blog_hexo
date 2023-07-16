---
title: windows安装pyCrypto包报错 | py36安装pyCryptodome
comments: true
toc: false
date: 2018-05-08 13:54:37
updated:
tags:
- python
- 加密
- 科学计算
- pip
- 包
categories:
- python
banner:
---
## py2.7-py3.5 安装pyCrypto
pip install pycrypto  
报错：could not find a satisfied package for this system  
原因：依赖vc编译器导致安装失败  
### 解决
下载编译好的.whl安装包
参考链接[PyCrypto-Wheels](https://github.com/sfbahr/PyCrypto-Wheels)
安装命令for py2.7-py3.5 64位windows系统
```bash
pip install --use-wheel --no-index --find-links=https://github.com/sfbahr/PyCrypto-Wheels/raw/master/pycrypto-2.6.1-cp35-none-win_amd64.whl pycrypto
```

## py3.6
上述命令在py3.6下报错，经查阅pycrypto停止维护[pycrypto停止维护的声明](https://github.com/dlitz/pycrypto/issues/238)，改名**pycryptodome**,
pycrypto is Dead😂 .  
pycryptodome在pypi上的主页[pycryptodome on pypi](https://pypi.org/project/pycryptodome/)
### 解决
```bash
pip install pycryptodome
```
不依赖vc编译器，很顺利安装完成，包语法与之前一致，如下测试。  
```bash
>>> from Crypto.Cipher import AES
>>>
```
不报错成功

### 题外话
现在的py新包都不怎么依赖编译器，比原来安装报错恼人好很多。但可能由于不像nodejs有package.json管理版本，py的包改名别人不知道😂，前两天安装beautifulsoup报错，最后发现叫beautifulsoup4😂。