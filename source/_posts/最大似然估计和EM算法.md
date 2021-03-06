---
title: 最大似然估计和EM算法
date: 2018-06-01 16:17:21
tags: [算法，机器学习]
categories: 机器学习
mathjax: true
---

本文是对最大似然估计和EM算法做的一个总结。

一般来说，事件A发生的概率与某个未知参数$\theta​$有关，$\theta​$取值不同，则事件A发生的概率$p(A|\theta)​$也不同。当我们在一次实验中事件A发生了，则认为此时的$\theta​$值应是t的一切可能取值中使$p(A|\theta)​$最大的那个。最大似然估计就是要选取这样的t值作为参数t的估计值，使所选取的样本在被选的总体中出现的可能性为最大。

EM算法是在有潜变量的情况下，通过不断进行最大似然估计来求解参数的过程。

# 最大似然估计

最大似然估计/极大似然估计(Maximum Likelihood Estimation，简称MLE)

## 前言

**利用已知的样本的结果，在使用某个模型的基础上，反推出最有可能导致这种结果的模型参数值。是一种参数估计方法。**

例子:

定义有些绕口，下面我们通过例子来理解一下。

我们知道，现实中的硬币是均匀的，即抛出后正面朝上和反面朝上的概率是一样的。
但是现在假设有两枚不均匀的硬币，这两枚硬币正面朝上的概率都不是0.5，分别记为$p_1$和$p_2$
记每选用一枚硬币抛5次为一个实验，得到实验结果如下：

| 实验所选硬币 | 实验结果 |
| :----------: | :------: |
|      1       | 3正、2反 |
|      1       | 1正、4反 |
|      1       | 2正、3反 |
|      2       | 2正、3反 |
|      2       | 1正、4反 |

好！那么我现在问，根据实验结果你可以得到$p_1$和$p2$的值吗？你应该会这样算：

$p1=(3+1+2)/15=0.4$

$p2=(2+1)/10=0.3$

然后你就说了，$p1$**最有可能**是0.4，$p2$**最有可能**是0.3。

现在我们就完成了一次最大似然估计！

---

- 什么是似然估计？---根据实验结果，反推出实验参数。
- 什么是最大似然估计？---根据实验结果，反推出<u>最有可能导致这个结果的</u>实验参数。
- 什么是概率？---根据参数，推出可能的实验结果。

用数学的语言来描述就是：

概率：$p(x|\theta)$       在**参数$\theta$确定**的情况下，$x$出现的概率

似然：$L(\theta|x_1,x_2,...)$      根据结果，反推参数

## 定义

最大似然估计提供了一种给定观察数据来评估模型参数的方法，即：“模型已定，参数未知”。

假设我们要统计全国人口的身高，首先假设这个身高服从正态分布，但是该分布的均值与方差未知。一种解决办法是：通过采样，获取部分人的身高，然后通过最大似然估计来获取上述假设中的正态分布与均值。

最大似然估计中采样需要满足：**所有的采样都是独立同分布的**。下面具体描述最大似然估计：

首先，假设$x_1,x_2,...,x_n$是独立同分布的采样，$\theta$是模型的参数，$f$为我们使用的模型。所以，参数为$\theta$的模型产生上述采样可以表示为：

$f(x_1,x_2,...,x_n)=f(x_1|\theta)\times f(x_2|\theta)\times ... \times f(x_n|\theta)$

回到上面“模型已定，参数未知”的说法，此时，我们已知的为$x_1,x_2,...,x_n$，未知参数$\theta$，所以似然定义为：

$L(\theta|x_1,x_2,...,x_n)=f(x_1,x_2,...,x_n)=\prod\limits_{i=1}^nf(x_i|\theta)$

最大似然估计就是求上式的极值点。所以自然想到求导了，因为右边是连乘，为了计算简便，同时对等号两边取对数，有：

$\ln L(\theta|x_1,...,x_n)=\sum\limits_{i=1}^n\ln f(x_i|\theta)$      $\hat l = \frac 1n\ln L$

其中   $\ln L(\theta|x_1,...,x_n)$称为对数似然，$\hat l$为平均对数似然。通常所说的最大似然指的是最大的平均对数似然：

$\hat{\theta}_{mle}=\arg\limits_{\theta\in\Theta}\max\hat l(\theta|x_1,...,x_n)$

### 例子1

举一个在很多博客都看到过的例子：

盒子里总共有若干个除颜色外均相同的球，进行100次有放回的随机摸球实验，摸到红球和白球的次数分别是30和70。用最大似然估计法求盒子中红球和白球比例。

解：

设红球比例为p，则白球为(1-p)。

则出现题目中结果(30次红，70次白)的概率可以写成：

$f(x_1,x_2,...,x_{100}|\theta)=f(x_1|\theta)\times f(x_2|\theta)\times ...\times f(x_{100}|\theta)$

$=p^{30}(1-p)^{70}$---------------------------式1

其中$x_i$代表第i次实验结果。

> ps:  我一直觉得上面这个式子有问题....这是问题不考虑红球白球取出的次序，计算概率时不是应该再乘以一个$C_{100}^{30}$吗？   因为常数系数不影响之后的求导结果，所以这个问题不影响下面计算，但还是很想知道为什么。。。

好，实验结果(抽100次，有30次红70次白)我们已经知道了，所以当理论上这个概率(上式的值)越大，说明实际情况发生的可能性也越大，实验结果符合预期岂不是美滋滋:happy:。

So，我们希望式1的值尽可能大。即让式1取最大值时，此时参数p的取值就时我们对p的最大似然估计。

那么，直接对式1求导就行了：$f^{'} =0\Longrightarrow p =0.3 $。也就是说当p=0.3时，出现这种实验结果(30,70)的可能性最大。这和我们常识的推测一致。所以0.3是我们求得的参数p的最大似然值。

### 例子2 正态分布

假如有一组采样值$(x_1,x_2,...,x_n)$，我们知道其服从正态分布，且标准差已知。当这个正态分布的期望为多少时，产生这个采样数据的概率为最大？

这个例子中正态分布就是模型M，而期望就是前文提到的未知参数$\theta$。

似然：$L(\theta|x_1,x_2,...,x_n)=f(x_1,x_2,...,x_n|\theta)=\prod\limits_{i=1}^nf(x_i|\theta)$

正态分布的公式：$M=f(x)=\frac1{\sqrt{2\pi}\sigma}\exp \left(-\frac{(x-\mu)^2}{2\sigma^2}\right)$      $N(\mu,\sigma^2)$

似然值：$f(x_1,x_2,...,x_n|\theta)=\left(\frac1{\sqrt{2\pi}\sigma}\right)^n\exp\left(-\frac1{2\sigma^2}\sum\limits_{i=1}^n(x-\mu)^2\right)$

对上面式子求导可得：$l^{'}=0\Longrightarrow \sum\limits_{i=1}^n(x_i-\mu)=0\Longrightarrow\mu=\frac1n\sum\limits_{i=1}^nx_i$

最大似然算法推导出的正态分布的期望和我们尝试算出来的一样。

## 总结

我们可以得到最大似然估计的算法步骤如下：

1. 写出似然函数；
2. 如果直接求导困难，则两边同时取$\ln$对数，化成对数似然函数；
3. 求导；
4. 根据导数=0，求出极值点。

# EM算法

EM(Expectation Maximal)算法，也称最大期望算法。

[很接地气的EM算法解读](https://www.jianshu.com/p/1121509ac1dc )

强烈推荐看上面这篇博客看，我觉得算法就是需要这种通俗的讲解才能真正吃透。这里就不累述了。
