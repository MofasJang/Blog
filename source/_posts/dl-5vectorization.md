---
title: 深度学习中的向量化
copyright: false
date: 2021-02-23 12:35:48
tag:
- 深度学习
- 毕设准备
categories:
- 毕设准备
- 深度学习
---
{% note info %}
课程名称：[吴恩达深度学习课程](https://www.bilibili.com/video/BV164411m79z)
学习资料：[深度学习教程中文笔记](http://file.panjiangtao.cn/Deeplearning%E6%B7%B1%E5%BA%A6%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0v5.71.pdf)
{% endnote %}

# 极大提高计算效率
　　在逻辑回归中计算$z=w^{T}x+b$时，w，x都是列向量。

　　非向量方法：
```py
z=0
for i in range(n_x)
z+=w[i]*x[i]
z+=b
```
<!-- more -->
　　向量方法：
```py
z=np.dot(w,x)+b
```
　　使用一个具体的例子，
```py
import numpy as np #导入 numpy 库
import time #导入时间库
a = np.random.rand(1000000)
b = np.random.rand(1000000) #通过 round 随机得到两个一百万维度的数组
tic = time.time() #现在测量一下当前时间
#向量化的版本
c = np.dot(a,b)
toc = time.time()
print(“Vectorized version:” + str(1000*(toc-tic)) +”ms”) #打印一下向量
化的版本的时间
#非向量化的版本
c = 0
tic = time.time()
for i in range(1000000):
c += a[i]*b[i]
toc = time.time()
print(c)
print(“For loop:” + str(1000*(toc-tic)) + “ms”)
```
　　向量化的方法几乎要快300倍。
　　其原因是深度学习基本上由CPU或GPU来完成计算，他们都有并行化的指令，称为SIMD(单指令流多数据流)，而高级语言中的内置函数(如np.dot)可以更好地利用并行化进行计算，其中GPU比CPU更擅长SIMD处理。

# 更多向量化例子
### `u=np.exp(v)` 可以使列向量v的每一项$v_{i}$都变为$e^{v_{i}}$。

    * np.log(v)——每一项求自然对数
    * np.abs(v)——每一项求绝对值
    * np.maximum(v,0)——每一项是否比0大

### 在逻辑回归中
　　原本需要使用二重for循环：
```py
J=0,db=0
for i = 1 to n:
    dw(i) = 0
for i = 1 to m:
    z(i) = wx(i)+b
    a(i) = sigmoid(z(i))
    J += -[y(i)log(a(i))+(1-y(i)）log(1-a(i))
    dz(i) = a(i)-y(i)
    for j = 1 to n:
        dw(j) += x(j)(i)dz(i)
    db += dz(i)
J/= m
for i = 1 to n:
    dw(i) /= m
db/= m
w=w-alpha*dw
b=b-alpha*db
```
　　在经过简单向量化后，代码具有一重for循环，变为：
```py
import numpy as np
J=0,dw=np.zeros(n_x,1),db=0
for i = 1 to m:
    z(i) = wx(i)+b
    a(i) = sigmoid(z(i))
    J += -[y(i)log(a(i))+(1-y(i)）log(1-a(i))
    dz(i) = a(i)-y(i)
    dw = np.dot(x,dz.T)
    db += dz(i)
J/= m
dw /= m
db/= m
w=w-alpha*dw
b=b-alpha*db
```

# 终极向量化
##　求取z
　　只需一行代码即可完成逻辑回归中z的计算，即`Z=np.dot(w.T,X)+b`。

　　$[z^{(1)} z^{(2)}...z^{(m)}]=w^{T}X+[b b...b]=[w^{T}x^{(1)}+b,w^{T}x^{(2)}+b...w^{T}x^{(m)}+b]$ 。

　　$w^{T}x^{(1)}+b$ 这是第一个元素，$w^{T}x^{(2)}+b$ 这是第二个元素， $w^{T}x^{(m)}+b$ 这是第 $m$ 个元素。

　　这里在 Python 中有一个巧妙的地方，这里 𝑏 是一个实数，或者你可以说是一个 1 × 1 矩阵，只是一个普通的实数。但是当你将这个向量加上这个实数时，Python 自动把这个实数 𝑏 扩展成一个 1 × 𝑚 的行向量。所以这种情况下的操作似乎有点不可思议，它在 Python 中被称作广播(brosdcasting)。

　　而$A=[a^{(1)} a^{(2)} ... a^{(m)}]=\sigma (Z)$可以高效输出A。
##　去掉循环
```py
Z = np.dot( w.T,X)+b
A = sigma( Z )
dZ = A - Y
dw = 1/m*np.dot(x,dz.T)
db= 1/m*np.sum(dZ)
w: = w - a*dw
b: = b - a*db
```