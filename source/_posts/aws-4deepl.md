---
title: 使用AWS Deep Learning Containers在Amazon EC2上训练深度学习模型
date: 2021-02-09 16:03:57
tags:
- 云服务
- AWS
- 亚马逊
- 深度学习
categories:
- 云服务
- AWS
---
{% note info %}
参考文章：[Train deep learning model AWS EC2 containers](https://aws.amazon.com/cn/getting-started/hands-on/train-deep-learning-model-aws-ec2-containers/)
{% endnote %}
# 简介
* AWS Deep Learning Containers (DL Containers)是预先安装了深度学习框架的 Docker 镜像，可以跳过从头构建和优化环境的复杂流程，轻松快速部署自定义机器学习环境。
* 利用AWS DL Containers，开发人员和数据科学家可以为其部署在 Amazon Elastic Container Service for Kubernetes (Amazon EKS)、自行管理的Kubernetes、Amazon Elastic Container Service (Amazon ECS) 和 Amazon EC2 上的容器化应用程序添加机器学习功能。
* 本文中我们将使用 AWS Deep Learning Containers 在 Amazon EC2 实例上训练一个 TensorFlow 机器学习模型。由于还未学习TensorFlow 机器学习模型的建立和使用，故文中使用AWS官方给出的样例。
<!-- more -->

# 一、添加访问Amazon ECR的权限
## 1. 导航到 IAM 控制台
打开 AWS 管理控制台，以便使本分步指南处于打开状态。此屏幕加载后，请输入用户名和密码以便开始操作。在搜索栏中输入 IAM，然后选择 **IAM** 以便打开服务控制台。
![导航到 IAM 控制台](1a.png)

## 2. 选择用户
在左侧导航窗格中，选择**用户**。
![选择用户](1b.png)

## 3. 添加权限
现在，为所创建的新 IAM 用户或现有 IAM 用户添加权限。在 IAM 用户摘要页面上选择**添加权限**。
![添加权限](1c.png)

## 4. 添加 ECS 完全访问策略
选择**直接附加现有策略**，搜索 ECS_FullAccess。选择 **Amazon_FullAccess** 策略，依次单击**查看**和**添加权限**。
![添加 ECS 完全访问策略](1d.png)

## 5. 添加内联策略
在 IAM 用户摘要页面上选择**添加内联策略**。
![添加内联策略](1e.png)

## 6. 粘贴 JSON 策略
选择 **JSON** 选项卡并粘贴以下策略：
```json
{
       "Version": "2012-10-17",
       "Statement": [
              {
                     "Action": "ecr:*",
                     "Effect": "Allow",
                     "Resource": "*"
              }
       ]
}
```
将此策略另存为 “ECR”，然后选择**创建策略**。
![粘贴 JSON 策略](1f.png)

# 二、启动一个 AWS Deep Learning Base AMI 实例
在本文中，我们将在 AWS Deep Learning Base Amazon Machine Images (AMIs) 上使用 AWS Deep Learning Containers，这些容器预先打包了必要的依赖项，例如 Nvidia 驱动程序、docker 和 nvidia-docker。可以使用这些软件包在任何 AMI 上运行 Deep Learning Containers。
## 1. 导航到EC2控制台
返回到 AWS 管理控制台，在搜索栏中输入 EC2，并选择 **EC2**以打开服务控制台。
![导航到EC2控制台](2a.png)

## 2. 启动一个 Amazon EC2 实例
再次导航到 Amazon EC2 控制台，然后选择 **Launch Instance**（启动实例）按钮。
![启动一个 Amazon EC2 实例](2b.png)

## 3. 选择 AWS Deep Learning Base AMI
选择左侧的 **AWS Marketplace** 选项卡，然后搜索“deep learning base ubuntu”。选择 **Deep Learning Base AMI (Ubuntu)**。还可以选择 **Deep Learning Base AMI (Amazon Linux)**。
![选择 AWS Deep Learning Base AMI](2c.png)

## 4. 选择实例类型
选择 Amazon EC2 实例类型。Amazon Elastic Compute Cloud (EC2) 是用于在云中创建和运行虚拟机的 Amazon Web 服务。AWS 将这些虚拟机称为“实例”。可以选择c5.large或基于GPU 的 P3 实例。

选择**检查和启动**。
![选择实例类型](2d.png)

## 5. 启动实例
查看实例的详细信息，然后选择**启动**。
![启动实例](2e.png)

## 6. 创建一个新的私钥文件
* 在下一个屏幕中，系统会要求选择现有密钥对或创建新的密钥对。可以使用密钥对通过 SSH 安全访问实例。AWS 会存储密钥对的公有部分，该部分密钥的作用就像门锁。需下载并使用密钥对的私有部分，该部分密钥的作用就像门锁钥匙。

* 选择**创建新密钥对**，并为其命名。然后选择**下载密钥对**，并将密钥存储在安全的位置。如果丢失密钥，就无法访问该实例。如果他人获得密钥，他们就能够访问该实例。

* 如果以前创建了私钥文件，并且现在仍可访问，则可以选择选择**现有的密钥对**，使用现有私钥。

![创建一个新的私钥文件](2f.png)

## 7. 查看实例详情
选择**实例ID**，在控制台上查看新创建的 Amazon EC2 的详细信息。 
![查看实例详情](2g.png)

# 三、连接到实例
在此步骤中，将使用 SSH 连接到新启动的实例。以下说明使用 Mac/Linux 环境。如果使用的是 Windows，请按照[教程](https://aws.amazon.com/cn/getting-started/hands-on/launch-a-virtual-machine/)完成。
## 1. 找到实例的公共 DNS 并复制
在**说明**选项卡下，复制 Amazon EC2 实例的公共 DNS (IPv4)。
![找到实例的公共 DNS 并复制](3a.png)

## 2. 打开命令行终端
在终端中使用以下命令更改为安全密钥所在的目录，然后使用 SSH 连接实例。
```javascript
cd /Users/<your_username>/Downloads/

chmod 0400 <your .pem filename>

ssh -L localhost:8888:localhost:8888 -i <your .pem filename> ubuntu@<your instance DNS>
```
![打开命令行终端](3b.png)

# 四、登录到 Amazon ECR
AWS Deep Learning Container 映像托管在 Amazon Elastic Container Registry (ECR) 上，这是一个完全托管的 Docker 容器注册表，让开发人员可以轻松存储、管理和部署 Docker 容器映像。在此步骤中，将登录并验证能否正常访问 Amazon ECR。

## 1. 使用 AWS 凭证配置 EC2 实例
需要提供AWS 访问密钥 ID 和秘密访问密钥。如果还没有这些信息，则可以在[此处](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html#Using_CreateAccessKey)创建访问密钥 ID 和秘密访问密钥。
![使用 AWS 凭证配置 EC2 实例](4a.png)

## 2. 登录到 Amazon ECR
使用以下命令登录到 Amazon ECR：
```javascript
$(aws ecr get-login --region us-east-1 --no-include-email --registry-ids 763104351884)
```
**注意**：需要在命令中包含“$”和圆括号。完成此步骤后，将看到“登录成功”。
![登录到 Amazon ECR](4b.png)

# 五、使用 Deep Learning Containers 运行 TensorFlow 训练
在此步骤中，我们将使用 AWS Deep Learning Container 映像，通过 Python 3.6 在 CPU 实例上进行 TensorFlow 训练。
## 1.运行 AWS Deep Learning Containers
现在，使用以下命令在 EC2 实例上运行 AWS Deep Learning Container 映像。如果 Deep Learning Container 映像在本地不存在，则此命令将自动提取该映像。
```javascript
docker run -it 763104351884.dkr.ecr.us-east-1.amazonaws.com/tensorflow-training:1.13-cpu-py36-ubuntu16.04
```
**注意**：此步骤可能需要几分钟，具体取决于映像的大小。如果使用的是 GPU 实例，请使用“nvidia-docker”而非“docker”。 成功完成此步骤后，将为容器进入一个 bash 提示符界面。
![运行 AWS Deep Learning Containers](5a.png)


## 2.拉取一个示例模型进行训练
我们将克隆 Keras 存储库，其中包括用于训练模型的示例 Python 脚本。
```javascript
git clone https://github.com/fchollet/keras.git
```
![拉取一个示例模型进行训练](5b.png)

## 3. 开始训练
使用以下命令开始训练规范的 MNIST CNN 模型：
```javascript
python keras/examples/mnist_cnn.py
```
已经成功使用 AWS Deep Learning Container 开始了训练。
![开始训练](5c.png)


# 六、终止资源
在此步骤中，将终止在本教程中创建的 Amazon EC2 实例。

**注意**：终止当前未在使用的资源可降低成本，这是最佳实践。不终止资源可能会在账户下产生费用。
## 1.选择正在运行的实例
在 Amazon EC2 控制台上，选择**正在运行的实例**。
![选择正在运行的实例](6a.png)

## 2.终止EC2实例
选择之前创建的 EC2 实例，再选择**操作 > 实例状态 > 终止**。
![终止EC2实例](6b.png)

## 3. 确认终止
系统会提示确认终止。选择**是，终止**。

**注意**：完成此过程可能需要几秒钟。终止实例后，EC2 控制台上的“实例状态”会变为“已终止”。
![确认终止](6c.png)

