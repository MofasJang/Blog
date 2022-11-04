---
title: 序列模型之语言辨识
copyright: false
date: 2021-03-15 12:59:59
tags:
tag:
- 深度学习
- 毕设准备
categories:
- 毕设准备
- 深度学习
---

{% note info %}
课程名称：[吴恩达深度学习课程](https://www.bilibili.com/video/BV1F4411y7BA)
学习资料：[深度学习教程中文笔记](http://file.panjiangtao.cn/Deeplearning%E6%B7%B1%E5%BA%A6%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0v5.71.pdf)
{% endnote %}

　　传统语音识别的第一步是用一段音频生成一个声谱图，如下图所示，横轴是时间，纵轴是声音的频率（**frequencies**），而图中不同的颜色，显示了声波能量的大小（**the amount of energy**）。有一段时间，语音识别系统是用音位（**phonemes**）来构建的，也就是人工设计的基本单元（**hand-engineered basic units of cells**），如果用音位来表示"**the quick brown fox**"，我这里稍微简化一些，"**the**"含有"**th**"和"**e**"的音，而"**quick**"有"**k**" "**w**" "**i**" "**k**"的音。
<!-- more -->
![语音识别问题](8da3e9cf049139a8e4a78503bd72e7fd.png)
　　而在**end-to-end**模型中，我们发现这种音位表示法（**phonemes representations**）已经不再必要了，而是可以构建一个系统，通过向系统中输入音频片段（**audio clip**），然后直接输出音频的文本（**a transcript**），而不需要使用这种人工设计的表示方法。

# CTC损失函数
　　通常,我们将语音音频划分为多个时间步,比如你有一段10秒的音频，并且特征（**features**）是100赫兹的，即每秒有100个样本，于是这段10秒的音频片段就会有1000个输入。而我们需要的输出往往没有这么多字母，因此将表示相同字母的音频输出为重复字母，没有意义的音频输出为空白符"_"，还可能有表示空格的音频输出" "。最后将重复的部分合并，就得到了我们希望的输出。
![CTC损失函数过程](8f409fc3980b0be00dca49bf4fac2659.png)

# 触发字检测
　　随着语音识别的发展，越来越多的设备可以通过你的声音来唤醒，这有时被叫做触发字检测系统（**rigger word detection systems**）
　　现在有一个这样的**RNN**结构，**我们要做的就是把一个音频片段（an audio clip）计算出它的声谱图特征（spectrogram features）得到特征向量**$x^{<1>}$, $x^{<2>}$, $x^{<3>}$..，然后把它放到**RNN**中，最后要做的，就是定义我们的目标标签$y$。假如音频片段中的这一点是某人刚刚说完一个触发字，比如"**Alexa**"，或者"小度你好" 或者"**Okay Google**"，那么在这一点之前，你就可以在训练集中把目标标签都设为0，然后在这个点之后把目标标签设为1。