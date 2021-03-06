---
date: '2020-04-27 11:00:00'
layout: post
slug: self-hosted-comment-system
status: publish
title: 自搭评论系统
categories:
- Blog
- 学习中
tags:
- 博客
- 评论
- Isso
- Disqus
- WordPress
- 服务器
- 数据库
---

在家宅久了很容易滋生拖延症，具体表现就是明明还有很多活堆着，但就是不想动手去做，导致最后浪费了很多时间在发呆上。最近一篇论文审稿快要到期，但审稿意见死活写不出来，于是昨天终于弃疗，索性给自己放个假，做点以前想做但没精力去折腾的事，其中之一就是试着搭一个能自己管理的博客评论系统。

在最早用 WordPress 写博客的时候，评论是直接整合进数据库的，不需要特别操心。后来改用静态博客，省掉了管理服务器的烦恼，但评论就成了一个麻烦。从那以来长时间用的是 [Disqus](https://disqus.com/) 评论系统，虽说一直是免费服务，拿别人手短，但不得不说 Disqus 想力推的“社交元素”可能是这个博客最不需要的东西了。用 Disqus 总有一种“长恨此身非我有”的感觉：评论数据都是 Disqus 掌握，而且没法轻易导入导出，这对数据管理有执念的人来说是很难受的一点。

后来在网上看到了一个开源项目 [Isso](https://posativ.org/isso/)，其目标就是替代 Disqus，利用它可以把评论系统架在自己的服务器上，方便数据备份和管理。这个系统是用 Python 写的，而且存储评论的数据库是 [SQLite](https://sqlite.org/index.html)，非常轻便，不需要动用 MySQL+PHP 这样的大型武器，要备份数据只需要复制一下数据库文件就行。之前注意到这个项目大概是两年前 COS 转静态网页需要找评论解决方案的时候，但那时候 Isso 功能还不是很完善，所以没有采用。昨天再次看的时候感觉已经差不多了，至少在我自己小流量的博客上可以先来测试一下效果。

<!-- more -->

目前来看，Isso 已经满足了我的大部分需求：

1. 不需要注册，填好姓名邮箱就可以发评论；
2. 支持 Markdown 的大部分格式；
3. 可以设置新评论邮件提醒；
4. 直接用 Javascript 加载评论内容，没有内嵌 `iframe`；
5. 可以将 Disqus 的评论导出然后导入到 Isso 中；
6. 操作 SQLite 数据库太方便了。

搭建 Isso 这套系统需要有一台自己的虚拟服务器，我主要参考了[这篇文章](https://lszero.github.io/coding4fun/comments-with-isso.html)的教程。Isso 官方的文档用的是 nginx 服务器，我不太熟悉，而这篇文章的作者给了 Apache2 的解决方案，帮了大忙，在此表示一下感谢。如果有人对搭建 Isso 系统感兴趣的话，我可能会再写一篇详细的教程出来，但目前光是安装和配置就有几个大坑，因此不建议现在上手。比如，目前 Isso 发布在 [PyPI](https://pypi.org/project/isso/) 上的版本跟其他依赖包是不兼容的，因此只能从 GitHub 上的源代码安装。再就是我用的 Linode 服务器屏蔽了从服务器发送邮件的端口，需要申请解封才能使用。

跟 Disqus 相比，Isso 有几个局限：

1. 只能对每一篇文章的评论生成 RSS，因此就没法订阅全部评论了。不过我觉得现在 RSS 用得越来越少，而且我的博客评论也不多，失掉这个功能还是可以接受的。
2. 从 Disqus 导入的评论是没有邮箱信息的。这个本来不是什么大事，但会使得评论没法利用 [Gravatar](https://en.gravatar.com/) 生成辨识度高的头像。说白了还是 Disqus 的锅，因为它有自己的一套头像系统，但不给博主提供评论者的邮箱信息（Disqus 的逻辑大概是说，留下评论的人是 Disqus 的用户，而不是博主的“用户”，因此资料不能随意给博主）。我目前的解决方案是手动为认识的评论者添加邮箱字段，这样只要他们用这个邮箱注册过 Gravatar，就可以正常显示头像。
3. 从 Disqus 转向 Isso 后就没有简单的回头路了，因为 Disqus 导入评论似乎有很多限制。

权衡过后，我觉得由自己掌控数据还是更舒心一些，所以目前整个中文博客的评论已经迁移到自搭的 Isso 系统了。看官可以随意留个评论测试一下，如果效果还行我就长时间使用了。
