---
title: 不同类型循环神经网络
copyright: false
date: 2021-03-12 14:28:23
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

# 分类
## 多对多(Many-to-many)
　　上一节中提到的命名识别模型就属于多对多模型（下图左），其$T_{x}=T_{y}$
　　多对多也存在$T_{x}!=T_{y}$的情况。
<!-- more -->
![多对多(左)，多对一(右)](14e1df0a7a8cdd1584b2e92e87e23aa7.png)

## 多对一(Many-to-one)
　　情感分类中（上图右），输入一个序列，输出单个评分，其$T_{x}>1$,$T_{y}=1$

## 一对多(One-to-many)
　　音乐生成模型，输入为一个音符，输出一段乐谱序列。每个输出单元都会喂给下一单元作为输入。
![音乐生成模型](db580f1dfd6095d672fc62cce74ce5e2.png)