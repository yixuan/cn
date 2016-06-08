---
date: '2010-10-20 18:32:47'
layout: post
slug: be-careful
status: publish
title: 小心！
wordpress_id: '459'
categories:
- 学习中
tags:
- 分布
- 正态分布
- 联合分布
- 边际分布
- 相关
---

客官请听题：话说有两个随机变量，$$X\sim N(\mu_1,\sigma_1^2),Y\sim N(\mu_2,\sigma_2^2)$$，而且这两个正态随机变量的相关系数是 $\rho$，问，(X, Y)的联合分布是什么？

如果你给出的答案是

$$\left(\begin{array}{c}X\\Y\end{array}\right)\sim N\left(\left(\begin{array}{c}\mu_{1}\\\mu_{2}\end{array}\right),\left(\begin{array}{cc}\sigma_{1}^{2}&\rho\sigma_{1}\sigma_{2}\\\rho\sigma_{2}\sigma_{1}&\sigma_{2}^{2}\end{array}\right)\right)$$

那么请打开你的R，然后运行一下下面的代码：

```r
set.seed(1);
x=rnorm(2000);
y=rnorm(2000);
z=ifelse(x*y>0,x,-x);
shapiro.test(z);
mean(z);
sd(z);
cor(y,z);
hist(z);
x11();
plot(y,z);
```

什么？还没缓过神来？请看仔细了，**y和z的符号可是相同的**！那么这说明什么了呢……

这是今天复习高等统计学的时候看到书上写的，看完之后突然感觉自己的IQ和EQ都受到了严重的摧残，后果基本上和[这个时候](http://yixuan.github.com/cn/2009/10/probability-played-a-trick-on-me/)差不多。难道我每隔一年就要被这样打击一次？
