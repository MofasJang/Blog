---
title: 论文笔记-Communication-Efficient Learning of Deep Networks from Decentralized Data
copyright: true
date: 2021-10-30 10:14:02
tags: 联邦学习
categories:
- 深度学习
- 联邦学习
---
## Communication-Efficient Learning of Deep Networks from Decentralized Data
{% note info %}
论文链接：[Communication-Efficient Learning of Deep Networks from Decentralized Data](https://arxiv.org/abs/1602.05629)
{% endnote %}

### 一、本文关注的问题

1. 移动设备上的数据由于隐私问题、数据量大的原因无法作为模型训练集
2. 参与优化的数据具有非独立同分布、数量不平衡、大规模、通信受限的问题
<!-- more -->

### 二、提出的方法

联邦学习：将训练数据分布在移动设备上，由中央服务器协调聚合每台设备本地数据计算的更新来学习共享模型，无需直接访问原始数据。

1. 通过下述方法减小通信轮数

   * 增加并行性，使用更多的客户端在每个通信轮之间独立工作

   * 增加了每个客户端的计算量

2. 使用联邦平均算法进行优化：将每个客户端上的局部随机梯度下降 (SGD) 与执行模型平均的服务器相结合，得到FedAvg方法，以FedSGD作为基线进行对比

   * 联邦优化：对于机器学习任务，我们取$ f_i(w)=l(x_i,y_i;w)$,代表模型参数w对实例$(x_i,y_i)$的预测损失，假设有K个包含不同数据的客户端，有数据点的索引集$P_k$,设$N_k=|P_k|$,则联邦平均算法适用于以下形式的任何有限和目标：

     $$f(w)=\sum_{K=1}^K{\frac{n_k}nF_k(w)}$$            where           $F_k(w)=\frac1{n_k}\sum_{i\in{P_k}}f_i(w)$

   * FedAvg算法：

     * 流程：对于每个客户端k，全选数据且固定学习率为$\eta$，计算本地数据在当前模型$w_t$上的平均梯度$g_k=\nabla{F_k(w_t)}$，中央服务器接收这些梯度且应用更新$w_{t+1}\leftarrow w_t-\eta\sum_{k-1}^K\frac{n_k}ng_k$，其中$\sum_{k-1}^K\frac{n_k}ng_k=\nabla{f(w_t)}$。这等效于以下更新：$\forall k,w_{t-1}^k\leftarrow w_t-\eta g_k$和$w_{t+1}\leftarrow \sum_{k-1}^K\frac{n_k}n w_{t+1}^k$。每个客户端可以使用本地数据进行多次迭代更新$w_k\leftarrow w_k−η\nabla F_k(w_k)$后交由服务器进行加权平均。计算量由三个关键参数控制:C，每轮执行计算的客户端比例;E，每轮每个客户端通过其本地数据集的训练次数;B，用于客户端更新的本地迷你批处理大小。

     * 伪代码：其中K个服务器的索引为k，B为本地批大小，E为本地训练次数，$\eta$为学习率

       -----

       **Server executes**:
       	initialize $w_0$
       	**for** each round t= 1,2, . . .**do**
       		$m←max(C·K,1)$
       		$S_t←$ (random set of $m$ clients)
       		**for** each client $k∈S_t$ **in parallel do**
       			$w^k_{t+1}$←ClientUpdate$(k, w_t)$
       		$w_{t+1}\leftarrow \sum_{k-1}^K\frac{n_k}n w_{t+1}^k$

       **ClientUpdate**$(k, w)$://Run on clientk
       	B ←(split $P_k$ into batches of size B)
       	**for** each local epoch $i$ from 1 to E**do**
       		**for** batch b∈ B do
       			$w←w−η\nabla l(w;b)$
       	return $w$ to server

       ------

### 三、取得的结果

1. 图像识别中，增加并行性对减小通讯次数提升不明显，只能加速10倍以内
2. 语言建模中，在莎士比亚合集中使用FedAvg算法增加客户端计算量对于非IID和不平衡数据具有较大的通信轮次下降，加速95倍
3. 在CIFAR数据集中使用FedAvg算法相较于基线SGD算法只需1/99的通信轮数就可达到相同的准确率
4. 在大规模下一词预测任务中，FedAvg算法相较于基线FedSGD算法只需1/23的通信轮数就可达到相同的准确率

