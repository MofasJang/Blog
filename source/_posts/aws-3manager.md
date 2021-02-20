---
title: 使用AWS Systems Manager在EC2实例上远程运行命令
date: 2021-02-09 14:23:40
tags:
- 云服务
- AWS
- 亚马逊
categories:
- 云服务
- AWS
---
{% note info %}
参考文章：[Remotely run commands EC2 instance systems manager](https://aws.amazon.com/cn/getting-started/hands-on/remotely-run-commands-ec2-instance-systems-manager/)
{% endnote %}
# 简介
* 本文介绍如何使用 AWS Systems Manager 在 Amazon EC2 实例上远程运行命令。Systems Manager 是一种管理工具，可以用它来了解 AWS 资源的运行状况，并以安全且规模化的方式对 AWS 资源采取措施。使用 Systems Manager 的一种自动化功能 Run Command 时，无需使用堡垒主机、SSH 或远程 PowerShell，从而可以简化管理任务。
* 为解决团队不允许通过 SSH 或使用堡垒主机直接访问生产服务器的问题，我们将创建 Identity and Access Management (IAM) 角色，在实例上启动一个与 Systems Manager 通信的代理，然后遵循通过运行 AWS-UpdateSSMAgent 文档升级 Systems Manager 代理的最佳实践，最后使用 Systems Manager 在实例上运行命令。
<!-- more -->

# 一、创建Identity and Access Management(IAM)角色
## 1. 打开[IAM控制台](https://console.aws.amazon.com/iam/)
![IAM控制台](1a.png)

## 2. 从左侧导航窗格中选择**Roles**（角色），然后选择**Create role**（创建角色）。
![创建角色](1b.png)

## 3. 在选择受信任实体的类型页面上，在**AWS 服务**下选择**EC2**，然后选择**下一步: 权限**。
![受信任实体](1c.png)

## 4. 在**Attached permissions policy**（附加的权限策略）页面上的搜索栏中输入 AmazonEC2RoleforSSM，然后从策略列表中选择**AmazonEC2RoleforSSM**，然后选择**Next: Review**（下一步：审核）。
![AmazonEC2RoleforSSM](1d.png)

## 5. 在**Review**（审核）页面上的**Role name**（角色名称）框中键入 EnablesEC2ToAccessSystemsManagerRole。在**Role description**（角色描述）框中键入 Enables an EC2 instance to access Systems Manager。选择**Create role**（创建角色）。
![角色描述](1e.png)

# 二、创建EC2实例
此过程在[AWS服务的注册和创建主机](http://www.panjiangtao.cn/posts/aws-1register/)已介绍，在此不加赘述。

# 三、更新 Systems Manager 代理
## 1. 单击顶部菜单中的**Services**（服务）。然后在“Management Tools”（管理工具）下，选择 Systems Manager 以打开 Systems Manager 控制台。
![Systems Manager 控制台](3a.png)

## 2. 选择左侧导航栏中**Shared Resources**（共享资源）下方的**Managed Instances**（托管实例）。
![托管实例](3b.png)

## 3. 在托管实例页面上，选择**Actions**（操作）下拉菜单中的**Run Command**（运行命令）。
![运行命令](3c.png)

## 4. 
* 在**Run a command**（运行命令）页面上的搜索栏中单击并选择**Document name prefix**（文档名称前缀），然后单击**Equal**（等于），并键入 AWS-UpdateSSMAgent。
* 现在单击**AWS-UpdateSSMAgent** 左边的单选按钮。本文档将更新实例上的系统管理代理。
* 向下滚动至**目标**面板，并单击您的托管式 Amazon EC2 实例旁的复选框。
* 最后，向下滚动并选择**运行**。

![AWS-UpdateSSMAgent](3d.png)

## 5. 接下来您将看到页面上记录了正在运行的命令，且整体状态以绿色显示为“Success”（成功）。
![成功](3e.png)

# 四、运行 Remote Shell 脚本
## 1. 从**系统管理**控制台中，选择左侧导航栏中**Shared Resources**（共享资源）下方的**Managed instances**（托管实例）。然后在**Actions**（操作）菜单中选择**Run Command**（运行命令） 菜单项。 
![运行命令](4a.png)

## 2. 
* 在**Run a command**（运行命令）页面上的搜索栏中单击并选择**Document name prefix**（文档名称前缀），然后单击**Equal**（等于），并键入 AWS-RunShellScript。
* 现在单击**AWS-RunShellScript** 左边的单选按钮。本文档将更新实例上的系统管理代理。
* 向下滚动至**目标**面板，并单击您的托管式 Amazon EC2 实例旁的复选框。

![更新实例上的系统管理代理](4b.png)

## 3. 
* 向下滚动至**Command Parameters**（命令参数）面板，并在**Commands**（命令）文本框中插入以下命令：
`sudo yum update -y`
* 最后，向下滚动并选择**运行**。

![命令参数](4c.png)

## 4. 当您的脚本在托管式 Amazon EC2 实例上远程运行时，**整体状态**显示为**正在进行**。不久后，**整体状态**将变成“成功”。届时，向下滚动至**Targets and outputs**（目标和输出）面板并单击实例的实例 ID。您的实例 ID 将与图片上的不同。
![成功](4d.png)

## 5. 在**Output on: i-XX** 页面中，单击**Step 1 - Output**（第 1 步 - 输出）面板的标头，查看来自实例的更新命令输出。
![查看](4e.png)
