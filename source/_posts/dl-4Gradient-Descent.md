---
title: 梯度下降法
copyright: false
date: 2021-02-20 14:07:27
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
　　我们通过使代价函数最小化来训练参数$w$和$b$。
![代价函数J(w,b)](cbd5ff8c461fcb5a699c4ec4789687b3.jpg)
　　下图中横轴表示你的空间参数$w$和$b$，在实践中，$w$可以是更高的维度，但是为了更好地绘图，我们定义$w$和$b$，都是单一实数，代价函数（成本函数）$J(w,b)$是在水平轴$w$和$b$上的曲面，因此曲面的高度就是$J(w,b)$在某一点的函数值。我们所做的就是找到使得代价函数（成本函数）$J(w,b)$函数值是最小值，对应的参数$w$和$b$。
<!-- more -->
![梯度下降法过程说明](c5eda5608fd2f4d846559ed8e89ed33c.jpg)
# 梯度下降法
## 一、在三维坐标系中
###　1. 初始化，随机取$w$,$b$，获得$J(w,b)$初始值
###　2. 从初始点不断向最陡的下坡方向迭代
###　3. 直到走到全局最优解附近

## 二、单参数细节说明
　　假定b恒定，则只有w在变化，J变为二维平面函数。
![二维梯度下降](4fb3b91114ecb2cd81ec9f3662434d81.jpg)
　　迭代此公式：$w=w-\alpha\frac{dJ(w)}{dw}$

　　其中等号为赋值符；a为学习率，控制步长。

* 初始化点在最优解右边时，斜率$\frac{dJ(w)}{dw}\>0$,故下一步向左走。

* 初始化点在最优解左边时，斜率$\frac{dJ(w)}{dw}<0$,故下一步向右走。

## 三、双参数细节说明
　　代价函数有两个参数，因此需要对两个参数分别求偏导。
　　　　$w=w-\alpha\frac{\partial J(w,b)}{\partial w}$　　$b=b-\alpha\frac{\partial J(w,b)}{\partial b}$
　　
　　$\partial $ 表示求偏导符号，可以读作**round**，$\frac{\partial J(w,b)}{\partial w}$  就是函数$J(w,b)$ 对$w$ 求偏导，在代码中我们会使用$dw$ 表示这个结果，$\frac{\partial J(w,b)}{\partial b}$  就是函数$J(w,b)$对$b$ 求偏导，在代码中我们会使用$db$ 表示这个结果，小写字母$d$ 用在求导数（**derivative**），即函数只有一个参数，偏导数符号$\partial $ 用在求偏导（**partial derivative**），即函数含有两个以上的参数。　

# 逻辑回归中的梯度下降(单个样本)
##　1. 求出损失函数L的值

　　假设样本只有两个特征$x_{1}$和$x_{2}$，只考虑单个样本情况，可以通过以下三个式子求出损失函数L的值。
![推理公式](03f5f96177ab15d5ead8298ba50300ac.jpg)
##　2.反向求导

　　现在让我们来讨论通过反向计算出导数。因为我们想要计算出的代价函数$L(a,y)$的导数，首先我们需要反向计算出代价函数$L(a,y)$关于$a$的导数，在编写代码时，你只需要用$da$ 来表示$\frac{dL(a,y)}{da}$。

　　通过微积分得到：$\frac{dL(a,y)}{da}=-y/a+(1-y)/(1-a)$ 

　　而根据$da$可以求出${dz} = \frac{dL(a,y)}{dz} = \frac{dL}{dz} = ( \frac{dL}{da} ) \cdot (\frac{da}{dz} ) = ( - \frac{y}{a} + \frac{(1 - y)}{(1 - a)})\cdot a(1 - a) = a - y$

　　然后通过反向推导求出是计算𝑤和𝑏变化对代价函数𝐿的影响：
　　$d{w_{1}}=\frac{\partial L}{\partial w_{1}}=x_{1}\cdot dz$， 

　　$d{w_{2}}=\frac{\partial L}{\partial w_{2}}=x_{2}\cdot dz$，

　　$db=dz$
![反向推导](6403f00e5844c3100f4aa9ff043e2319.jpg)

##　3. 更新参数值

　　$w_{1}=w_{1}-\alpha d{w}_{1}$，

　　$w_{2}=w_{2}-\alpha d{w}_{2}$，

　　$b=b-\alpha db$。

# 逻辑回归中的梯度下降(m个样本)
　　我们所需要做的就是将单个样本的梯度下降过程进行m次并求出平均值。
![一轮梯度下降计算](8b725e51dcffc53a5def49438b70d925.png)
　　我们初始化$J=0,d{w_{1}}=0,d{w_{2}}=0,db=0$

　　代码流程：

```py
J=0,dw1=0,dw2=0,db=0
for i = 1 to m:
    z(i) = wx(i)+b
    a(i) = sigmoid(z(i))
    J += -[y(i)log(a(i))+(1-y(i)）log(1-a(i))
    dz(i) = a(i)-y(i)
    dw1 += x1(i)dz(i)
    dw2 += x2(i)dz(i)
    db += dz(i)
J/= m
dw1/= m
dw2/= m
db/= m
w=w-alpha*dw
b=b-alpha*db
```

　　然而，随着n的增大，dw的求取也需要一重循环，导致程序效率低下，故考虑向量化来摆脱循环。