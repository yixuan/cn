---
date: '2010-04-30 18:24:11'
layout: post
slug: install-ubuntu-10-04-from-hard-disk
status: publish
title: 硬盘安装Ubuntu 10.04
wordpress_id: '377'
categories:
- 学习中
tags:
- Ubuntu
- 安装
- 硬盘
---

整个四月一篇博客也没写，今天来充个数好了，内容非原创，只不过是把Ubuntu硬盘安装的方式记录一下，免得到时候又上网到处找。



	
  * 第一步，下载GRUB4DOS，把其中的grub.exe和grldr解压到C盘根目录。

	
  * 第二步，创建menu.lst，内容如下：
  
    <pre>title Install Ubuntu
    root (hd0,1)
    kernel (hd0,1)/vmlinuz boot=casper iso-scan/filename=/ubuntu-10.04-desktop-i386.iso ro quiet
    splash locale=zh_CN.UTF-8
    initrd (hd0,1)/initrd.lz</pre>
理论上Windows的C盘应该是(hd0,0)的，但不知为何我这儿成了(hd0,1)。
	
  * 第三步，修改Windows启动项，加上语句
  
    <pre>C:\grldr="Install Ubuntu"</pre>

  * 第四步，把光盘镜像复制到C盘根目录，并解压其中的vmlinuz和initrd.lz。

	
  * 第五步，重启，进入Ubuntu光盘。其中要注意，安装之前需要在终端执行

    <pre>sudo umount -l /isodevice</pre>


我下的这个版本，安装完Ubuntu后Windows XP的启动菜单会消失，这时需要在终端下执行

    
    sudo update-grub


但据说官方会修复这个问题，把光盘重新打包。

另：关闭按钮放在左边确实有些别扭……

[![](https://i.imgur.com/DmOrZ.png)](https://i.imgur.com/DmOrZ.png)
