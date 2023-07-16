---
title: python-re模块的match、search、findall、finditer区别
comments: true
toc: false
date: 2018-07-25 15:39:24
updated:
tags:
- python
- 三方包
- 正则
- 爬虫
categories:
- python
banner:
---

## 区别
### match
```python
re.match(pattern, string[, flags])
```
从首字母开始开始匹配，string如果包含pattern子串，则匹配成功，返回Match对象，失败则返回None，若要完全匹配，pattern要以$结尾。
### search
```python
re.search(pattern, string[, flags])
```
若string中包含pattern子串，则返回Match对象，否则返回None，注意，如果string中存在多个pattern子串，只返回第一个。
### findall
```python
re.findall(pattern, string[, flags])
```
返回string中所有与pattern相匹配的全部字串，返回形式为数组。
### finditer
```python
 re.finditer(pattern, string[, flags])
```
返回string中所有与pattern相匹配的全部字串，返回形式为迭代器。
### group groups
若匹配成功，match()/search()返回的是Match对象，finditer()返回的也是Match对象的迭代器，获取匹配结果需要调用Match对象的group()、groups或group(index)方法。
group()、groups()与group(index)的区别，如下所示：
```python
>>> import re
>>> s = '23432werwre2342werwrew'
>>> p = r'(\d*)([a-zA-Z]*)'
>>> m = re.match(p,s)
>>> m.group()
'23432werwre'
>>> m.group(0)
'23432werwre'
>>> m.group(1)
'23432'
>>> m.group(2)
'werwre'
>>> m.groups()
('23432', 'werwre')
>>> m = re.findall(p,s)
>>> m
[('23432', 'werwre'), ('2342', 'werwrew'), ('', '')]
>>> p=r'(\d+)'
>>> m=re.match(p,s)
>>> m.group()
'23432'
>>> m.group(0)
'23432'
>>> m.group(1)
'23432'
>>> m.groups()
('23432',)
>>> m=re.findall(p,s)
>>> m
['23432', '2342']

```
- group()：母串中与模式pattern匹配的子串；
- group(0)：结果与group()一样；
- groups()：所有group组成的一个元组，group(1)是与patttern中第一个group匹配成功的子串，group(2)是第二个，依次类推，如果index超了边界，抛出IndexError；
- findall()：返回的就是所有groups的数组，就是group组成的元组的数组，母串中的这一撮组成一个元组，那一措组成一个元组，这些元组共同构成一个list，就是findall()的返回结果。另，如果groups是只有一个元素的元组，findall的返回结果是子串的list，而不是元组的list了。

## 例子
`s ="1113446777"`
用正则表达式把s分为1111, 3, 44, 6, 777
```python
>>> import re
>>> s='1113446777'
>>> m = re.findall(r'(\d)\1*',s)
>>> print m
['1', '3', '4', '6', '7']
>>> m = re.search(r'(\d)\*',s)
>>> m.group()
>>> m=re.search(r'(\d)\1*',s)
>>> m.group()
'111'
>>> m.groups()
('1',)
>>> m.group(0)
'111'
>>> m.group(1)
'1'
>>> m.group(2)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: no such group
>>> m=re.finditer(r'(\d)\1*',s)
>>> m.next().group()
'111'
>>> m.next().group()
'3'
>>> m.next().group()
'44'
>>> m.next().group()
'6'
>>> m.next().group()
'777'
>>> m.next().group()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration

```
另一个例子
```python
>>> p = r'(\d)\1+([a-zA-Z]+)'
>>> s = '1111werwrw3333rertert4444'
>>> p = r'(\d)\1+([a-zA-Z]*)'
>>> import re
>>> re.findall(p,s)
[('1', 'werwrw'), ('3', 'rertert'), ('4', '')]
>>> m = re.search(p,s)
>>> m.group()
'1111werwrw'
>>> m.group(1)
'1'
>>> m.group(2)
'werwrw'
>>> m.groups()
('1', 'werwrw')
>>> m = re.finditer(p,s)
>>> m.next().group()
'1111werwrw'
>>> m.next().group()
'3333rertert'
>>> m.next().group()
'4444'
>>> m.next().group()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration

```

## license
转自[CSDN-djskl的博客](https://blog.csdn.net/djskl/article/details/44357389)