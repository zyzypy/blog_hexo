# 函数|数据结构的高级用法。
不要求掌握，for循环可以实现同样功能，但下面的用法可以
使程序更简洁。

### 匿名函数
def add1(a, b):
    return a + b
    
lambda a:a+b

适用场景，函数较为简单。经常与map连用。
### map
map(fx(), sequence)

map(lambda x:x*x, [1,2,3,4,5])

for x in [1,2,3,4,5]:
    return x*x
    
### reduce
py3中从全局中移除放入functiontools
from functiontools import reduce
reduce(lambda x, y: x+y, [1, 2, 3, 4, 5]) calculates
传入两个参数，定义函数功能。先计算前两个值，得到结果后作为第一个值与后面的值继续运算。
相当于((((1+2)+3)+4)+5)

