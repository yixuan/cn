---
layout: page_no_comment
title : 文章归档
header : 文章归档
group: no_navigation
---
{% include JB/setup %}

{% for post in site.posts  %}
    {% capture this_year %}{{ post.date | date: "%Y" }}{% endcapture %}
    {% capture this_month %}{{ post.date | date: "%B" }}{% endcapture %}
    {% capture next_year %}{{ post.previous.date | date: "%Y" }}{% endcapture %}
    {% capture next_month %}{{ post.previous.date | date: "%B" }}{% endcapture %}
  
    {% if forloop.first %}
<h2>{{this_year}}</h2>
    {% endif %}
  
    {% if forloop.last %}
<div style="margin-bottom: 30px">
<p><span class="blog-list-date">{{ post.date | date: "%Y-%m-%d" }}</span>&raquo;
<span class="blog-list-title"><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></span></p>
</div>
    {% else %}
        {% if this_year != next_year %}
<div style="margin-bottom: 15px">
<p><span class="blog-list-date">{{ post.date | date: "%Y-%m-%d" }}</span>&raquo;
<span class="blog-list-title"><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></span></p>
</div>
<h2>{{next_year}}</h2>
        {% else %}
<div>
<p><span class="blog-list-date">{{ post.date | date: "%Y-%m-%d" }}</span>&raquo;
<span class="blog-list-title"><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></span></p>
</div>
        {% endif %}
    {% endif %}
{% endfor %}
