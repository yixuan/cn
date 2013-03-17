---
date: '2009-10-06 18:29:19'
layout: post
slug: my-heart-for-china
status: publish
title: 我的中国心®
wordpress_id: '76'
categories:
- R
tags:
- R
- 中国心
- 国庆
---

国庆那天ID为微微的朋友在[COS论坛](http://cos.name)的R版发了一个帖子，名叫“[灌灌水庆庆生](http://cos.name/bbs/read.php?tid=16743)”，里面是用随机点图描绘的一幅漂亮的中国心。当时突然想起来以前在百度贴吧看到的[一个帖子](http://tieba.baidu.com/f?ct=335675392&tn=baiduPostBrowser&sc=5137217861&z=511497934&pn=0&rn=30&lm=0&word=%CA%FD%D1%A7#5137217861)，是用一个曲面方程画出一颗立体的心形，于是乎，我就委托Mr. R照着方程的样子也画了一颗。

方程的形式虽然简洁，但画起来可不容易，每画一个点就要解一个方程，而且经常出现斜率不存在的点，所以如果只从一个方向上来画，难免会得出NA解，使得原本光滑的曲面出现很多漏洞（如下图）。

[![h01](http://i.imgur.com/K6Bs7Hu.png)](http://i.imgur.com/K6Bs7Hu.png)[![h02](http://i.imgur.com/2W5SI5r.png)](http://i.imgur.com/2W5SI5r.png)

就因为这样，暂时的解决办法就是从两个相互垂直的方向分别画出曲面，然后把它们重叠起来，将各自的漏洞都填补好。最后再利用rgl包的贴图功能，在心上“贴好”五星红旗，一颗中国心就完成了。

[![heart_snapshot01](http://i.imgur.com/N7XYXTo.png)](http://i.imgur.com/N7XYXTo.png) [![heart_snapshot02](http://i.imgur.com/9CGqGHa.png)](http://i.imgur.com/9CGqGHa.png) [![heart_snapshot03](http://i.imgur.com/SwDd22K.png)](http://i.imgur.com/SwDd22K.png)

附：R源文件和用来贴图的国旗（因为贴图的位置需要做了镜像变换）。点击[这里](https://bitbucket.org/yixuan/cn/downloads/myChina.zip)下载。

运行程序时设好工作空间，可能会有些慢，想要加快些速度可以把程序中的n=100改小一些，当然图形的精细度会略微差些。

另：老大说可以放到fun包里，那是不是得“普世”一点了？比如贴个R上去？:)
