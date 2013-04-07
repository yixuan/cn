---
date: '2010-11-17 19:53:45'
layout: post
slug: rhelper-rgtk2-updated
status: publish
title: 更新一下Rhelper-RGtk2
wordpress_id: '480'
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
---

前两天做了一个稍微有些艰难的决定，把[Rhelper-RGtk2](http://yixuan.github.com/cn/2010/08/rhelper-rgtk2/)更新了一下。主要更新的地方有：



	
  * Rd文档的解析函数全部重写，应该速度比之前的快些；

	
  * Rd文档格式的支持更加完整，但目前`\tabular`标签的排版还是个很麻烦的问题；

	
  * 新增包选择功能，可以从下拉框中选择软件包，包中的函数会自动列出；

	
  * 新增过滤器功能，在输入框中键入字符串后按回车，函数列表会根据键入的内容进行匹配，相当于`apropos()`。


测试环境是Windows XP 32位及RGtk2 2.20.3版本，其它环境尚未测试过。

示例：

    
    library(RGtk2);
    Rhelper("help");


截图：


[![Rhelper-RGtk2](http://i.imgur.com/hQ3sD.png)](http://i.imgur.com/hQ3sD.png)



下载：[Rhelper-RGtk2 2010.11.17更新](https://bitbucket.org/yixuan/cn/downloads/Rhelper_RGtk2_20101117_uft8.txt)（注：必须在UTF-8编码下打开）

目前程序还不是特别稳定，暂时仍处于测试状态。
