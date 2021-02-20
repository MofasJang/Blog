---
title: 测试一下
date: 2020-09-17 20:15:27
tags:
- test
categories:
- 测试
---
## tab选项卡
{% tabs tab,2 %}
<!-- tab -->
this is tab1
<!-- endtab -->
<!-- tab -->
this is tab2
<!-- endtab -->
<!-- tab -->
this is tab3
<!-- endtab -->
{% endtabs %}

## 音乐
{% aplayer "SomethingJustLikeThis" "The Chainsmokers/Coldplay" "http://file.panjiangtao.cn/SomethingJustLikeThis.mp3" "SomethingJustLikeThis.jpg" "lrc:SomethingJustLikeThis.txt" %}

## b站外链
https://xbeibeix.com/api/bilibili/biliplayer/?url=

## 按钮
<center>{% btn #, 按一下, home fa-fw fa-lg, Title %}</center>

<!-- more -->
## 便签
{% note info %}
default,primary,success,info,warning,danger
{% endnote %}

## pdf
<iframe src="http://file.panjiangtao.cn/jianli.pdf" width="100%" height="800px"></iframe>


## iframe插件
```md
{% iframe url [width] [height] %}
```
<iframe 
	height=500 width=100% 
	src="http://file.panjiangtao.cn/gouge.mp4" 
	frameborder=0 allowfullscreen>
</iframe>

