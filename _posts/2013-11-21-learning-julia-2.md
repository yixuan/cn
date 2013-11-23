---
date: '2013-11-21 20:37:00'
layout: post
slug: learning-julia-2
status: publish
title: 学习Julia（二）
categories:
- 学习中
tags:
- Julia
- R
- t检验
- 程序
- 语言
---

接[上一篇](http://yixuan.cos.name/cn/2013/11/learning-julia-1)。本次包括以下一些新的内容：

- 条件判断
- 异常处理
- 表达式（黑魔法）

这一次我们的线索依然是t检验的程序，我们准备进一步完善它。作为比较，我们依然先把R的输出结果贴出来：

{% highlight r %}
t.test(rnorm(10), mu = 0)
{% endhighlight %}

{% highlight r %}
data:  rnorm(10)
t = 0.768, df = 9, p-value = 0.4622
alternative hypothesis: true mean is not equal to 0
95 percent confidence interval:
 -0.6509637  1.3201667
sample estimates:
mean of x 
0.3346015 
{% endhighlight %}

上一篇中t检验的程序有几个缺陷：

- 没有对数据进行测试，因为t检验样本量至少为2
- 没有显示置信区间（这本质上与下面的几行代码是一样的，这里是偷懒没把它加上）
- 输出结果中小数点位数太多
- 没有显示第一行的 `data:  rnorm(10)`

下面我们一一解决，照例先把代码贴上：

[![](http://i.imgur.com/iG2ttVy.png)](http://i.imgur.com/iG2ttVy.png)

<!-- more -->

在Julia中产生错误信息的函数与R一样，叫做 `error()`。程序第7到第9行是判断 `x` 的长度，如果不足2，则产生错误信息，退出函数。可以看出，`if` 语句采用的同样是缩进风格，并需要用 `end` 来关闭，但省略了R中的圆括号和大括号。

第15行到第17行是计算置信区间的上下界，其中 `quantile()` 进一步展示了分布函数的操作，这是取分布的分位数，相当于R中的 `q` 系函数，如 `qt()` 等。

在输出部分中，与R一样，四舍五入的函数是 `round()`，在后面我们对要输出的数值都进行了舍入处理。

最后，本篇中最大的坑来了，R中那行诡异的 `data:  rnorm(10)`。

可能你会觉得，这行输出与其他的语句没什么区别，按理就是一些字符串操作，但实际情况要复杂得多。让我们先做一个实验。在R里面，如果你运行

    t.test(rnorm(100))

那么输出结果中的第一行就是 `data:  rnorm(100)`。如果你输入的是

    t.test(1:10)

输出结果就是 `data:  1:10`。如果函数里面是一个已经定义好的变量 `x`，那输出结果就是 `data:  x`。换言之，Ｒ会把你输入的表达式原封不动地打印出来。也就是说，当表达式传递给函数时，表达式本身并不会先求值，而是把表达式整体传递给函数。当函数要用到表达式的值时，才会运行这段表达式。这种特性在Ｒ中称为“惰性求值”（lazy evaluation）。很晕对不对，确实，让我们看一个简单的例子来帮助理解。

请分别运行下面两段程序：

{% highlight r %}
f1 = function(x)
{
    return(NULL);
}
f1(print(1));

f2 = function(x)
{
    y = x;
    return(NULL);
}
f2(print(1));
{% endhighlight %}

输出结果应该是这样的：

    > f1(print(1));
    NULL
    > f2(print(1));
    [1] 1
    NULL

看到区别了吗？运行 `f1(print(1))` 的时候这个1并没有被打印出来，而在 `f2(print(1))` 中却会。这是因为，在 `f1()` 中，我们根本就没有用到函数的参数 `x`，所以表达式传递给函数后没有再被使用，R也就不会计算它的取值。而在 `f2()` 中，我们需要把 `x` 的值赋给另一个变量，这样就必须对其进行计算。在R中，我们可以通过 `substitute()` 函数来捕获参数本身的表达式，然后将其转换成字符串，输出到屏幕上。

然而在Julia中，非常遗憾，所有的参数在传递之前都要计算取值，这是[在Julia设计之初就定下的规矩](https://groups.google.com/forum/#!topic/julia-dev/lDI2E1UjjbA)，所以我们无法知道数据是通过何种表达式生成的，也就无法输出类似于 `data:  rnorm(10)` 这样的结果。

当然，我说的是无法实现自动的惰性求值，如果只是要实现表达式的操作，Julia也是可以的。Julia用冒号和括号来表示表达式，比如

{% highlight julia %}
:x
:(randn(5) + 2)
:(x + 1)
{% endhighlight %}

当表达式只包含一个元素时，它的类型就变成了符号（Symbol），比如上面的 `:x`。你甚至可以先把表达式存下来，然后对其取值，或是转换成字符串。

{% highlight julia %}
expr = :(x + 1)
x = 1
eval(expr)
string(expr)
{% endhighlight %}

总结一下，惰性求值是R中非常独特的一个功能（“作者你够了，这篇文章应该是写Julia的吧！”），最常见的两个例子，一个是 `library(MASS)` （或其他任何一个 `library()` 语句），一个是 `plot(rnorm(10), rnorm(10))`。不知道你有没有发现惰性求值存在的痕迹呢？
 
当然，惰性求值基本属于黑魔法范畴。Julia虽然不支持这一特性，但对于大部分的应用来说，并不会造成太大的问题——它基本属于锦上添花一类，而不是必需的。

最后依然是涉及到的一些R与Julia的对比。


| R                   | Julia                        | 作用       |
|---------------------|------------------------------|----------|
| `error()`           | `error()`                    | 生成错误信息   |
| `qt(0.975, df)`     | `quantile(TDist(df), 0.975)` | t分布分位数   |
| `round(x, 3)`       | `round(x, 3)`                | 四舍五入     |
| `substitute()`      |                              | 捕获表达式    |
| `expression(x + 1)` | `:(x + 1)`                   | 生成表达式    |
| `eval(expr)`        | `eval(expr)`                 | 表达式求值    |
| `as.character(x)`   | `string(x)`                  | 强制转换为字符串 |

