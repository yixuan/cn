---
layout: index
title: 怡然轩
tagline: 平生书癖已无恙，解名缰，更逃羁网
---
{% include JB/setup %}

{% for i in (0..5) %}
{% assign post = site.posts[i] %}
<div class="post">
  <div class="title">
    <h2><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></h2>
    {% if post.date %}
    <small>{{ post.date | date: "%Y 年 %m 月 %d 日" }} | 分类于
    {% else %}
    <small>某年 某月 某日 | 分类于
    {% endif %}
    {% for category in post.categories %}
    <a href="{{ BASE_PATH }}{{ site.JB.categories_path }}#{{ category }}-ref" title="{{ category }}" rel="category">{{ category }}</a>{% unless forloop.last %}，{% endunless %}
    {% endfor %}
    </small>
  </div><!-- END title -->
  <div class="entry">
    {{ post.content | split: '<!-- more -->' | first }}
    <div class="clear"></div>
  </div><!-- END entry -->
  <p><a href="{{ BASE_PATH }}{{ post.url }}">阅读全文→</a></p>
</div><!-- END post -->
<div class="deco-line"></div>
{% endfor %}

<p><a href="{{ BASE_PATH }}{{ site.JB.archive_path }}">所有文章列表→</a></p>

