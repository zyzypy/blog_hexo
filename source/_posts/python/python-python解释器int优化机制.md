---
title: python解释器int优化机制
comments: true
toc: false
date: 2018-07-30 16:27:26
updated:
tags:
- 解释器
- python
categories:
- python
banner:
---
## 内容
整数在程序中的使用非常广泛，Python为了优化速度，使用了小整数对象池， 避免为整数频繁申请和销毁内存空间。
Python 对小整数的定义是 [-5, 256] 这些整数对象是提前建立好的，不会被垃圾回收。在一个 Python 的程序中，无论这个整数处于LEGB中的哪个位置，
所有位于这个范围内的整数使用的都是同一个对象。同理，单个字母也是这样的。
```python
>>> a = 1 
>>> b = 1 
>>> a is b   
True
  
>>> a = 257 
>>> b = 257 
>>> a is b  
False
```
注意不是这种情况
```python
>>> a=1234
>>> b=a
>>> a is b
True
```
## license
转自 [CSDN-python-shilei-0604的博客](https://blog.csdn.net/shilei123456789666/article/details/78046761)
