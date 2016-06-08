---
date: '2009-10-02 21:29:22'
layout: post
slug: my-first-php-program
status: publish
title: 平生第一个PHP程序
wordpress_id: '67'
categories:
- 学习中
tags:
- PHP
- WordPress
- 摘要
- 程序
- 函数
---

一波未平，一波又起。刚把图片的居中和背景搞定，突然发现文章的自动摘要又出问题了。山寨版学院网站用的是WP Kit CN的插件，但不知为什么，字数控制总是不好使，不管怎么样都会把第一段整个显示出来，好像只能控制段落数而不能控制字数。我看COS用的也是这个插件，怎么就可以精确做到250个字呢？

后来想研究一下插件的代码，但这对于我这个PHP盲来说实在太困难了，光找到那个起作用的函数就花了好长时间。之后半猜半蒙查函数库，算是大概知道怎么回事了，不过得到的结果有点让我吃惊。那个函数里面貌似是通过换行符号“\n”来划分“单词”的，也就是说如果内容本身的分段不多，那么这个函数就基本上没有什么效果了。

我目前对摘要的一个想法是，如果文章有多段，那么摘要就只显示第一段；如果第一段太长，那么再精简句子。有了这个想法后我就开始硬着头皮把WP Kit CN插件的wp-kit-cn.class.php文件（摘要函数所在的文件）进行改写，最后把里面的`WKC_excerpt()`函数改成了下面的语句：

```php
function WKC_excerpt ($text){
    global $post;
    remove_filter('the_excerpt', 'mul_excerpt');
    remove_filter('the_excerpt_rss', 'mul_excerpt');
    if ( '' == $text ) {
        $text = $post->post_content;
        $text = apply_filters('the_content', $text);
        $text = str_replace(']]>', ']]>', $text);
        $text = strip_tags($text);
        $text = trim($text);
        $wordnum = $this->options['excerpt_words_number'];
        $firstpara=explode("\n", $text);
        $text=$firstpara[0];
        $text=$this->cut_str($text,$wordnum);
        $words = explode("。", $text);
        $output = '';
        if ( count($words) == 1 ) {
            $output=$text;
        }else{
            for($i=0;$i<count($words)-1;$i++){
                $output.=$words[$i].'。';
            }
        }
        $output.='……';
        return $output;
    }
    return $text;
}
```

其中有些函数和变量是来自这个文件的其它地方，要查看全部的代码的话可以到[http://wordpress.org/extend/plugins/wp-kit-cn/](http://wordpress.org/extend/plugins/wp-kit-cn/)去下载插件。这段程序的意思大概就是先提取文本的第一段，然后截取前`$wordnum`（通过WP Kit CN的设置获取）个字符，再判断这段文字中有多少个句号，如果没有句号就全部返回，如果有就返回最后一个句号前的所有文本，最后再加上一个意味深长的省略号，就算是完成了。

这段程序其实非常粗糙，首先是没有加上“阅读全文”之类的超链接，我这里是因为模版会在另外一个函数中提供，所以就删了，其实插件原来的文件中是有的；然后就是分句的条件太过简单，没有考虑问号感叹号之类的，不过感觉学院网站的新闻不太可能出现这些符号，所以就偷懒没改。

不管怎么说吧，好歹是自己的第一个PHP程序，就在此纪念一下吧。
