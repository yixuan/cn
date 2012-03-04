---
date: '2011-10-31 21:53:26'
layout: post
slug: rationalfun
status: publish
title: rationalfun
wordpress_id: '640'
categories:
- R
- 学习中
tags:
- 有理函数
- 积分
- 论文
- 软件包
- 黑魔法
---

刚刚向CRAN提交了一个新包，名字叫`rationalfun`。这个包顾名思义，是处理有理函数的。毕业论文里面有一块内容是要求有理函数的积分，数学分析课本中给出的方法是部分分式，但这个在程序中不好实现；而如果用数值积分，则速度又太慢，不划算。后来费尽千辛万苦（这里是夸张的修辞手法），终于在一篇计算机的论文中找到了解决的办法，于是索性把算法写成了包，也就是`rationalfun`。本来想把论文放到博客上的，[但听说要坐牢](http://www.fanren8.com/read.php?tid=31536)，心想还是算了。论文的信息是


> T. N. Subramaniam, and Donald E. G. Malm, How to Integrate Rational Functions, _The American Mathematical Monthly_, Vol. 99, No.8 (1992), 762-772.


目前这个包还很小很天真，能进行的运算无非是加减乘除乘方导数和积分，下面的代码差不多概括了主要的用法：

    
    > r1 = rationalfun(c(1, 1), c(1, rep(0, 8), 1));
    > r1;
     1 + x
    -------
    1 + x^9
    > simplify(r1);
                           1
    -----------------------------------------------
    1 - x + x^2 - x^3 + x^4 - x^5 + x^6 - x^7 + x^8
    > r2 = rationalfun(c(1, 1), c(1, rep(0, 6), 1));
    > r3 = rationalfun(c(0, 1), c(1, rep(0, 6), 1));
    > r4 = r2 - r3;
    > r4
       1
    -------
    1 + x^7
    > r4^2
           1
    ----------------
    1 + 2*x^7 + x^14
    > deriv(r4)
         -7*x^6
    ----------------
    1 + 2*x^7 + x^14
    > integral(r4)
    0.142857142857143 * log(abs(x + 1)) + 0.0890699716941046 * log(x^2 +
        1.24697960371747 * x + 1) + 0.223380423562293 * atan((x +
        0.623489801858734)/0.78183148246803) - 0.031788704850902 *
        log(x^2 - 0.445041867912629 * x + 1) + 0.27855083205195 *
        atan((x - 0.222520933956314)/0.974927912181824) - 0.128709838271774 *
        log(x^2 - 1.80193773580484 * x + 1) + 0.123966782605016 *
        atan((x - 0.90096886790242)/0.433883739117558) + 0


是不是有一种在进行符号运算的错觉？

另：读牛人写的包总能发现令人震惊的东西，`rationalfun`里面很多代码都是依赖或改编自`polynom`包的，里面发现了这样一个函数：

    
    as.function.polynomial <-
    function(x, ...)
    {
        a <- rev(coef(x))
        w <- as.name("w")
        v <- as.name("x")
        ex <- call("{", call("<-", w, 0))
        for(i in seq_along(a)) {
            ex[[i + 2]] <- call("<-", w, call("+", a[1], call("*", v, w)))
            a <- a[-1]
        }
        ex[[length(ex) + 1]] <- w
        f <- function(x) NULL
        body(f) <- ex
        f
    }


这个函数的作用就是用一个系数向量来生成一个多项式函数，里面`as.name()`，`call()`，`body()`之类的黑魔法用得不亦乐乎……
