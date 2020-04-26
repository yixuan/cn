---
date: '2010-09-17 00:23:34'
layout: post
slug: the-road-to-paralleled-r-1-a-first-trial
status: publish
title: R的原生并行——一个尝试
wordpress_id: '415'
categories:
- R
- 学习中
tags:
- BLAS
- R
- 并行
- 程序
---

一直以来，R的并行计算都是一件令人头疼的问题。尽管有各种各样的包可以辅助地实现并行计算，但R本身依然只能使用一个核，这对于逐渐普及的多核CPU来说确实是一种资源的浪费。而对于我们普通用户来说，并行计算的编程和实现似乎是一件遥不可及的事，但昨天偶然在图书馆翻到的一本书却迅速改变了我的这种观点。下面先把图和真相贴出来。


[![串行计算](https://upload.yixuan.blog/cn/2010/09/serial.png)](https://upload.yixuan.blog/cn/2010/09/serial.png)


[![并行计算](https://upload.yixuan.blog/cn/2010/09/parallel.png)](https://upload.yixuan.blog/cn/2010/09/parallel.png)


上面两幅图展示了同一个函数（矩阵乘法）的两种实现途径，第一种是普通的计算，从图中可以看出，只有一个CPU核处在完全使用状态，总的CPU利用率是50%；而第二种方法采用了并行计算，两个CPU核都是100%的使用率。从时间上来看，第二种方法几乎为第一种的一半，与CPU的利用率相吻合。

可能你会觉得，第二个程序会比第一个程序复杂很多，但实际上，在它们的C程序源代码中，两段程序只有**一行**是不同的！程序的C源代码、编译出的dll文件以及R源代码可以在[这里](https://bitbucket.org/yixuan/cn/downloads/test_parallel.zip)下载得到，其主要原理就是使用了OpenMP这个支持并行计算的库。一个简单但很有启发性的介绍可以看[这里](http://rajorshi.net/blog/2009/05/programming-for-multicore-introduction-openmp-gcc/)。

事实上，R的很多代数运算都是基于BLAS系统的，而从网上的一些介绍来看，它也有支持并行计算的版本，如果可以把它直接链接到R中，那就有好戏看了……
