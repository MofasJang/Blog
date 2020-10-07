---
title: Vue框架搭建的餐厅管理系统
date: 2020-09-18 20:07:34
tags: 
- Vue
categories: 
- frontend
- Vue
---

{% note info %}
使用系统前务必阅读本文件！如果没有安装好环境，可以直接访问[餐厅管理系统](https://rest.guoshaocong.cn)，功能与我们的代码是一致的。
{% endnote %}

## 餐厅管理系统

### 简介
一个餐厅管理系统，前端由Vue框架进行开发，后端使用js，和数据库一起部署在阿里云上。
<!-- more -->
### 功能
1. 用户正常登陆与登出
2. 前台管理系统可以满足用户进行开台、买单与结账数据分析的需求
3. 基础信息管理可以满足用户进行对餐品与餐桌的管理
4. 服务管理部分可以满足用户进行点餐，开单，签单的需求
5. 人员管理部分可以满足用户进行对顾客和员工的管理

## 环境安装
由于本系统后端部分部署在阿里云上，所以只需安装前端依赖

### 安装Node.js，换源

- 打开[Node.js官网](https://nodejs.org/en/)，选择LTS版本下载（目前为12.16.1），并进行安装。
- 使用淘宝源：`npm config set registry https://registry.npm.taobao.org`
- 验证换源是否成功：`npm config get registry`

### 安装vue-cli

- 安装：`npm install -g @vue/cli`
- 验证：`vue --version`

### 下载项目源码

- 使用git下载 
`git clone https://github.com/MofasJang/restaurant`
- 访问[github仓库](https://github.com/MofasJang/restaurant)下载

### 安装项目所需依赖

- 确保终端位于frontend目录内
- `npm install`来按照package.json安装所有所需依赖

### 进行本地测试

- 运行开发服务器：`npm run serve`
- 访问终端中显示的地址 `http://localhost:8080/` ，应该能看到前端页面。


## 为了方便于登陆，默认账号密码可以回车录入，点击登陆，就可以使用本系统了！
