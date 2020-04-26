---
date: '2010-08-09 08:49:13'
layout: post
slug: rhelper-rgtk2
status: publish
title: Rhelper-RGtk2版本
wordpress_id: '401'
categories:
- R
- 学习中
tags:
- R
- GUI
- RGtk2
- Rhelper
- 帮助
- 程序
- Ubuntu
- Windows
---

寒假的时候用wxWidgets程序库写过一个[Rhelper小程序](http://localhost:4000/2010/01/please-help-me-test-a-program-rhelper/)，初衷是在chm类型的帮助文档被取消后，能提供一个类似的查看帮助的环境。当时编的那个程序基本实现了这一目标，但它有两个比较大的缺点：一是程序与R相脱离，调用起来不方便；二是程序只能在Windows下运行，对操作系统有依赖。由于当时能力和时间有限，所以也就没再多加考虑。

之后，从谢老大那里得知了有gWidgets包的存在，顿时兴奋不已，因为这个包可以用简单的语句生成一系列的图形界面，而且不依赖于操作系统，甚至不依赖于图形界面库（GTK+，Java，tcl/tk等）。谢老大也以这个包为主题在[第三届中国R语言会议](http://cos.name/2010/06/3rd-china-r-beijing-summary/)上做了[报告](http://yihui.name/cn/wp-content/uploads/2010/06/3rd-ChinaR-gWidgets-Yihui-Xie.pdf)。

当然了，越是方便的工具，其功能也越容易受到限制。就Rhelper来说，有两个问题必须要得到解决，一是要实现文档格式化（比如以HTML的形式展现），二是要实现超链接跳转。这两个要求在wxWidgets中都有现成的程序库可以解决，可是在gWidgets中都很难实现。[Omegahat](http://www.omegahat.org/)曾经开发了一个[RwxWidgets包](http://www.omegahat.org/RwxWidgets/)，用来在R中调用wxWidgets，可是这个包很久没有更新，而且似乎在Windows下很不稳定。最后综合各方面的因素，决定用比较底层的RGtk2包来实现Rhelper的功能。

由于RGtk2包没有直接解析HTML的控件，所以文档的格式化成了程序中最大的一个难题。目前我是直接从Rd文档中获取文字和相应的标签，然后对不同的标签应用不同的格式。下面是两张效果图，分别是Windows下和Ubuntu下的运行界面：

<p style="text-align: center;">Windows下运行界面</p>
[![](https://upload.yixuan.blog/cn/2010/08/rhelper-windows.png)](https://upload.yixuan.blog/cn/2010/08/rhelper-windows.png)

<p style="text-align: center;">Ubuntu下运行界面</p>
[![](https://upload.yixuan.blog/cn/2010/08/rhelper-ubuntu.png)](https://upload.yixuan.blog/cn/2010/08/rhelper-ubuntu.png)

其中蓝色的链接都是可以点击的（光标不会变成手形），不过目前Rd文档的解析仍然是个大工程，有很多函数的文档会出现格式混乱的现象，这个还需要不断修补。总之这个程序还只是一个初步的框架，很多细节仍有待完善。

附件：[程序代码下载](https://bitbucket.org/yixuan/cn/downloads/Rhelper-RGtk2_dev.txt)

用的时候输入 `Rhelper("&&")` 之类的就可以了。
