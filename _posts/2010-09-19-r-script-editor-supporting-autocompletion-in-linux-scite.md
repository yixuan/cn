---
date: '2010-09-19 11:06:01'
layout: post
slug: r-script-editor-supporting-autocompletion-in-linux-scite
status: publish
title: Linux下支持自动完成的R编辑器——SciTE
wordpress_id: '433'
categories:
- R
- 学习中
tags:
- Linux
- SciTE
- Ubuntu
- 编辑器
- 自动补全
- 配置
---

之前谢老大写了一篇[在Notepad++中配置R函数自动完成的文章](http://yihui.name/en/2010/08/auto-completion-in-notepad-for-r-script/)，甚好甚强大，不过可惜的是Notepad++只能在Windows下用，没有Linux的版本。当然了，Linux下的编辑器也不少，拿Ubuntu来说，简单的有Gedit，高级的有Kate，神级的有Emacs、Vim等等，但本文介绍的SciTE绝对是一款甚小甚强大的编辑器软件。

Ubuntu下安装的方法就不用说了，`sudo apt-get install scite` 就可以搞定。软件非常小，但其功能绝对不容小觑，只要你有耐心设置好配置文件，几乎任何一种语言都可以实现语法高亮、代码折叠和自动完成等功能。下面是编辑R脚本时的截图。

[![SciTE自动完成](https://upload.yixuan.blog/cn/2010/09/scite-1.png)](https://upload.yixuan.blog/cn/2010/09/scite-1.png)

[![SciTE函数提示](https://upload.yixuan.blog/cn/2010/09/scite-2.png)](https://upload.yixuan.blog/cn/2010/09/scite-2.png)

要实现上面的效果，需要一些额外的配置文件，可以到下面下载。

下载：[SciTE配置文件](https://bitbucket.org/yixuan/cn/downloads/SciTE_config.zip)

配置方法：


  * 用管理员权限打开/usr/share/scite/SciTEGlobal.properties（具体的位置视系统而定），搜索其中的“import r”语句，将其前面的井号去掉；


  * 将附件中的r.api用管理员权限复制到/usr/share/scite中；


  * 将附件中的.SciTEUser.properties复制用户目录下，即/home/<用户名>。


一些主要的配置选项都放在.SciTEUser.properties中，其中大都加了注释，可以按需要进行修改。修改时可以参照[SciTE的官方文档](http://www.scintilla.org/SciTEDoc.html)获得完整的说明，没错，那里有上百个选项可以进行自定义，只要你有足够的耐心……

事实上，SciTE是一个跨平台的编辑器，其配置文件也具有良好的兼容性，只需对配置文件做些许修改，在Windows下同样可以完成相同的功能。这里有个Windows版的SciTE，体积是小到令人发指的600K，要使用自动完成功能的话，需要把SciTEGlobal.properties的最后一行改成正确的路径。



下载：[Windows版SciTE](https://bitbucket.org/yixuan/cn/downloads/SciTE_win.zip)
