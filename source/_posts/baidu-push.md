---
title: Hexo博客将链接自动提交百度
date: 2020-09-25 16:29:10
tags: Hexo
categories: 
- Hexo
---
试过多种方法，发现这个是最高效的SEO

# 安装插件
```js
npm install hexo-baidu-url-submit  –save
```
<!-- more -->

# 在根目录配置文件_config.yml中：
## 添加新字段
需要在[百度资源平台](https://ziyuan.baidu.com/)注册并获取token秘钥
```yml
baidu_url_submit:
  count: 100 # 提交最新的一个链接
  host: wudong.tech # 在百度站长平台中注册的域名
  token: 23GYpxowKnhVsUM # 请注意这是您的秘钥， 所以请不要把博客源代码发布在公众仓库里!
  path: baidu_urls.txt # 文本文档的地址， 新链接会保存在此文本文档里
```

## deploy中添加新的type
```yml
deploy:
- type: git       #这行最前面加-
  repo: 
  branch: master
- type: baidu_url_submitter     #这行是新加的
```

# 最后重新deploy一下就好啦。