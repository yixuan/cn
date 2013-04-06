---
date: '2009-09-26 20:40:55'
layout: post
slug: use-xampp-to-test-website
status: publish
title: 折腾网站的好方法——XAMPP
wordpress_id: '23'
categories:
- 学习中
tags:
- WordPress
- XAMPP
- 网页
- 服务器
- Ubuntu
---

刚刚学习WordPress少不了一番折腾，不过经常遇到的一个麻烦是担心不当的修改会破坏原有的网页。一个可选的做法是用一个备份镜像做测试，不过在网络上操作毕竟有些不便，比如用FTP传输文件有时候还是挺麻烦的。

前段时间在[一个网站](http://fairyfish.net/2007/06/25/installing-wordpress-locally/)上看到一种方法是在本地安装一个网页服务器，然后把网站的所有文件都放到本地机上，通过网页服务器软件就可以在浏览器中访问这些内容。这样的好处就是在本地修改文件，就能在浏览器中及时看到修改的结果，无需再通过FTP进行增删改操作。

实现的办法就是下载一个名叫XAMPP的软件，网址是[http://www.apachefriends.org/zh_cn/xampp.html](http://www.apachefriends.org/zh_cn/xampp.html)，之前那个网站上也有很详细的说明。我是在Ubuntu下用的，软件的官网也有安装步骤，基本上就是直接解压就行，不需要编译安装，非常方便。唯一要注意的是，在Linux下按照提示提示运行程序可能会提示下面的错误：

    
    Syntax error: "(" unexpected


出现这种情况时，只需要找到XAMPP安装的目录，打开lampp这个文件，把第一行的

    
    #!/bin/sh


改成

    
    #!/bin/bash


就可以了。

之后把网站文件塞到/opt/lampp（或其它的安装目录）/htdocs中就行了，比如就是wordpress文件夹。运行启动服务的命令后，在浏览器中输入http://localhost/wordpress（对应于放进去的文件夹名）就可以在本地安装、测试和折腾WordPress了。
