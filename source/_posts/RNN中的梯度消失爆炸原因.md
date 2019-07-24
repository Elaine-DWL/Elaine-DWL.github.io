---
title: RNN中的梯度消失/爆炸原因
date: 2018-08-04 19:38:42
updated: 2018-08-04 19:38:42
tags: [数学推导]
categories: 深度学习
mathjax: true
---

## RNN中的梯度消失/爆炸原因

梯度消失/梯度爆炸是深度学习中老生常谈的话题，这篇博客主要是对RNN中的梯度消失/梯度爆炸原因进行公式层面上的直观理解。

![1563957116984](/images/RNN等梯度消失之_1.png)

首先，上图是RNN的网络结构图，$(x_1, x_2, x_3, …, )$是输入的序列，$X_t$表示时间步为$t$时的输入向量。假设我们总共有$k$个时间步，用第$k$个时间步的输出$H_k$作为输出（实际上每个时间步都有输出，这里仅考虑$H_k$），用$E_k$表示损失。

其中，$C_{t}=\tanh \left(W_{c} C_{t-1}+W_{x} X_{t}\right)$

从上式可以看出 $W_x$和$W_c$其实是差不多的，记$W=[W_c, W_x]$，那么求偏导可以得到：

$\begin{aligned} \frac{\partial E_{k}}{\partial W}=& \frac{\partial E_{k}}{\partial H_{k}} \frac{\partial H_{k}}{\partial C_{k}} \frac{\partial C_{k}}{\partial C_{k-1}} \ldots \frac{\partial C_{2}}{\partial C_{1}} \frac{\partial C_{1}}{\partial W}=\\ & \frac{\partial E_{k}}{\partial H_{k}} \frac{\partial H_{k}}{\partial C_{k}}\left(\prod_{t=2}^{k} \frac{\partial C_{t}}{\partial C_{t-1}}\right) \frac{\partial C_{1}}{\partial W} \end{aligned}$

其中的累乘部分为：

$\begin{aligned} \frac{\partial C_{t}}{\partial c_{t-1}}=& \tanh ^{\prime}\left(W_{c} C_{t-1}+W_{x} X_{t}\right) \cdot \frac{d}{d C_{t-1}}\left[W_{c} C_{t-1}+W_{x} X_{t}\right]=\\ & \tanh ^{\prime}\left(W_{c} C_{t-1}+W_{x} X_{t}\right) \cdot W_{c} \end{aligned}$

将该式代入上式有：

$\frac{\partial E_{k}}{\partial W}=\frac{\partial E_{k}}{\partial H_{k}} \frac{\partial H_{k}}{\partial C_{k}}\left(\prod_{t=2}^{k} \tanh ^{\prime}\left(W_{c} C_{t-1}+W_{x} X_{t}\right) \cdot W_{c}\right) \frac{\partial c_{1}}{\partial W}$

观察这个式子，和上篇文章中一样，因为链式法则，出现了累乘项，因为tanh的导数 <= 1，所以，当k很大的时候，上式的值是趋向于0的。(<1的数多次相乘)，也就是：

$\Pi_{t=2}^{k} \tanh ^{\prime}\left(W_{c} C_{t-1}+w_{x} X_{t}\right) \cdot W_{c} \rightarrow 0,$ so $\frac{\partial E_{k}}{\partial W} \rightarrow 0$

此时，权重更新公式：

$W \leftarrow W-\alpha \frac{\partial E_{k}}{\partial W} \approx W$

也就是说，RNN很容易出现梯度消失现象，使得参数更新缓慢，甚至是停止更新。