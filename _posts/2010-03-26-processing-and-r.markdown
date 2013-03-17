---
date: '2010-03-26 15:25:51'
layout: post
slug: processing-and-r
status: publish
title: Processing R，你看见了什么？
wordpress_id: '371'
categories:
- R
- 学习中
tags:
- Java
- Processing
- R
- 可视化
---

标题里的Processing不是一个动名词，而是一个纯粹的名词。很早以前谢老大就曾[在COS论坛中推荐过Processing](http://cos.name/cn/topic/15015)，特别是在“统计图形与数据可视化兴趣小组”成立之后。

简单来说，Processing（主页[http://processing.org](http://processing.org)）是一门定位于数据可视化的编程语言，它是用Java编写的，本身也可以跟Java互通，但Processing专注于图形方面的开发，所以在图形的绘制上（2D及3D）Processing具有更加简洁的语法，画出来的图也很漂亮。

用Processing编好的程序，会被编译成Java中的类，同时还可以根据需要生成相应的可执行文件，在具有Java环境的系统中运行。因为是基于Java，所以Processing也是跨平台的（同时也是开源的）。

可以先简单看看下面的这张截图，演示的是洛仑兹混沌理论的“蝴蝶效应”，动态运行的程序和源代码放在附件的Lorenz文件夹中（文章最后），运行需要Java环境支持，如果没有的话可以到[http://www.java.com/](http://www.java.com/)下载。

[![](http://i.imgur.com/pLrQ43D.png)](http://i.imgur.com/pLrQ43D.png)

Processing确实很艺术，不过对于数据的理解，光靠画图还是不够的。老大之前说过，就目前而言，Processing基本上跟统计没什么关系，因此它的作用更像是画图而不是数据可视化。如果想要结合统计分析与图形表达，一个很自然的想法就是让Processing这样的绘图工具与R这样的统计计算软件结合起来。R也有很强大的绘图功能，但是有一个小问题就是，对于动画这种表现形式，在R中会受到一些制约，这主要体现在R加载图形时会有一小段时间的缓冲，因此每一秒钟加载图形的数量不能太多，这使得图形的运动不够平滑，而且也较难表现高速的变化。Processing正好可以弥补这点不足，它默认的帧速是60帧/秒，这对于一般的视觉体验来说是足够了。

幸运的是，Processing与Java的结合非常紧密，而R与Java的沟通又可以通过rJava包来实现，所以在Processing中使用R的函数也就成为了可能。下面再给一个演示布朗运动的例子，程序和源代码放在附件的Brown_trace文件夹中。以下两张图是两次布朗运动的“轨迹”实现，其中需要用到的随机数都是通过R来生成的。

[![](http://i.imgur.com/hOxyEfD.png)](http://i.imgur.com/hOxyEfD.png)

[![](http://i.imgur.com/FudU4RR.png)](http://i.imgur.com/FudU4RR.png)

（大家来找茬：通过以上两幅图，你能找出两次“轨迹”实现中**共同的起点**吗？）

要在Processing中调用R的函数，需要做以下几件事：



	
  * 把R的bin目录放到系统PATH中；

	
  * 在R中装好rJava包；

	
  * rJava包下面有一个jri文件夹，把这个文件夹的路径也放到系统PATH中。


在购建Processing的项目时，点击Sketch->Add File...添加之前jri文件夹中的JRI.jar文件，然后参照Brown_trace的例子写一些导入库的语句以及TextConsole类的声明。后面就可以通过Rengine和REXP等定义好的类型去调用R的函数，这一部分可以参照jri文件夹下的examples。

附件：[点击这里下载](https://bitbucket.org/yixuan/cn/downloads/Processing.zip)

说明：每个例子都包含三个文件夹，格式是application.*，代表了不同的操作系统。再里面有一个source文件夹，其中.pde文件是Processing的源文件，.java文件是将.pde文件转换成的java源文件。
