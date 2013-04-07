---
date: '2013-04-07 09:34:17'
layout: post
title: 高频函数
categories:
- 学习中
- R
tags:
- 递归
- 函数
- R
- 程序
- 频数
---

`ggplot2`的书马上就要出版了，前段时间太云让我试着做一下封面的图案，大概是分析一下书中的代码，然后生成一个函数使用频数的词云。

要分析函数的使用频率，就得先把函数提取出来，这相当于是做一个语法解析。这个工作可以由R中的`parse()`函数来完成，
它可以读取文件或字符串，将它们转换成R中的`expression`对象。

`expression`对象是一个你每次用R都会见到，但很少直接对它操作的东西，我在这里把它翻译成“表达式串”。表达式串由一个或多个表达式组成，
每一个表达式在R中叫做`call`，代表了一条语句或一次函数调用。`call`对象又可以互相嵌套，比如一个函数调用可以作为另一个函数调用的参数。
一个简单的例子是这样的：

<!-- more -->

{% highlight r %}
> e = expression(x + abs(y), z <- 1);
> class(e);
[1] "expression"
> ca = e[[1]]; ca;
x + abs(y)
> class(ca);
[1] "call"
> ca[[1]];
`+`
> ca[[2]];
x
> ca[[3]];
abs(y)
> class(ca[[3]]);
[1] "call"
{% endhighlight %}

从上面可以看出，我们能用`[[]]`来对`call`对象取子集，例如上面的例子中，`x + abs(y)`是一个`call`对象，其第一个元素是操作符（也是一个函数）`+`，
第二个元素是参数`x`，第三个元素是一个新的`call`对象`abs(y)`。我们的任务就是递归地把所有的函数/操作符提取出来，然后统计频数。

提取R文件中函数名的函数是这样的：

{% highlight r %}
print.exp = function(e)
{
    # 单个元素，不可再分解
    if(length(e) < 2)
    {
        # 单个元素可能是变量、常量等，只打印函数
        if(inherits(e, "call")) cat(as.character(e[[1]]), "\n");
        return(invisible(NULL));
    }
    # 对表达式（串）中的每个元素逐个调用print.exp本身
    for(i in 1:length(e))
    {
        # 一般的表达式类名为'call'
        # 赋值表达式和大括号是特殊的表达式，
        # 类分别为'<-'和'{'，需要单独列出
        if(inherits(e[[i]], c("call", "=", "<-", "{")))
        {
            print.exp(e[[i]]);
        } else {
            # 表达式的第一个元素为函数名，如plot(x, y)，
            # 在表达式列表中即为'plot'，'x'和'y'
            # 类似地，x + y的列表形式为'+'，'x'和'y'
            if(i == 1) cat(as.character(e[[i]]), "\n");
        }
    }
    invisible(NULL);
}
{% endhighlight %}

用的时候先让`parse()`解析文件，然后套到这个函数里面，就可以把文件里面的函数名打印出来了。为了处理方便，可以用`sink()`重定向输出，例如：

{% highlight r %}
op = options(warn = -1);
sink("output.txt");
print.exp(parse("someRfile.R"));
sink();
options(op);
{% endhighlight %}

《ggplot2：数据分析与图形艺术》中的代码托管在[这里](https://github.com/cosname/ggplot2-translation/)，
利用上面编写的函数配合`wordcloud`包，最终做出的词云是这样的：

[![](http://i.imgur.com/YcPh8Tz.png)](http://i.imgur.com/YcPh8Tz.png)


