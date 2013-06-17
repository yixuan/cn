---
date: '2013-06-16 10:45:40'
layout: post
title: R并行计算的相关资料
categories:
- 学习中
- R
tags:
- R
- 并行
- 翻译
- 文档
---

R 从 2.14.0 开始加入了 `parallel` 包，用以支持 R 中的并行计算。但是在这方面，相关的文档和书籍还是不是很多，我目前知道的有下面几本：

* Q. Ethan McCallum 和 Stephen Weston 的 [*Parallel R*](http://shop.oreilly.com/product/0636920021421.do)，是一本专门讲解 R 并行的书，内容较深，包括了`snow`，`multicore`，`parallel` 几个包和 `Hadoop` 的内容。
* Norman Matloff 老爷子的 [*Art of R Programming*](http://nostarch.com/artofr.htm)，里面的第16章标题就是“Parallel R”。这本书已经有了[中文版](http://book.jd.com/11243704.html)，嗯，这是广告。
* 还是 Norman Matloff 老爷子写的，书名叫 [Programming on Parallel Machines](http://heather.cs.ucdavis.edu/parprocbook)，其中的第10章是讲 R 的并行，是对上面编程艺术那本书的补充。这本书很有意思，是一本“开源”的书，可以免费下载得到。
* Joseph Adler 的 [R in a Nutshell, 2nd Edition](http://shop.oreilly.com/product/0636920022008.do)，其中第26章讲的是R和Hadoop。[思喆](http://www.bjt.name/)，[舰哥](http://jliblog.com/)和[一硕](http://yishuo.org/)正在翻译此书。
* `parallel` 包自带的小品文（vignette），介绍了 `parallel` 包的基本用法和注意事项。其他类似的还有 `foreach`，`doMC`，`doParallel` 等一系列并行包的小品文，这些文档前前后后加起来也不少了。

中文文档方面，目前为止成型的书可能只有翻译好的编程艺术一本，其他的主要还是一些技术文档，比如阿稳之前博客上的一系列文章（目前暂时无法访问，之后加上链接）。

在看 `parallel` 包的小品文时，觉得这篇文档可以作为并行 R 的入门参考读物，与编程艺术中的章节有很多对应的地方，所以就花时间把它翻译成了中文，放在了[github](https://github.com/yixuan/parallel-translation)上，供各位参考和修正。后续如果有精力准备继续翻译 `foreach`，`doMC` 等包的小品文，如果客官愿意加入进来也可以直接在 github 中一起协作完成。

