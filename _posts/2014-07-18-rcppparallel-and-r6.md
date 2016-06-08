---
date: '2014-07-18 18:20:31'
layout: post
slug: rcppparallel-and-r6
status: publish
title: RcppParallel 和 R6
categories:
- 学习中
- R
tags:
- 并行
- OOP
- 软件包
- 创新
---

最近 RStudio 的几位大佬频发大招，JJ 写了一个 [RcppParallel](https://github.com/RcppCore/RcppParallel)，用来在 Rcpp 中支持并行计算，而 Winston 发布了 [R6](http://cran.r-project.org/web/packages/R6/index.html)，意在紧随“R5”的步伐，将 R 的面向对象编程（OOP）向前再推一把。

RcppParallel 是 Rcpp 核心组件的新成员，目前还没有发布到 CRAN，但可以从 github 安装。我感觉它最大的亮点是给类 UNIX 系统和 Windows 系统提供了一个统一的接口，因为 R 中大部分支持并行的包都没法在 Windows 上工作。

当然了，像其他 Rcpp 组件一样，RcppParallel 主要是用来写 C++ 代码的，因此对大部分的 R 用户来说其作用依然有限。RcppParallel 中最重要的一个函数是 `parallelFor`，下面给一个简单的例子，来说明其用法。假设我们想并行计算 R 中的 `qf` 函数（计算非中心化 F 分布的分位数），我们可以写下面的 R 代码（C++ 代码被放到了字符串中，供 R 调用）：

<!-- more -->

```r
includes = '
using namespace Rcpp;
using namespace RcppParallel;

struct Qf : public Worker
{
   const double *x;
   double df1;
   double df2;
   double ncp;
   double *result;
   
   Qf(const NumericVector &x_, double df1_, double df2_, double ncp_,
      NumericVector &result_) :
       x(x_.begin()), df1(df1_), df2(df2_), ncp(ncp_),
       result(result_.begin()) {}
   
   void operator()(std::size_t begin, std::size_t end) {
       for(std::size_t i = begin; i < end; i++)
           result[i] = R::qnf(x[i], df1, df2, ncp, 1, 0);
   }
};
'

code = '
NumericVector parqf(NumericVector x, double df1, double df2, double ncp)
{
    NumericVector result(x.length());
    Qf fun(x, df1, df2, ncp, result);
    parallelFor(0, x.length(), fun);
    
    return result;
}
'

cppFunction(code, depends = "RcppParallel", includes = includes)
```

其中有几点需要解释一下。我们先看主程序 `parqf`，它调用了之前提到的 `parallelFor` 函数。顾名思义，这一句指的是一个并行的 `for` 循环。那么循环体在哪里呢？这就要看里面的 `fun` 参数。`fun` 是 `Qf` 的一个对象，而 `Qf` 又继承自 RcppParallel 中定义好的一个类 `Worker`。这个 `Worker` 类描述了并行计算中每个线程需要干的活，而其核心部分在于那个运算符重载 `operator()`。这又是什么意思？

我们先假设，我们要算一个循环，循环变量从 0 一直到 99，那么要并行计算时，我们将 0 到 49 的部分给第一个线程，而 50 到 99 给第二个线程。也就是说，对于第一个线程，它接收到了两个参数，0 和 49，这就对应了 `operator()` 中的 `begin` 和 `end` （事实上 49 对应的是 `end - 1`）。有了这个信息，线程 1 就可以开始干活了，在这里就是计算 `result[begin]`，`result[begin + 1]`，一直到 `result[end - 1]` 的取值。对于线程 2，它接收到了另一组 `begin` 和 `end` 的取值，但干的活是差不多的，唯一的区别是下标不同。所以说，`operator()` 就代表了每个线程需要完成的那部分循环任务。

说到这里，可能已经把一些看官说晕了。但没办法，C++ 的语法实在是比 R 复杂太多，这也是为什么 R 的简洁性很难被取代。要获取效率，就只能在语法的简洁性上进行牺牲了。

还是直接来看效率的对比，我们分别计算 R 中串行的 `qf` 还有我们实现的 `parqf`，结果如下：

```r
> set.seed(123)
> x = runif(100000)
> system.time(res1 <- qf(x, 3, 5, 3.4))
   user  system elapsed 
  8.010   0.000   8.013 
> system.time(res2 <- parqf(x, 3, 5, 3.4))
   user  system elapsed 
 12.010   0.001   3.017 
> identical(res1, res2)
[1] TRUE
```

我的机器开了 4 个线程，运行时间从 8 秒减少到了 3 秒。

总的来说，效果还是很显著的，但为了实现这一并行的功能，我们多写了几十行 C++ 的代码，而且为了写这几十行 C++ 代码，我们可能还得去读几百页的 C++ 的书。至于值不值得，就是见仁见智了。

另一个要提一下的 `R6` 则要轻松得多了，完全是 R 本身的东西。作者在介绍文档中特意解释了这个名字的来历：R 中早期的 OOP 系统包括 S3 和 S4，几年前做了一个 Reference Class 出来，大家戏称 R5，但官方一直没承认这个名字。这下好了，R6 的名字就这样被占了，R core 们就是想用回 R5 这个名字，估计也会在心里暗叫不爽吧——总感觉包作者是有意想调戏一下诸位老顽固们。

`R6` 说白了，就是想模仿 C++ 那样来在 R 中定义类，它相对于 Reference Class 的改进在于实现了公有成员和私有成员的区分。`R6` 的[说明文档](http://cran.r-project.org/web/packages/R6/vignettes/Introduction.html)很清晰，在这里就不多说了。

最后要说，这两个包都给人眼前一亮的感觉，但相比之下，作为它们基础部分的 R，其本身的功能已经停滞不前很久了。益辉之前分享了一篇文章，[为什么 Perl 输了](http://outspeaking.com/words-of-technology/why-perl-didnt-win.html)，说的就是当创新不再，就离游戏结束不远了。这话还适用于学问，事业，人生，等等。


