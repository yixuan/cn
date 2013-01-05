---
date: '2010-09-18 13:32:21'
layout: post
slug: the-road-to-paralleled-r-2-modify-blas
status: publish
title: R的原生并行2——修改BLAS
wordpress_id: '423'
categories:
- R
- 学习中
tags:
- ATLAS
- BLAS
- R
- 并行
- 测试
- 源代码
---

BLAS的全称是Basic Linear Algebra Subprograms，它是用FORTRAN语言编写的一套跟线性代数运算有关的函数，包括向量与向量的运算、向量与矩阵的运算以及矩阵与矩阵的运算三个层次。R的很多底层运算都是基于BLAS来完成的，比如常见的 `%*%`，用来完成矩阵的乘法。而要让R支持原生的并行计算，一个很重要的方面就是让BLAS本身可以通过并行计算来完成。幸运的是，BLAS中很多运算都是比较规整的循环语句，而这些语句恰恰可以通过OpenMP来实现并行。由于BLAS中的函数非常多，在此为了简便起见，我只修改了其中的dgemm.f文件，该文件主要是实现矩阵乘法运算。下面是我用修改后的BLAS源代码编译出的Rblas.dll文件，用它替换R的bin目录下的Rblas.dll（同时需要把压缩包中其它两个dll文件也复制到bin目录下），就可以对R的并行矩阵运算进行测试（注：程序的有效性可能会受CPU型号和架构的影响）。当然了，说是说修改源代码，其实就是参照[这篇文章](http://rajorshi.net/blog/2009/05/programming-for-multicore-introduction-openmp-gcc/)对一些循环语句进行了并行化的声明。

下载：[Rblas.dll](https://bitbucket.org/yixuan/cn/downloads/Rblas_OpenMP.zip)

其中dgemm.f是修改后的程序，完整的BLAS源代码可以到[这里](http://www.netlib.org/blas/blas.tgz)下载。

下面是我电脑上的一些测试。
测试环境：
	
  * 操作系统——32位 Windows XP Home Edition SP3

	
  * CPU——Intel Core Duo T2300 1.66GHz（双核）

	
  * 内存——1.00GB DDR2


测试程序：

    
    set.seed(123);
    a = matrix(rnorm(4000000), 2000);
    system.time(a %*% a);


测试结果：



	
  1. 用R自带的Rblas.dll：

    
    > system.time(a %*% a);
      用户  系统   流逝
    25.97  0.05 26.05




	
  2. 用附件中的Rblas.dll：

    
    > system.time(a %*% a);
      用户  系统   流逝
    32.76  0.06 16.85




	
  3. 用[ATLAS优化的Rblas.dll](http://ftp.ctex.org/mirrors/CRAN/bin/windows/contrib/ATLAS/)：

    
    > system.time(a %*% a);
      用户  系统   流逝
    11.46  0.01 11.50





其中第一项和第三项测试CPU都只能使用单核。

从结果可以看出，并行计算的时间几乎比一般的串行计算少了一半，但因为程序没有经过优化，所以还不如ATLAS优化过的BLAS。但是，如果CPU核的数量很多，ATLAS优化的BLAS依然只能使用一个核，性能将不会有明显的提升，但并行的BLAS则可以进一步提高性能。

如果要进一步实现R的并行计算功能，我觉得有两方面的工作要做，一是将BLAS中的其它函数都进行相应的修改，而是将并行与ATLAS优化结合起来，进一步提高运算效率。
