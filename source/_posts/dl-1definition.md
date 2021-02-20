---
title: 机器学习引言
date: 2021-02-19 12:05:14
tags:
- 深度学习
- 毕设准备
categories:
- 毕设准备
- 深度学习
copyright: false
---
{% note info %}
课程名称：[吴恩达机器学习课程](https://www.bilibili.com/video/BV164411b7dx)
{% endnote %}
# 机器学习(machine learning)定义
* Arther Samuel(1959):Machine Learning:Field fo study that gives computers the ability to learn without being explicitly programmed.
* Tom Mitchell(1998) well-posed Learning Problem: A computer program is said to learn from experience E with respect to some task T and some performance measure P, if its improves with experience E.

　　在跳棋游戏中，E->程序和自己下上万次棋，T->玩跳棋，P->与新对手下棋赢的概率。
<!-- more -->
# 机器学习算法分类
## 　一、监督学习(Supervised learning)
### (1)回归问题(Regression problem):预测出一个连续值的输出。
例如：预测房价问题，根据样本的数据集进行拟合就可以得到一条连续的曲线。
![房价预测](https://pic1.zhimg.com/80/v2-7497b88c2931c082a5bf6334f54b147c_720w.jpg)
### (2)分类问题(Classification problem):预测出一个离散值的输出。
例子：根据肿瘤的某些特征来判断是良性还是恶性，得到的结果是“良性”或者是“恶性”，是离散的。
![肿瘤预测-单变量](https://pic4.zhimg.com/80/v2-7e1eb4d75ea5d8fb6b3cba72addeffef_720w.jpg)
![肿瘤预测-多变量](https://pic3.zhimg.com/80/v2-c11e632cb62e2351541647412b0b546e_720w.jpg)
## 　二、无监督学习(Unsupervised learning)
　　无监督学习的数据集和监督学习的不同，没任何标签，也就是**没有“正确的输出结果”**。在此过程中没有指导者，只有计算机自己学习。
### 聚类算法(Clustering algorithms)
　　从数据集中可以通过非监督学习得到数据的某种结构，可能是把数据分成两个不同的聚集簇，称为聚类算法。
应用：
* Google新闻将相似新闻分类
* 基因学将有相同基因序列的个体分为一类

![DNA微阵列数据](https://pic2.zhimg.com/80/v2-0e62546b76127accdbcd984d89ff7ed1_720w.jpg)

### 盲信号分离算法
* 鸡尾酒会问题(Cocktail party problem):两人在说话，两个麦克风离两人距离不同，从每段麦克风录制的音频中分离出两个人各自的声音。

![鸡尾酒会问题](https://pic4.zhimg.com/80/v2-61819fa6432378a1b42e03f6be07b8b7_720w.jpg)
实现该音频处理只需如下代码：
![音频分离代码](https://pic1.zhimg.com/80/v2-ff9409c9b9439df1af1e03a11f429d4c_720w.jpg)
其中：SVD()函数——奇异值分解的缩写，作为线性代数常规函数的缩写,内置于Octave软件中。
