---
title: DNN中的梯度消失/爆炸原因
date: 2018-08-03 12:20:32
updated: 2018-08-03 12:20:32
tags: [数学推导]
categories: 深度学习
mathjax: true
---

## DNN中的梯度消失/爆炸原因

梯度消失/梯度爆炸是深度学习中老生常谈的话题，这篇博客主要是对DNN中的梯度消失/梯度爆炸原因进行公式层面上的直观理解。

![1563957116984](/images/DNN等梯度消失之_1.png)

如上图所示，假设有2个隐层，前向传播公式：

$f_1 = \sigma(w_1x+b_1)，z_1 = w_1x+b_1$

$f_2 = \sigma(w_2f_1+b_2)，z_2 = w_2f_1+b_2$

$f_3 = \sigma(w_3f_2+b_3)，z_3 = w_3f_2+b_3$

$f_3$是输出层的神经元，所以可以认为$loss$是关于$f_3$的函数。

当$loss$反向传播的时候，我们可以对权重$w_3, w_2, w_1$进行更新:

$\frac{\partial loss}{\partial w_3} = \frac{\partial loss}{\partial f_3}  \frac{\partial f_3}{\partial w_3} = \frac{\partial loss}{\partial f_3} \sigma^{'}(w_3f_2+b_3)f_2$

$\frac{\partial loss}{\partial w_2} = \frac{\partial loss}{\partial f_3}  \frac{\partial f_3}{\partial f_2} \frac{\partial f_2}{\partial w_2} = \frac{\partial loss}{\partial f_3} \sigma^{'}(w_3f_2+b_3)w_3 \sigma^{'}(w_2f_1+b_2)f_1$

$\frac{\partial loss}{\partial w_1} = \frac{\partial loss}{\partial f_3}  \frac{\partial f_3}{\partial f_2} \frac{\partial f_2}{\partial f_1} \frac{\partial f_1}{\partial w_1} = \frac{\partial loss}{\partial f_3} \sigma^{'}(w_3f_2+b_3)w_3 \sigma^{'}(w_2f_1+b_2)w_2 \sigma^{'}(w_1x+b_1)x$

根据上面规律，我们可以把$x$写成$f_0$，当有n-1层隐层时，$f_n$是输出，如果要求$w_l$也就是第$l$层的权重，反向传播中涉及的偏导计算为：

$\frac{\partial loss}{\partial w_l } = \frac{\partial loss }{\partial {f_n} }  \prod_{i=l}^{n}\sigma^{'}(w_if_{i-1} + b_i)\prod_{i=l+1}^{n}w_i  f_{l-1}$

上面这个式子就是我们要推导的核心！

当梯度反向传播到第$l$层的时候，我们用上述公式计算偏导，根据链式法则，上面用大括号括起来的就是累乘项，其中前半部分是关于激活函数的导数的累乘，后半部分是关于权重值的累乘。我们知道，激活函数比如sigmoid函数，其导数的取值范围是$(0, \frac{1}{4}]$，是恒小于1的，当网络层数很深的时候，多个小于1的数进行累乘，结果是趋向于0的，也就是说此时，梯度反向传播的时候，根据参数更新公式$\theta := \theta - \alpha \cdot \frac{\partial loss}{\theta}$，偏导部分的取值趋于0，那么该参数得不到更新，也就出现了我们说的**梯度消失**现象。

另外，我们也注意到，大括号的后半部分是关于权重值的累乘，当我们初始化权值很大的时候，多个大于1的数累乘，结果是$+\infty$，此时就出现了**梯度爆炸**现象。

