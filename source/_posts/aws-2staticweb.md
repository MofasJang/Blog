---
title: 在AWS上托管简单静态页面
date: 2021-02-09 13:55:59
tags:
- 云服务
- AWS
- 亚马逊
categories:
- 云服务
- AWS
---
{% note info %}
参考文章：[host static website](https://aws.amazon.com/cn/getting-started/hands-on/host-static-website/)
{% endnote %}
# 简介
* 本文记录在AWS控制台中使用 AWS Amplify 托管静态网站的过程。AWS Amplify 可提供完全托管的静态网站和 Web 应用程托管。Amplify 的托管解决方案利用 Amazon CloudFront 和 Amazon S3 通过 AWS 内容分发网络 (CDN) 交付您的站点资产。
* 设置连续部署：Amplify 提供了基于 Git 的连续部署工作流，允许您在每次提交代码时自动将更新部署到站点。
<!-- more -->

# 构建
## 一、创建并连接数据库
## 1. 创建并初始化一个存储库。最简单的方法就是使用命令 create-react-app。在命令提示符或终端中使用以下命令安装此软件包。
```javascript
npx create-react-app amplifyapp
cd amplifyapp
npm start
```

## 2. 为应用创建一个新的 GitHub 代码库
![GitHub 代码库](git库.png)

## 3. 初始化 Git 并将应用程序推送到新的 GitHub 代码库，在命令行界面中执行以下命令：
```javascript
git init
git remote add origin git@github.com:username/reponame.git
git add .
git commit -m “initial commit”
git push origin master
```

## 4. 登录[Amplify 控制台](https://signin.aws.amazon.com/signin?redirect_uri=https%3A%2F%2Fconsole.aws.amazon.com%2Famplify%2Fhome%3Fstate%3DhashArgs%2523%26isauthcode%3Dtrue&client_id=arn%3Aaws%3Aiam%3A%3A015428540659%3Auser%2Famplify&forceMobileApp=0&code_challenge=TtR_InsZGGm6AJR4mRegMnEVcuDPSHVTWaZiEAyfK6E&code_challenge_method=SHA-256)，然后在**部署**下选择**入门**。
![Amplify 控制台](入门.png)

## 5. 连接GitHub存储库。在授权 Amplify 控制台后，Amplify 会通过存储库提供商获取访问令牌，但不会将令牌存储在 AWS 服务器上。Amplify 仅使用安装在特定存储库中的部署密钥来访问存储库。
![连接存储库](选择.png)


## 6. 连接存储库服务提供商后，请选择一个存储库，然后选择一个对应的分支来进行构建和部署。
![选择分支](分支.png)

## 二、确认构建设置
　　对于所选择的分支，Amplify 会检查存储库，以自动检测要执行的构建命令的顺序。确认构建设置并单击**下一步**。
![确认构建设置](确认.png)

## 三、保存并部署
　　查看所有设置，以确保所有设置均正确无误。选择**保存并部署**，将Web 应用程序部署到全球内容分发网络 (CDN)。前端构建通常需要 1-2 分钟的时间，但可能会因应用程序的大小而有所差异。
![部署Web应用](部署.png)

