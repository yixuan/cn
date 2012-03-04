---
date: '2012-02-28 00:18:31'
layout: post
slug: julia-comes
status: publish
title: Julia来了
wordpress_id: '680'
categories:
- R
- 学习中
tags:
- Julia
- R
- R语言
- 并行
- 有理数
- 编程
- 语言
- 速度
---

> ## _In short, because we are greedy._


今天看到一篇文章，是[介绍一门编程语言Julia的](http://sd.csdn.net/a/20120223/312315.html)。文章底下的评论有些扎眼，建议移步看它的[英文原文](http://julialang.org/blog/2012/02/why-we-created-julia/)。文章的作者，同时也是这门语言的创始人之一，[Stefan Karpinski](http://karpinski.org/)，目前是加州大学圣踏芭芭拉分校的一名博士生，但曾经在若干家企业工作过。他自称是一名数据科学家和应用数学家，最近发表的一篇论文题目叫作_Non-Parametric Discrete Mixture Model Recovery via NNMF_。看到这里我隐约感觉到，这门语言有可能是我感兴趣的。

为什么又来了一种语言？作者给出的回答就是第一段的那句话——因为我们贪得无厌。我们想要C的速度，想要Python的通用性，想要R的统计，想要Matlab的代数运算，想要很多很多以及更多。Julia是否能成为这样的一门编程语言我目前还无法预知，但它目前的一些特性确实让我眼前一亮。


### 与R及Matlab相似的语法


Julia的官方网站是[http://julialang.org/](http://julialang.org/)，源代码放置在github上，地址是[https://github.com/JuliaLang/julia](https://github.com/JuliaLang/julia)。从中可以看到一些示例的代码，典型的Matlab风格，但好在与R的差距并不是很大，像我依葫芦画瓢也能写出一些比较简单的代码来，比如下面就是用一个暴力的二重循环来生成一个1000*1000的均匀分布随机数矩阵，并计算所有元素之和。

Julia:

    function fun1j()
        m = zeros(1000, 1000)
        s = 0
        for i = 1:1000
            for j = 1:1000
                m[i, j] = rand()
                s = s + m[i, j]
            end
        end
        s
    end


与之等价的R程序是

R:

    
    fun1r = function() {
        m = matrix(0, 1000, 1000)
        s = 0
        for(i in 1:1000)
        {
            for(j in 1:1000)
            {
                m[i, j] = runif(1)
                s = s + m[i, j]
            }
        }
        s
    }




### 速度


速度应该是所有的编程语言都要考虑的内容。我们都知道R的痛点在哪儿，像上面的这个R程序照常理是要挨板子的，但我们还是看看它的运行时间。在我的电脑上（就不贴硬件参数了），运行时间大概为：

R:

    
    > system.time(fun1r())
      用户  系统   流逝
    5.769 0.004 5.782


然后在Julia中，

Julia:

    
    julia> @elapsed fun1j()
    0.09904909133911133


是的你没看错，只有不到0.1秒。

但这并不是一次有意义的对比，因为上述过程是可以被向量化的。我们用R来把这段程序优化一下：

R:

    
    fun1optr = function() {
        m = matrix(runif(1000 * 1000), 1000, 1000)
        sum(m)
    }
    
    > system.time(fun1opt())
      用户  系统   流逝
    0.048 0.000 0.051


同样地，Julia也进行改写：

Julia:

    
    function fun1optj()
        m = rand(1000, 1000)
        sum(m)
    end
    
    julia> @elapsed fun1optj()
    0.008805990219116211


相信结果已经很清楚了。


### 有理数


这可能只是一项很小的功能，但有些时候还是很方便的。Julia用`//`表示有理数除法，比如

    
    julia> 1//2
    1//2
    
    julia> 11//22
    1//2
    
    julia> 987//654 + 12//34 * 56//78
    84917//48178




### 并行


Julia是一门刚诞生不久的语言，因此它尽可能地采用了最新的设计理念来应对当前的问题，其中对并行的支持是很重要的一点。

要让Julia使用多个CPU或多个核，需要在启动时加上相应的参数，例如下面的命令表示让Julia程序启用2个处理器：

    
    $ ./julia -p 2


Julia提供了许多内置的函数来支持多核的运算，在此引用Julia文档中的一个例子，具体的信息可以在[这个页面](http://julialang.org/manual/parallel-computing/)中获取。

    
    M = {rand(1000,1000) | i=1:10}
    pmap(svd, M)


以上程序就是对10个大矩阵进行并行的奇异值分解。


### 接下来的事


由于时间所限，这里举的都是非常基础的一些例子，而Julia的开发应该说也还是刚刚起步。目前Julia只支持类UNIX系统，如Linux和Mac，已经编译好的二进制文件可以在[这里](https://github.com/JuliaLang/julia/downloads)下载。

Julia会不会成为R的一个良好替代，现在还都是未知，但有一点是肯定的，那就是无论一门语言多么优秀，其生命力永远是由用它的人决定的。R的成功，一部分源于其本身的设计，而更重要的是其背后存在一个强大的开源社区。这里有[一个关于R与Julia的讨论](http://groups.google.com/group/julia-dev/browse_thread/thread/9f79ed4f8334830a)，其中Julia的另一名开发者Viral Shah说了一句特别有意思的话："What should julia do in order to provide an alternative to R? In my opinion, it is R's libraries and its community that define it - rather than the language design or its implementation."

R与Julia的未来发展会如何？我们拭目以待。
