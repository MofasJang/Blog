---
title: 神经网络介绍
date: 2021-02-19 13:47:24
tag:
- 深度学习
- 毕设准备
categories:
- 毕设准备
- 深度学习
copyright: false
---
{% note info %}
课程名称：[吴恩达深度学习课程](https://www.bilibili.com/video/BV164411m79z)
学习资料：[深度学习教程中文笔记](http://file.panjiangtao.cn/Deeplearning%E6%B7%B1%E5%BA%A6%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0v5.71.pdf)
{% endnote %}
# 基本神经网络
房价预测问题：
![房价预测函数](3fe6da26014467243e3d499569be3675.png)
　　从趋近于零开始，然后变成一条直线。这个函数被称作 **ReLU** 激活函数，它的全称是Rectified Linear Unit。rectify（修正）可以理解成𝑚𝑎𝑥(0, 𝑥)，这也是你得到一个这种形状的函数的原因。
<!-- more -->

![神经网络预测房价](7a0e0d40f4ba80a0466f0bd7aa9f8537.png)
　　图上每一个画的小圆圈都可以是**ReLU**的一部分，也就是指修正线性单元，或者其它稍微非线性的函数。基于房屋面积和卧室数量，可以估算家庭人口，基于邮编，可以估测步行化程度或者学校的质量。最后你可能会这样想，这些决定人们乐意花费多少钱。

　　神经网络的一部分神奇之处在于，当你实现它之后，你要做的只是输入x，就能得到输出y。因为它可以自己计算你训练集中样本的数目以及所有的中间过程。

# 神经网络的监督学习(Supervised Learning with Neural Networks)
　　在监督学习中你有一些输入𝑥，你想学习到一个函数来映射到一些输出𝑦，比如我们之前提到的房价预测的例子，你只要输入有关房屋的一些特征，试着去输出或者估计价格𝑦。我们举一些其它的例子，来说明神经网络已经被高效应用到其它地方。
![监督学习应用](QQ截图20210219142516.jpg)
* 对于图像应用，我们经常在神经网络上使用卷积（Convolutional Neural Network），通常缩写为 CNN。

![卷积神经网络模型](1bebe0ac41715ef8132f2d802968495c.png)
* 对于序列数据，例如音频，有一个时间组件，随着时间的推移，音频被播放出来，所以音频是最自然的表现。作为一维时间序列（两种英文说法 one-dimensional time series / temporal sequence）。对于序列数据，经常使用 RNN，一种递归神经网络（Recurrent Neural Network），语言，英语和汉语字母表或单词都是逐个出现的，所以语言也是最自然的序列数据，因此更复杂的 RNNs 版本经常用于这些应用。

![递归神经网络模型](4656617e30e7ad44490fe605b2e49e56.png)

# 深度学习的发展
　　多亏数字化社会的来临，现在的数据量都非常巨大，我们花了很多时间活动在这些数字的领域，比如在电脑网站上、在手机软件上以及其它数字化的服务，它们都能创建数据，同时便宜的相机被配置到移动电话，还有加速仪及各类各样的传感器，同时在物联网领域我们也收集到了越来越多的数据。仅仅在过去的20年里对于很多应用，我们便收集到了大量的数据，远超过机器学习算法能够高效发挥它们优势的规模。

　　事实上如今最可靠的方法来在神经网络上获得更好的性能，往往就是**要么训练一个更大的神经网络，要么投入更多的数据**，这只能在一定程度上起作用，因为最终你耗尽了数据，或者最终你的网络是如此大规模导致将要用太久的时间去训练，但是仅仅提升规模的的确确地让我们在深度学习的世界中摸索了很多时间。
![深度学习规模大小带来的性能影响](2b14edfcb21235115fca05879f8d9de2.png)

　　作为一个具体的例子，神经网络方面的一个巨大突破是从**sigmoid**函数转换到一个**ReLU**函数。
![sigmoid函数和ReLU函数](1a3d288dc243ca9c5a70a69799180c4a.png)
　　可以知道的一个使用**sigmoid**函数和机器学习问题是，在这个区域，也就是这个**sigmoid**函数的梯度会接近零，所以学习的速度会变得非常缓慢，因为当你实现梯度下降以及梯度接近零的时候，参数会更新的很慢，所以学习的速率也会变的很慢，而通过改变这个被叫做激活函数的东西，神经网络换用这一个函数，叫做**ReLU**的函数（修正线性单元），**ReLU**它的梯度对于所有输入的负值都是零，因此梯度更加不会趋向逐渐减少到零。而这里的梯度，这条线的斜率在这左边是零，仅仅通过将**Sigmod**函数转换成**ReLU**函数，便能够使得一个叫做梯度下降（**gradient descent**）的算法运行的更快，这就是一个或许相对比较简单的算法创新的例子。