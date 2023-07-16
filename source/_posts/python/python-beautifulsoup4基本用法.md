---
title: python-beautifulsoup4基本用法
comments: true
toc: false
date: 2018-07-28 17:09:52
updated:
tags:
- 第三方包
- python
- 爬虫
- 解析
categories:
- python
banner:
---
## 介绍 & 版本选择
beautifulsoup是一个解析网页提取所需数据的库。  
beautifulsoup3已停止维护，py3安装的话记得会失败，第一次安装时想这么流行的库为什么装不上，搜索许久后在github上查到了作者的声明。所以请直接安装beautifulsoup4。  
html解析器选择，一个是python自带的'html.parser'；还有一个是第三方的'lxml',C写的，解析效率更高。
## 安装包
```bash
pip install beautifulsoup4
```
（可选）lxml解析器
```bash
pip install lxml
```
## 例子
html的内容我们事先定义好。 BeautifulSoup将html转换成python树形结构，每个节点都是一个对象。  
然后根据节点、id、attrs、class、content等特征查找。
```python
from bs4 import BeautifulSoup   

html = """
<html>
    <body>
        <a id="aaa" href='http://www.baidu.com' name='aaa' class='aaa'>百度一下</a>
        <a href='http://www.baidu.com'>百度一下2</a>
        <h1>大家好</h1>
    </body>
</html>
"""

bs = BeautifulSoup(html, 'lxml')    # 第一个参数为html内容，第二个参数是解析器，两种解析器选一个
# bs = BeautifulSoup(html, 'html.parser')
# 打印bs对象
print(bs.prettify())        # 格式化输出html内容

# ========= 访问标签的名字、属性、内容 ============
# 查找标签
print(bs.title)     # 没找到的返回None
print(bs.head)
print(bs.a)     # 返回第一个
print(bs.find_all('a'))     # 返回列表
# 标签名称
print(bs.name)      # 返回值根文档'[document]'
print(bs.a.name)    # a
# 标签的属性
print(bs.a['href'])
# 删除标签的属性
del bs.a['id']
print('删除a标签的id属性后{}'.format(bs.a.attrs))   # 返回字典
# 标签内容
print(bs.a.string)


# ========= 操作节点 ============
# 遍历父节点
# print(bs.head.contents)
print(bs.body.contents)
# 遍历父节点，返回迭代器
res = bs.body.children
for r in res:
    print(r)
# 获取父节点
print(bs.a.parent.name)

# ========= 搜索 ============
# 根据标签
print(bs.find_all('a'))
print(bs.find_all(['a', 'h1']))
# 根据属性
print(bs.find(id='aaa'))
# 根据class
print(bs.find_all(class_='aaa'))     # 因为class是关键字，所以class_
# 通过css语法 选择器
print(bs.select('a'))
print(bs.select('.aaa'))
print(bs.select('#aaa'))
print(bs.select('body .aaa'))
```