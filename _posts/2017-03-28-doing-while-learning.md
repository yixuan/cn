---
date: '2017-03-28 22:38:09'
layout: post
slug: doing-while-learning
status: publish
title: 现学现卖
categories:
- 学习中
tags:
- 数据库
- SQL
- PHP
- 插件
- 论坛
---

之前统计之都一直在酝酿一系列的网站改版，但因为种种原因中断了一些时间，直到最近才又把改版计划提上了日程。其中[主站的部分](https://github.com/cosname/cosx.org)由谢大牵头，目前已经召集了一支[精锐部队](https://github.com/orgs/cosname/teams/editors)大刀阔斧地在 Github 上开干了，而论坛的部分则是由我接盘，准备把当前有些老旧的 bbPress 系统迁移到 [Flarum](http://flarum.org/) 上去。

俗话说得好，不写 SQL 的前端工程师不是好的 PHP 程序员。我前阵子决定接论坛的盘是因为上一次论坛搬家的时候费力研究过 bbPress 的数据库结构，所以在看过 Flarum 的后台后觉得这个可以有，然而实际开始搬的时候才发现掉进了天坑里，而这个锅主要得甩给[反人类的 SQL](http://blog.schauderhaft.de/2010/02/15/why-sql-sucks/)。就这么说吧，假设所有的表都存成数据框（Data frame），在 R 里10分钟能搞定的活用 SQL 就非得耗掉你一小时，其中30分钟是在搜需要的函数，而这30分钟里面的25分钟是在确认并不存在我需要的那个函数。依我看如果科技公司要招数据库程序员，技术面试的问题都可以免了，直接给个论坛的数据带回家，一周之内成功转到另一个系统就算通过。这个考验的不是懂多少 SQL，而是考察这个人有没有足够的耐性写一千行枯燥的 `SELECT` 和 `UPDATE` 而不至于暴走。

就在我天真地以为数据库导好后就万事大吉时，我才发现万里长征只迈出了第一脚。原因是当前的 Flarum 系统还不成熟，许多功能核心开发者还没有打算实现，比如我发现它的密码验证机制跟当前论坛的不一样，还有默认的编辑器不支持数学公式等。怎么办？没办法，只好参照[官方文档](http://flarum.org/docs/extend/)的描述，给论坛写些扩展的插件了。可是论坛的后端是用[最好的编程语言](https://www.zhihu.com/question/26133313)写的，还是基于什么高大上的 [Laravel](https://laravel.com/) 框架，我一个平时用 R 做统计计算的，真的办不到啊。

所以最后能做的就是依葫芦画瓢，现学现卖了。找了几个官方出品的插件，模仿着它们的结构边查文档边改代码，最终也算是把程序都调通了。[统计之都的 Github](https://github.com/cosname) 上一堆 flarum 开头的项目都是这时期的产物。

<!-- more -->

<div align="center">
  <img src="{{ site.JB.BASE_PATH }}/images/flarum.png" alt="Flarum插件" />
</div>

说实话，论坛这种上古遗物可能确实已经有些跟不上时代了，或许只有我们这种前朝遗老还愿意花时间和精力把数据库里面一行行的记录搬来搬去，视若珍宝。不过新论坛现在看上去还是挺简洁美观的，新增的功能也都非常实用，各位大侠不妨点开看看这个[预览版](https://cos.name/flarum/)，然后到[这个帖子](https://cos.name/cn/topic/418468/)去提些意见和建议。在此先行谢过。
