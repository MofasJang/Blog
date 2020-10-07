---
title: 测试一下
date: 2020-09-17 20:15:27
tags:
- test
categories:
- 测试
---
## 音乐
{% aplayer "SomethingJustLikeThis" "The Chainsmokers/Coldplay" "http://qh04sczlg.hd-bkt.clouddn.com/SomethingJustLikeThis.mp3" "SomethingJustLikeThis.jpg" "lrc:SomethingJustLikeThis.txt" %}

## 按钮
<center>{% btn #, 按一下, home fa-fw fa-lg, Title %}</center>

<!-- more -->
## 便签
{% note info %}
default,primary,success,info,warning,danger
{% endnote %}

## pdf
<iframe src="http://qh04sczlg.hd-bkt.clouddn.com/jianli.pdf" width="100%" height="800px"></iframe>


## iframe插件
```md
{% iframe url [width] [height] %}
```
<iframe 
	height=500 width=100% 
	src="http://qh04sczlg.hd-bkt.clouddn.com/gouge.mp4" 
	frameborder=0 allowfullscreen>
</iframe>

