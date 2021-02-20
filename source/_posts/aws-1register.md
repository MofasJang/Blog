---
title: AWS服务的注册和创建主机
date: 2021-02-09 13:06:20
tags:
- 云服务
- AWS
- 亚马逊
categories:
- 云服务
- AWS
---
{% note info %}
参考文章：[注册aws账号创建ec2免费套餐](https://blog.csdn.net/jjihuang/article/details/80777306),[AWS使用教程](https://www.cnblogs.com/ccw869476711/p/11535812.html)
{% endnote %}
# 一、注册aws账号
* 进入[aws注册页面](（https://portal.aws.amazon.com/billing/signup）)
* 填写email、密码、用户名等信息，绑定信用卡(我暂无支持aws的信用卡)
* 完成注册
<!-- more -->

# 二、开通EC2套餐
## 1. 访问[AWS主页](https://aws.amazon.com/)，登录后找到AWS管理控制台
![AWS管理控制](主页.png)

## 2. 在控制台页面点击**服务->计算->EC2**
![选择服务](服务.png)

## 3. 跳转到资源页面先选择区域再点击**启动实例**
![启动实例](启动.png)

## 4. 选择AMI（记得打勾仅免费套餐，过滤出所有的AMI都是免费的。按照自己的需要选择一个，一般选第2个）
![选择AMI](ami.png)

## 5. 实例类型默认，直接**下一步：配置实例详细信息**
![选择实例类型](实例.png)

## 6. 填写详细信息，大都默认
![详细信息](详细.png)

## 7. 添加存储，大小改为30G
![存储大小](30g.png)

## 8. 添加标签直接**下一步**
![添加标签](标签.png)

## 9. 安全组主要开通访问服务器的端口和允许访问的IP，按下图配置完就可以下一步**审核启动**
![安全组配置](安全组.png)

## 10. 审核启动，确定没错后点击**启动**
![审核启动](审核.png)

## 11. 创建密钥，填写名称后下载密钥对（一定要保存好），点击**启动实例**
![创建密钥](密钥.png)

## 12. 实例正在启动
![启动状态](状态.png)

## 13. 启动完成，查看实例
![查看实例](查看.png)