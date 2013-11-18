---
date: '2013-11-18 10:54:00'
layout: post
slug: learning-julia-1
status: publish
title: 学习Julia（一）
categories:
- 学习中
tags:
- Julia
- R
- t检验
- 程序
- 语言
---

[Julia](http://julialang.org/) 0.2 发布了。

[很早以前](http://yixuan.cos.name/cn/2012/02/julia-comes/)写过一篇博客简单地介绍了一下这门新兴的语言，现在第二个稳定版本发布，看来可以有计划地学习一下了。

作为本系列（设想中，如果烂尾了请人艰不拆）的第一篇，我想先编写一个简单的单样本 t 检验程序，来展示一下Julia的一些基本语法和特性。当然，我是从一个R用户的角度来学习Julia的，所以并不会从最基本的操作开始，而是侧重于统计计算和与R的比较。这次的内容大概有下面这些：

- 基本的向量操作
- 定义函数，以及默认值的使用
- 打印字符串
- 附加包的安装和使用
- Julia中的分布函数
- 查看/设定工作空间，以及运行脚本

作为一个演示的例子，我们模仿R中的`t.test`函数实现一个单样本的t检验。（R的输出如下）

{% highlight r %}
t.test(rnorm(10), mu = 0)

        One Sample t-test

data:  rnorm(10)
t = 0.768, df = 9, p-value = 0.4622
alternative hypothesis: true mean is not equal to 0
95 percent confidence interval:
 -0.6509637  1.3201667
sample estimates:
mean of x 
0.3346015 
{% endhighlight %}

接下来就开门见山，直接上Julia代码。

{% highlight julia %}
# Pkg.add("Distributions")
# Pkg.installed()
using Distributions

function t_test(x, mu = 0)
    n = length(x)
    meanx = mean(x)
    stdx = std(x)
    tstat = abs(meanx - mu) / stdx * sqrt(n)
    tdist = TDist(n - 1)
    pvalue = 2 * ccdf(tdist, tstat)
    
    println("")
    println("        One Sample t-test")
    println("")
    println("t = ", tstat, ", df = ", n - 1, ", p-value = ", pvalue)
    print("alternative hypothesis: true mean is ");
    print_with_color(:red, "not equal")
    println(" to ", mu)
    println("mean of x")
    println(meanx)
end
{% endhighlight %}

由于现在博客的代码环境暂时不支持行号，所以我再贴一张图上来：

[![](http://i.imgur.com/OFDdr5e.png)](http://i.imgur.com/OFDdr5e.png)

逐行解释一下这段代码。

第1行的 `Pkg.add()` 是Julia中安装软件包的命令，相当于R中的 `install.packages()`。同样地，`Pkg.installed()` 是查看已安装的软件包，在R中相当于不带参数的 `library()`。此外可以看出，Julia的注释方法与R一样，是用`#`号。要使用安装好的软件包，需要使用 `using` 语句（第3行），类似于R中的 `library(*package*)`。

`Distributions` 是Julia中处理分布函数的软件包，后面我们将用它来计算t检验的p值。详细的说明文档在[这里](http://juliastats.github.io/Distributions.jl/index.html)。

从第5行开始我们就在定义函数了。Julia定义函数的方法与Matlab相似，是用缩进风格。此外，Julia不允许用点作为变量名，所以我们的函数名改为了 `t_test`。参数列表与R中差不多，其中 `mu = 0` 声明了 `mu` 的默认值。

代码的6~9行有强烈的R既视感，除了把 `sd()` 改成了 `std()`。第10和第11行是Julia中使用分布函数的方法，我们要先创建一个t分布的对象（创建时需要指定自由度），然后调用 `ccdf()` 函数计算生存函数。

再接下来就是字符串的输出。简单地来说，Julia中的 `print()` 就相当于R中的 `cat(..., sep = "")`，而 `println()` 则是 `cat(..., "\n", sep = "", )`。额外的福利是 `print_with_color()` 函数，用它可以来输出彩色的文字。

像R中一样，Julia的代码有两种运行方式，存成脚本，或在控制台里运行。我们先把上述代码保存为脚本文件 `t_test.jl`，然后展示控制台中的操作。

依然与R中一样，在Julia中我们有工作空间的概念。在R中查看和设定工作空间的函数是 `getwd()` 和 `setwd()`，在Julia中则对应为 `pwd()` 和 `cd()`。接下来我们用 `include()` 函数加载这个文件（相当于R中的 `source()`），然后做一个简单的测试：

[![](http://i.imgur.com/hWZiNKG.png)](http://i.imgur.com/hWZiNKG.png)

上面的 `randn()` 是生成标准正态随机数的函数，用分号结尾是避免其取值被打印出来（这两点都源自Matlab）。最后我们用 `exit()` 来退出Julia。

总结一下，Julia借鉴并融合了一些其他科学计算语言的特性，我相信R和Matlab的用户都会对它的语法感到很舒服。下面是本文涉及的R和Julia的函数对比：

| R                          | Julia                | 作用      |
|----------------------------|----------------------|---------|
| `install.packages("pkg")`  | `Pkg.add("pkg")`     | 安装软件包   |
| `library()`                | `Pkg.installed()`    | 查看已安装包  |
| `library(pkg)`             | `using pkg`          | 加载软件包   |
| `length(x)`                | `length(x)`          | 向量长度    |
| `mean(x)`                  | `mean(x)`            | 求均值     |
| `sd(x)`                    | `std(x)`             | 求标准差    |
| `1 - pt(x, df = n)`        | `ccdf(TDist(n), x)`  | t分布     |
| `cat(..., sep = "")`       | `print()`            | 打印字符串   |
| `cat(..., "\n", sep = "")` | `println()`          | 打印一行    |
|                            | `print_with_color()` | 彩色打印    |
| `getwd()`                  | `pwd()`              | 查看工作空间  |
| `setwd()`                  | `cd()`               | 设定工作空间  |
| `source()`                 | `include()`          | 运行代码文件  |
| `rnorm(n)`                 | `randn(n)`           | 标准正态随机数 |
| `q()`                      | `exit()`             | 退出      |

