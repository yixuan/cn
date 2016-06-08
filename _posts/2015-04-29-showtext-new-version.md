---
date: '2015-04-29 22:25:04'
layout: post
slug: showtext-new-version
status: publish
title: showtext 新版本发布
categories:
- R
- 学习中
tags:
- 图形
- 程序
- 字体

---

<div align="center">
  <img src="http://i.imgur.com/7dmcchI.png" alt="quick_example" />
</div>

前两天把 [showtext](http://cran.r-project.org/web/packages/showtext/index.html) 包更新到了 0.4-1 版本。因为这次更新的内容比较多，所以简单写篇博客介绍下。

第一个比较重大的改变是现在可以在图形设备中自动使用 `showtext` 了，用法比以前更为简单。在这个版本之前，需要每次都打开图形设备，调用 `showtext.begin()` 和 `showtext.end()`，然后关闭图形设备。这样反复调用函数显得非常麻烦。在新版本中，只要在最开始调用一下 `showtext.auto()`，之后所有的图形设备就会自动使用 `showtext` 来进行渲染。这对窗口设备尤其重要，因为我们平常用 R 画图时，很少手动去调用 `windows()` 或是 `x11()`，而是直接使用 `plot()` 函数让 R 自动打开一个窗口。调用了 `showtext.auto()` 之后，窗口设备也可以自动使用载入的字体了，这对于预览图形效果会很有帮助。

自动调用的另一个好处是能在 `ggsave()` 中使用 `showtext` 了。`ggsave()` 本身会自动打开和关闭图形设备，所以在以前的版本中我们无法在 `ggsave()` 中插入 `showtext.begin()`。现在自动化以后，`ggsave()` 也可以使用 `showtext` 的字体库了。

要关闭自动调用功能，只需执行 `showtext.auto(FALSE)`。

第二个比较显著的改进是更好地支持了位图图形。在之前的版本中，`showtext` 主要用于矢量图，比如 `pdf()` 或者 `svg()`。如果在 `png()` 或是 `jpeg()` 中使用 `showtext`，你会发现画出的字体非常难看。这是因为那些位图设备没有较好的抗锯齿支持。而现在，`showtext` 可以为那些设备绘制好平滑后的字形，所以图片质量也会得到提升。唯一需要注意的是要保持图形设备的 DPI 与 `showtext` 的一致，比如用 `png()` 设备时，应该手动设置一下想要的分辨率（此处 DPI 为120）

```r
library(showtext)
showtext.opts(dpi = 120)
png(..., res = 120)
```

支持位图图形也就意味着支持了窗口设备，比如 `windows()` 和 `x11()`。

最后一个改进其实是之前 `sysfonts` 包的更新内容，就是目前在国内无法直接连接 Google 的字体库，所以在使用 `font.add.google()` 时，可以使用 360 提供的[代理](http://libs.useso.com/)。使用方法是加一个参数，比如

```r
library(showtext)
font.add.google("Gochi Hand", "gochi", repo = "useso")
```

其他的例子和说明都可以参见 Github 上的[介绍](https://github.com/yixuan/showtext/blob/master/README.md)。

<!-- more -->



