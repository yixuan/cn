---
date: '2009-12-29 13:28:57'
layout: post
slug: many-functions-can-be-called-from-r
status: publish
title: 原来R里面的很多函数是可以直接调用的
wordpress_id: '243'
categories:
- R
- 学习中
tags:
- C
- GCC
- R
- 动态链接
- 编程
---

以前一直只知道用R可以调用C程序，昨天猛然一试发现C也可以调用R的。大家不妨看看R目录include子目录里的Rmath.h看看，里面定义了一系列跟分布有关的函数，也就是我们常用的rdpq系列。但是这个头文件中只有定义，这些函数的实现已经被编译到bin目录下的R.dll中了（这里都是指Windows系统）。下面就简单演示一下怎么在C中调用这些函数。

首先，建立一个能让程序安身立命的文件夹（其实是废话……）；

其次，配置一下能让程序安身立命的环境，主要是把R的bin文件夹添加到系统路径，然后需要有GCC编译器（Rtools中自带了）；

第三，把R目录下的include文件夹以及bin文件夹中的R.dll拷贝到刚才那个安身立命的文件夹中，并把R.dll重命名为libR.dll；

第四，编写C语言文件，示例如下：

    
    #include <stdio.h>
    #include <Rmath.h>
    
    int main()
    {
        double a=Rf_dnorm4(0,0,1,0);
        printf("%f",a);
    
        return 0;
    }


其中`Rf_dnorm4()`是求正态分布密度的函数，参数的意义见R中的`dnorm()`。

第五，假设上述C程序保存为test.c，那么现在总的文件夹中将有如下的东西：一个include文件夹，一个libR.dll以及一个test.c。打开cmd，cd进这个文件夹，然后输入下面的命令：

    
    gcc -o test test.c -I./include -L. -lR


不出意外的话文件夹中应该已经生成了一个test.exe文件。在刚才的cmd中输入test，屏幕上应该就会打印出0.398942，这就是标准正态分布的密度函数在0点的取值。

解释一下gcc命令的几个参数。-o以及后面的test是指定生成的程序的名称；-I./include是让gcc程序在当前目录的include文件夹中搜索头文件（这就是为什么之前要把include文件夹拷过来），因为我们用到了Rmath.h；-L.（注意有个点！）是让gcc程序在当前目录中搜索库文件（比如本例中的dll文件），-lR是指定要载入的库文件的名称（这个比较诡异，比如-l后面如果跟的是abc，那么它要载入的就是libabc.dll，也就是说库文件的开头一定要有lib这个字眼，这也是为什么之前要把R.dll改成libR.dll）。

另外要注意的是，为了保证程序能正确运行，一定要让程序能找到R目录bin文件夹中的几个dll，而且缺一不可。如果你没有将bin目录添加到系统路径，那么就应该将那几个dll拷贝到程序的同一目录下。

嗯，暂时没了。
