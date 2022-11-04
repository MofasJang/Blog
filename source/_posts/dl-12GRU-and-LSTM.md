---
title: GRU单元和LSTM
copyright: false
date: 2021-03-12 16:58:20
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

# GRU(门控循环单元)
## GRU推导过程
　　GRU单元中包含记忆细胞$c^{<t>}=a^{<t>}$用来记住序列的前后联系。
　　
　　在每个时间步，我们将用一个候选值重写记忆细胞，即${\tilde{c}}^{<t>}$的值，所以它就是个候选值，替代了$c^{<t>}$的值。然后我们用**tanh**激活函数来计算，${\tilde{c}}^{<t>} =tanh(W_{c}\left\lbrack c^{<t-1>},x^{<t>} \right\rbrack +b_{c})$，所以${\tilde{c}}^{<t>}$的值就是个替代值，代替表示$c^{<t>}$的值。

<!-- more -->

　　$\Gamma_{u}= \sigma(W_{u}\left\lbrack c^{<t-1>},x^{<t>} \right\rbrack +b_{u})$

　　这是个下标为$u$的大写希腊字母$\Gamma$，$u$代表更新门，这是一个0到1之间的值。我们刚才写出来的用$\tilde{c}$更新$c$的等式。然后门决定是否要真的更新它，按照下述式子进行计算。

　　$c^{<t>} = \Gamma_{u}*{\tilde{c}}^{<t>} +\left( 1- \Gamma_{u} \right)*c^{<t-1>}$

## 完整GRU
![GRU完整过程](523650730db3f0d5c05a7192da02f878.png)
　　添加一个门$\Gamma_{r}$，你可以认为$r$代表相关性（**relevance**）。这个$\Gamma_{r}$门告诉你计算出的下一个$c^{<t>}$的候选值${\tilde{c}}^{<t>}$跟$c^{<t-1>}$有多大的相关性。计算这个门$\Gamma_{r}$需要参数，正如你看到的这个，一个新的参数矩阵$W_{r}$，$\Gamma_{r}= \sigma(W_{r}\left\lbrack c^{<t-1>},x^{<t>} \right\rbrack + b_{r})$。

# LSTM(长短时记忆网络)
## LSTM正向传播
　　LATM中不再使用$\Gamma_{r}$，也不直接将$c^{<t>}$作为$a^{<t>}$输出，其主要式子如下：

${\tilde{c}}^{<t>} = tanh(W_{c}\left\lbrack a^{<t-1>},x^{<t>} \right\rbrack +b_{c}$

更新门：$\Gamma_{u}= \sigma(W_{u}\left\lbrack a^{<t-1>},x^{<t>} \right\rbrack +b_{u})$

遗忘门：$\Gamma_{f} =\sigma(W_{f}\left\lbrack a^{<t-1>},x^{<t>} \right\rbrack +b_{f})$

输出门：$\Gamma_{o} =\sigma(W_{o}\left\lbrack a^{<t-1>},x^{<t>} \right\rbrack +>b_{o})$

$c^{<t>} =\Gamma_{u}*{\tilde{c}}^{<t>} + \Gamma_{f}*c^{<t-1>}$

$a^{<t>} = \Gamma_{o}*c^{<t>}$

## **LSTM**反向传播计算：

###　**门求偏导：**

$d \Gamma_o^{\langle t \rangle} = da_{next}*\tanh(c_{next}) * \Gamma_o^{\langle t \rangle}*(1-\Gamma_o^{\langle t \rangle})\tag{1}$

$d\tilde c^{\langle t \rangle} = dc_{next}*\Gamma_i^{\langle t \rangle}+ \Gamma_o^{\langle t \rangle} (1-\tanh(c_{next})^2) * i_t * da_{next} * \tilde c^{\langle t \rangle} * (1-\tanh(\tilde c)^2) \tag{2}$

$d\Gamma_u^{\langle t \rangle} = dc_{next}*\tilde c^{\langle t \rangle} + \Gamma_o^{\langle t \rangle} (1-\tanh(c_{next})^2) * \tilde c^{\langle t \rangle} * da_{next}*\Gamma_u^{\langle t \rangle}*(1-\Gamma_u^{\langle t \rangle})\tag{3}$

$d\Gamma_f^{\langle t \rangle} = dc_{next}*\tilde c_{prev} + \Gamma_o^{\langle t \rangle} (1-\tanh(c_{next})^2) * c_{prev} * da_{next}*\Gamma_f^{\langle t \rangle}*(1-\Gamma_f^{\langle t \rangle})\tag{4}$

### **参数求偏导 ：**

$ dW_f = d\Gamma_f^{\langle t \rangle} * \begin{pmatrix} a_{prev} \\ x_t\end{pmatrix}^T \tag{5} $

$ dW_u = d\Gamma_u^{\langle t \rangle} * \begin{pmatrix} a_{prev} \\ x_t\end{pmatrix}^T \tag{6} $

 $ dW_c = d\tilde c^{\langle t \rangle} * \begin{pmatrix} a_{prev} \\ x_t\end{pmatrix}^T \tag{7} $

$ dW_o = d\Gamma_o^{\langle t \rangle} * \begin{pmatrix} a_{prev} \\ x_t\end{pmatrix}^T \tag{8}$

为了计算$db_f, db_u, db_c, db_o$ 需要各自对$d\Gamma_f^{\langle t \rangle}, d\Gamma_u^{\langle t \rangle}, d\tilde c^{\langle t \rangle}, d\Gamma_o^{\langle t \rangle}$ 求和。

### 最后，计算隐藏状态、记忆状态和输入的偏导数：

$ da_{prev} = W_f^T*d\Gamma_f^{\langle t \rangle} + W_u^T * d\Gamma_u^{\langle t \rangle}+ W_c^T * d\tilde c^{\langle t \rangle} + W_o^T * d\Gamma_o^{\langle t \rangle} \tag{9}$

$ dc_{prev} = dc_{next}\Gamma_f^{\langle t \rangle} + \Gamma_o^{\langle t \rangle} * (1- \tanh(c_{next})^2)*\Gamma_f^{\langle t \rangle}*da_{next} \tag{10}$

$ dx^{\langle t \rangle} = W_f^T*d\Gamma_f^{\langle t \rangle} + W_u^T * d\Gamma_u^{\langle t \rangle}+ W_c^T * d\tilde c_t + W_o^T * d\Gamma_o^{\langle t \rangle} \tag{11} $