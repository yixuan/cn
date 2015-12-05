---
date: '2015-12-05 09:44:28'
layout: post
slug: weekly-journal-3
status: publish
title: 周记（三）
categories:
- 生活
- 学习中
tags:
- 周记
- 网络
- Flash
- SWF
- 怀旧
- Spark
- ADMM
- 大什么数据
- 技术

---

周日在朋友家吃火锅，其间Z同学喊了句“藕下了啊”，我便调侃了句“这话在好多年前的 BBS 上就是我要下线了的意思”，然后W同学评论说暴露年龄了，于是接下来又互相聊出了更多暴露年龄的典故，像是多图杀猫，九城社区，东北人都是活雷锋，小小，Showgood 之类。这么列举一下，发现2000年初的网络文化其中很重要的一支就是 Flash 动画，而这其中 Showgood 毫无疑问是当时中国 Flash 界的主力军之一。

第一次听说 Showgood 我记得是初中的时候去P姑娘家玩，她推荐了大话三国系列中的某一部作品给我看，当时我就被那部动画的画风和搞笑台词惊艳到了，后来家里能拨号上网，就自己去 Showgood 的网站上看，现在依然还有印象的就是三集左右的《千里走单骑》，还有后来的《小兵的故事》。下午回到公寓后特意又搜了下，发现 Showgood 这个网站居然还在，只是已经成了一家做互联网儿童教育的创业公司。尽管已经改头换面，但进网站后迎面那句“这些年，你们还好吗？”还是让人唏嘘不已。

但其实在我心目中真正的 Flash 神作还是小小系列，也就是当时风靡整个 Flash 界的火柴人动画。我至今还记得小小的域名是 xiaoxiaomovie.com，用 whois 查了下，并没有被注销掉，只是网站已经无法访问了。小小的影响力之大，我清楚地记得曾经玩过一个英文的 Flash 格斗游戏，里面可选的角色是各类美国的超级英雄和日本的动漫角色，但列表中赫然有着 Xiao Xiao 的名字。可惜后来再想找到这个游戏，就如同大海捞针了。少年时的记忆就像是擦肩而过的路人，偶尔留下一抹难忘的影子，又很快地融入到黑压压的人群中，再不见踪迹。

<!-- more -->

<object width="600" height="338" type="application/x-shockwave-flash" data="http://flash.ngfiles.com/video_player/videoplayer.swf" id="video_embed" style="visibility: visible;">
<param name="wmode" value="direct">
<param name="allowscriptaccess" value="never">
<param name="allowfullscreeninteractive" value="true">
<param name="allowfullscreen" value="true">
<param name="play" value="false">
<param name="flashvars" value="NewgroundsAPI_PublisherID=1&amp;NewgroundsAPI_SandboxID=5663073e76acc&amp;NewgroundsAPI_SessionID=null&amp;NewgroundsAPI_UserName=%26lt%3Bdeleted%26gt%3B&amp;NewgroundsAPI_UserID=0&amp;ng_username=%26lt%3Bdeleted%26gt%3B&amp;url=http%3A//uploads.ungrounded.net/alternate/8000/8601_alternate_24378.mp4%3F-62169966000&amp;title=null&amp;max_resolution=1080&amp;promo_url=http%3A//www.newgrounds.com/portal/exitroll/42307">
</object>

初中那会儿大概就是受众多 Flash 动画的影响，曾经痴迷过好一阵的 Flash 动画制作，那时每次去书店都会瞄一眼有没有关于 Flash 的新书，有合适的就买回去学习里面的例子。这种痴迷当然是在某个时候戛然而止，然而万万没有想到它对我的影响却几乎一直存在。大一下学期，计算机公共课上老师讲了几节课的扩展话题，其中之一就是 Flash，还留了作业，让大家自己做一个 Flash 动画，上传到作业系统中，而且互相可以投票，选出最受欢迎的十件作品。当时这个作业几乎重燃了我初中时学做 Flash 的所有热情，虽然技术一直很渣，但却乐此不疲，拿鼠标一点一点画背景画角色画时间轴，简直就像是从当年穿越过来一样。后来我提交的动画拿了个第二名，我确信它至今还保存在我某个移动硬盘中，只是一时半会儿也找不出来了。

现在回想一下 [R2SWF 诞生记](http://yixuan.cos.name/cn/2013/12/story-of-r2swf-2/)，虽然当时 Flash 早就开始没落，快被 HTML5 取而代之，但依然花了这么多时间来维护这个包，是不是潜意识里也在向那个辉煌的 Flash 时代致敬呢？

怀旧结束。这周干了一件正事，就是在传海的课上讲了一下 Spark 上 [ADMM 算法](http://joegaotao.github.io/cn/2014/02/admm/) 的[实现](https://github.com/yixuan/ADMM-Spark)，幻灯片放在了[这里](http://statr.me/files/ADMM-Spark.html)。周日是感恩节假期最后一天，下午收到邮件说让我们第二天讲，我和队友Z同学顿时就吓傻了，因为当时幻灯片还没做。幸好10月份的时候秉帅让我在南昌R会上做了一个关于 ADMM 的报告，大部分的片子都可以直接翻译一下拿来用，所以秉帅真是救了我一命呢。当然到了周一的时候，有另一组的三位大神救场，我们的报告被安排在了周三，讲出来效果还不错。

这年头技术更新太快，但大部分都是知易行难。像是 Spark 和 Scala 这些，在各种报告中幻灯片中看到的是一回事，实际做起来就是另一回事。没有亲自尝试一遍，完全无法理解各种细节上的琐碎。现在来看 Scala/Spark 上许多重要的库还不是非常完善，但其实只要有需求，有活跃的开发者和社区，相信短板都会慢慢补上。如今的江湖风起云涌，各大门派雄据一方，且看未来将如何变幻。











