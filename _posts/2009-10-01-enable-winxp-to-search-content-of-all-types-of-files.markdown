---
date: '2009-10-01 23:09:13'
layout: post
slug: enable-winxp-to-search-content-of-all-types-of-files
status: publish
title: 让Windows XP实现所有类型文件的包含文字搜索
wordpress_id: '62'
categories:
- 学习中
tags:
- PHP
- WinXP
- WordPress
- 包含文字
- 搜索
---

最近几天都在弄WP的模版，虽然我一直是怀着无比崇敬的心情去研究那些免费提供的模版，但再好的模版总会有一些细节不合你的心意——倒不是因为功能太少了，而是因为功能太多以至于有些东西你不想放在页面上。当然了，这对于我这个htmlxmljavascriptphpcss的初级未入门者来说是个好消息——改不过还注释不过吗？

于是乎目前的工作基本上就是找到恰当的模版的恰当的页面然后恰当地注释掉那些显示我不想显示的东西的函数，而我感觉在一大堆PHP语句中找到那些函数的定义简直就跟炼狱一般。最后实在不行就准备用Windows XP的包含文字搜索功能，在文件夹中递归搜索文件。但是曾经有一段时间怎么也搜不出来，后来才知道，原来Windows XP默认的情况是只搜索特定的几种文件类型，比如*.txt，而*.php却不在其中。

接着就是百度Google一齐上，找到了一个解决方案：[http://www.phpx.com/happy/thread-109195-1-1.html](http://www.phpx.com/happy/thread-109195-1-1.html)。我用的是第二种方法，感觉直观些，就是找到注册表中的HKEY_LOCAL_MACHINE\SYSTEM\ControlSet\Control\ContentIndex，然后把FilterFilesWithUnknownExtensions子键的值改为1。之后就可以搜索到php文件里面的内容了。
