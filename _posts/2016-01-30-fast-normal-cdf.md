---
date: '2016-01-30 04:00:41'
layout: post
slug: fast-normal-cdf
status: publish
title: 标准正态分布函数的快速计算方法
categories:
- 学习中
tags:
- 函数
- 正态分布
- 卷积
- 速度
- 编程
- 误差

---

标准正态分布的分布函数 $\Phi(x)$ 可以说是统计计算中非常重要的一个函数，基本上有正态分布的地方都或多或少会用上它。在一些特定的问题中，我们需要大量多次地计算这个函数的取值，比如我经常需要算正态分布与另一个随机变量之和的分布，这时候就需要用到数值积分，而被积函数就包含 $\Phi(x)$。如果 $Z\sim N(0,1), X\sim f(x)$，$f$ 是 $X$ 的密度函数，那么 $Z+X$ 的分布函数就是

$$P(Z+X\le t)=\int_{-\infty}^{+\infty} \Phi(t-x)f(x)\mathrm{d}x$$

我们知道，$\Phi(x)$ 没有简单的显式表达式，所以它需要用一定的数值方法进行计算。在大部分的科学计算软件中，计算的精度往往是第一位的，因此其算法一般会比较复杂。当这个函数需要被计算成千上万次的时候，速度可能就成为了一个瓶颈。

当然有问题就会有对策，一种常见的做法是略微放弃一些精度，以换取更简单的计算。在大部分实际应用中，一个合理的误差大小，例如 $10^{-7}$，一般就足够了。在这篇文章中，给大家介绍两种简单的方法，它们都比R中自带的 `pnorm()` 更快，且误差都控制在 $10^{-7}$ 的级别。

第一种办法来自于经典参考书 *Abramowitz and Stegun: Handbook of Mathematical Functions*
的[公式 26.2.17](http://people.math.sfu.ca/~cbm/aands/page_932.htm)。其基本思想是把 $\Phi(x)$ 表达成正态密度函数 $\phi(x)$ 和一个有理函数的乘积。这种办法可以保证误差小于 $7.5\times 10^{-8}$，一段C++实现可以在[这里](http://www.johndcook.com/blog/cpp_phi/)找到。（代码中的常数与书中的略有区别，是因为代码是针对误差函数 $\mathrm{erf}(x)$ 编写的，它与 $\Phi(x)$ 相差一些常数）

我们来对比一下这种方法与R中 `pnorm()` 的速度，并验证其精度。

{% highlight r %}
library(Rcpp)
sourceCpp("test_as26217.cpp")

x = seq(-6, 6, by = 1e-6)
system.time(y <- pnorm(x))
## user  system elapsed
## 1.049   0.000   1.048
system.time(asy <- r_as26217ncdf(x))
## user  system elapsed
## 0.293   0.019   0.311
max(abs(y - asy))
## [1] 6.968772e-08
{% endhighlight %}

可以看出，A&S 26.2.17 的速度大约是 `pnorm()` 的三倍，且误差也在预定的范围里，是对计算效率的一次巨大提升。

那么还有没有可能更快呢？答案是肯定的，而且你其实已经多次使用过这种方法了。怎么，不相信？看看下面这张图，你就明白了。

<!-- more -->

<div align="center">
  <img src="http://i.imgur.com/buX1QWo.png" alt="正态分布概率表" />
</div>

没错，这种更快的方法其实就是两个字：查表。它的基本想法是，我们预先计算好一系列的函数取值 $(x_i,\Phi(x_i))$，然后当我们需要计算某个点 $x_0$ 时，就找到离它最近的两个点 $x_k$ 和 $x_{k+1}$，再用线性插值的方法得到 $\Phi(x_0)$ 的近似取值：

$$\Phi(x_0)\approx \frac{x_0-x_k}{x_{k+1}-x_k}\Phi(x_{i+1})+\frac{x_{k+1}-x_0}{x_{k+1}-x_k}\Phi(x_i)$$

什么？觉得这个方法太简单了？先别急，这里面还有不少学问。之前我们说了，我们需要保证这种方法的误差不超过 $\epsilon=10^{-7}$，因此就需要合理地选择预先计算的点。由于 $\Phi(-x)=1-\Phi(x)$，我们暂且只需要考虑 $x$ 为正的情况。如果让 $x_i = ih,i=0,1,\ldots,N$，那么对函数 $f$ 进行线性插值的误差将不超过（[来源](http://pages.cs.wisc.edu/~amos/412/lecture-notes/lecture09.pdf)）

$$E(x)\le \frac{1}{8}\Vert f''\Vert_{\infty}h^2$$

其中 $\Vert f'' \Vert_{\infty}$ 是函数二阶导绝对值的最大值。对于正态分布函数来说，它等于 $\phi(1)\approx 0.242$。于是令 $E(x)=10^{-7}$，我们就可以解出 $h\approx 0.001818$。最后，只要 $x_N>5.199$，即 $N\ge 2860$ 并另所有 $x>x_N$ 的取值等于1，就可以保证整个实数域上 $\Phi(x)$ 的近似误差都不超过 $10^{-7}$。

这种简单方法的实现我放在了 [Github 上](https://github.com/yixuan/fastncdf)，源程序和测试代码也可以在文章最后找到。下面给出它的表现：

{% highlight r %}
library(Rcpp)
sourceCpp("test_fastncdf.cpp")

x = seq(-6, 6, by = 1e-6)
system.time(fasty <- r_fastncdf(x))
## user  system elapsed
## 0.043   0.024   0.066
max(abs(y - fasty))
## [1] 9.99999e-08
{% endhighlight %}

与之前的结果相比，相当于速度是 `pnorm()` 的15倍！

我们似乎一直以为，在计算机和统计软件普及以后，一些传统的做法就会慢慢被淘汰，例如现在除了考试，或许大部分的时间我们都是在用软件而不是正态概率表。从教学与实际应用的角度来看，这种做法是[应该进行推广和普及的](http://yihui.name/cn/2009/04/how-and-what-to-teach-in-statistics/)，但这也不妨碍我们从一些“旧知识”中汲取营养。关于这种大巧若拙的做法的故事还有很多，比如广为流传的[这一则](http://www.matrix67.com/blog/archives/362)。在计算资源匮乏的年代，科学家们想出了各种巧妙的办法来解决他们遇到的各种问题。现如今计算机的性能已经远不是当年可以媲迹，但前人的很多智慧却依然穿透了时间来为现在的我们提供帮助，不得不说这也是一种缘分吧。

附：[实现和测试源程序](http://yixuan.cos.name/cn/files/2016/01/normal_cdf.zip)