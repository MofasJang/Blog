---
title: 论文笔记-Measuring the Effects of Non-Identical Data Distribution for Federated Visual Classification
copyright: true
date: 2021-10-30 10:17:38
tags: 联邦学习
categories:
- 深度学习
- 联邦学习
---
## Measuring the Effects of Non-Identical Data Distribution for Federated Visual Classification
{% note info %}
论文链接：[Measuring the Effects of Non-Identical Data Distribution for Federated Visual Classification](https://arxiv.org/pdf/1909.06335.pdf)
{% endnote %}

### 一、本文关注的问题

非同分布的数据对联邦学习模型的准确性可能有较大影响，需要对其进行优化。
<!-- more -->

### 二、提出的方法

1. 合成非同分布客户端数据：由一个向量**q**将所有数据参数化为N个类，(其中$q_i\ge0$,$i\in[1,N]$ 且 $||\mathtt q||_1=1$)。从狄利克雷分布中得到**q**~Dir($\alpha$**p**)，其中**p**表示N个类上的先验类分布，$\alpha>0$是控制客户端数据同一性的参数。$\alpha \to \infty$时所有客户端的类分布相同；$\alpha \to 0$时，每个客户端只有一个随机类的样本。随机生成30个客户端，N为10，给定不同的$\alpha$值对**q**进行采样，得到下图分布情况。

   ![生成数据](http://pic.panjiangtao.cn/202110301020_281.jpg)

2. FedAvgM算法：联邦平均算法中，通过$w\leftarrow w-\Delta w$更新梯度，其中$\Delta w=\sum_{k-1}^K\frac{n_k}n\Delta w_k$，$\Delta w_k$是第k个客户端的梯度更新。将动量加入算法以抑制震荡，改为计算$v\leftarrow \beta v+\Delta w$，其中$\beta \in \{0,0.7,0.9,0.97,0.99,0.997\}$，模型更新变为$w\leftarrow w-v$。称该算法为基于服务器动量的联邦平均算法。

### 三、取得的结果

1. $\alpha$较小时其变化对模型准确度影响较大，报告比例C和同步频率对性能影响不大。$\alpha$较小时，训练错误的波动性更大，且很难收敛。
2. C和$\alpha$较小时训练对超参数更敏感。
3. $\alpha$较小时，使用基于动量的联邦平均算法使得模型准确性具有显著提高。
4. 定义有效学习率$\eta_{eff}=\eta/(1-\beta)$，报告比例C较大时$\eta_{eff}$的可选范围更大，降低学习率$\eta$提高动量$\beta$以降低$\eta_{eff}$可以防止客户端更新偏离。

