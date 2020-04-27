---
date: '2011-03-21 21:24:35'
layout: post
slug: processing-js-and-integration-with-wordpress
status: publish
title: Processing JS+Wordpress整合！
wordpress_id: '518'
categories:
- 学习中
tags:
- Javascript
- Processing
- WordPress
- 插件
- 图形
---

Processing有Javascript版本了！虽然这已经是个[很老很老的新闻](http://processingjs.org/)，但今天突然看到有款[Wordpress插件](http://www.keyvan.net/code/processing-js/)，可以直接在Wordpress里写Processing程序，于是果断装上试试。下面是一段示例（Processing的JS库比较大，需要等它下载完毕，所以可能得过一段时间才出现）：

<div style="text-align: center;">
<script type="text/javascript" src="//yixuan.blog/cn/assets/themes/delicate/js/processing-1.4.1.min.js"></script>
<script type="application/processing">
//Info: http://processingjs.org/reference
float x=60,y=240,z=-120;
float x2=61,y2=240,z2=-120;
int sigma=120,rho=336,beta=32;
float vx=sigma*(y-x),vy=x*(rho-z)-y,vz=x*y-beta*z;
float vx2=vx,vy2=vy,vz2=vz;
float deltat=0.0005;

void setup() {
  size(500, 500);
  background(0);
}
void draw() {
  frameRate(60);
  dot1();
  dot2();
}
void dot1()
{
  smooth();
  stroke(0,0);
  fill(255,0,0,100);
  ellipse(x+250,y+220,5,5);
  x+=vx*deltat;
  y+=vy*deltat;
  z+=vz*deltat;
  vx=sigma*(y-x);
  vy=x*(rho-z)-y;
  vz=x*y-beta*z;
}
void dot2()
{
  smooth();
  stroke(0,0);
  fill(0,0,255,100);
  ellipse(x2+250,y2+220,5,5);
  x2+=vx2*deltat;
  y2+=vy2*deltat;
  z2+=vz2*deltat;
  vx2=sigma*(y2-x2);
  vy2=x2*(rho-z2)-y2;
  vz2=x2*y2-beta*z2;
}
</script>
<canvas width="500" height="500"></canvas>
</div>

效果与Java版本相比几乎没有区别！
