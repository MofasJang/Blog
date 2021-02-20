---
title: 手写字体制作并在Next上部署
date: 2020-11-15 10:51:40
tags:
- daily
- hexo
categories: 
- 生活
---
{% note info %}
下载[江涛休闲体](http://file.panjiangtao.cn/ziti.ttf)
{% endnote %}

# 字库制作
## 准备
　　在[flexifont](http://www.flexifont.com/flexifont-chn/mine/)上注册并下载需要的字体模板，如下图。
![字体模板](moban.jpg)
<!-- more -->
　　推荐使用6763字+符号6886字这个模板，包含了GB2312完整字库，基本上碰不到这以外的字体。

## 书写
　　将下载的pdf文件打印出来写，或者在电脑上用PS+手绘板、ipad上用pencil。这一步是整个过程最麻烦的部分，博主耗时18个小时。
　　注意事项：
　　1. 请使用黑色或蓝色的圆珠笔、水笔或者钢笔，请勿使用签字笔、铅笔和彩笔。
　　2. 书写时，保证文字尽量居中，大小匀称，不要压到或超过边框。

## 拍照上传
![拍照要求](paizhao.jpg)

## 生成字库
![生成字库要求](shengcheng.jpg)
　　生成完毕后下载ttf文件，打卡后安装即可在word文档中使用。

# 博客安装
## 转码
　　在[字体文件转码网站](https://www.aconvert.com/cn/image/ttf-to-svg/)将ttf文件分别转码成svg、woff、eot文件，下载备用。

## 安装
　　* 在Hexo文件夹中的source文件夹下建立_data文件夹，在其内新建styles.styl文件。
　　* 在主题文件夹下的source文件夹下建立fonts文件夹，其内放入所有字体文件。
　　styles.styl文件内容：
```css
@font-face {
    font-family: 'JTHandwrite'; /*引号中为字体名，将下列所有JTHandwrite用你的文件名代替 */
    src: url('/fonts/JTHandwrite.eot');
    src: url('/fonts/JTHandwrite.eot?#iefix') format('embedded-opentype'),
        url('/fonts/JTHandwrite.svg') format('svg'),
        url('/fonts/JTHandwrite.ttf')  format('truetype');
    font-weight: 500;
}
```
　　在主题配置文件_config.yml中修改成如下：
```yml
font:
  enable: true

  host:

  # Global font settings used for all elements inside <body>.
  global:
    external: false
    family: 'JTHandwrite' # 改为上面你的font-family
    size:
```

## OK,完事了。