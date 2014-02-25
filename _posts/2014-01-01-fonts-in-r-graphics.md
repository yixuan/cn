---
date: '2014-01-01 17:01:57'
layout: post
slug: fonts-in-r-graphs
status: publish
title: R绘图字体解决方案
categories:
- 学习中
- R
tags:
- 图形
- 程序
- 软件包
- 字体
- 图形设备
---

[COS论坛](http://cos.name/cn/topic/147359)里面经常会遇到的一个问题就是绘图时中文字体怎么解决。最初，一个流行的方法是使用`family = "GB1"`，但一般这样做出来的图比较难看，而且并没有完全解决问题。后来发现了[Cairo](http://cran.r-project.org/web/packages/Cairo/index.html)包，[喜大普奔](http://cos.name/cn/topic/101521)，但后来又发现它选字体的时候不太明确，也就是说我不知道怎样才能选中我想要的那个字体。再然后，[Winston Chang](https://github.com/wch)写了[extrafont](https://github.com/wch/extrafont)包，功能很完善，但需要系统里面安装Ghostscript。我是懒人，不想花时间在配置Ghostscript上，于是写了一个叫做`showtext`的包。（好像哪里不对）

<div align="center">
  <img src="http://i.imgur.com/IdFtOLP.png" />
</div>

<!-- more -->

`showtext`详细的说明写在我的[英文博客里面](http://statr.me/2014/01/using-system-fonts-in-r-graphs/)，四级的词汇量，很好懂的。这里就简单说明一下用法，因为用法挺简单的。

上面那幅图所有的代码如下，当然要事先将其中用到的四个[字体文件](https://bitbucket.org/yixuan/cn/downloads/showtext-example-fonts.zip)放到工作目录下。

{% highlight r %}
library(showtext)
library(Cairo)

font.add("newrocker", "newrocker.ttf")
font.add("aerofoil", "aerofoil.ttf")
font.add("xkcd", "xkcd.ttf")
font.add("nextdoor", "thegirlnextdoor.woff")

CairoPNG("showtext.png", 640, 400)
showtext.begin()
plot(1, type = "n", family = "xkcd", cex.axis = 1.5, cex.lab = 1.5)
text(1, 1.2, "showtext 0.2", cex = 5, family = "nextdoor",
     col = "#F8766D")
text(1, 0.8, "has been released!", cex = 5, family = "newrocker",
     col = "#00BFC4")
title("Free to use fonts", family = "aerofoil", cex.main = 2)
showtext.end()
dev.off()
{% endhighlight %}

其实也就两步：先用`font.add()`加载字体，然后用`showtext.begin()`和`showtext.end()`把绘图语句包含进来。

`font.add()`的第一个参数是绘图时字体的系列名（family），也就是告诉R如何在绘图时引用这个字体。第二个参数是字体的路径，如果字体在工作目录下就直接打字体名。当然，操作系统一般都会有一些默认的字体安装位置，`showtext`（实际上是其依赖的[sysfonts](https://github.com/yixuan/sysfonts/)包）会尝试搜索这些路径。在Windows下，直接输入

{% highlight r %}
font.add("songti", "simsun.ttc")
{% endhighlight %}

就可以加载宋体。

加载完了字体，R本身还不知道如何使用它们，所以，绘图的语句需要包含在一对`showtext.begin()`和`showtext.end()`中间。这时候只需要指定合适的`family`参数就可以使用这些字体了。

`showtext`自带了[文泉驿微米黑](http://wenq.org/wqy2/index.cgi?MicroHei)字体，如果只是想在图中使用中文，只需要加载`showtext`，然后使用`showtext.begin()`和`showtext.end()`即可。

注意`showtext`只对当前打开的图形设备有效，如果当前没有图形设备，或是设备已关闭，`showtext`都是不起作用的。

`showtext`的开发页面在[https://github.com/yixuan/showtext](https://github.com/yixuan/showtext)，欢迎到那里拍砖捉虫。在此留言亦可，重谢。

