---
title: Spring的系统架构
copyright: false
date: 2023-03-04 21:04:31
tags:
- Spring
- Java
categories:
- backend
- Spring
---
## 1.系统架构

<img src="http://pic.panjiangtao.cn/img/image-20230227102613658.png" alt="image-20230227102613658" style="zoom: 50%;" />
<!-- more -->
- ②Data Access：数据访问
- ②Data Integration：数据集成（整合数据层技术MyBatis）
- ④Transaction：事务控制
- Web：Web开发
- ③AOP:面向切面编程，不动源码增加功能   
- ③Aspects：AOP思想的实现

- ①**Core Container**：核心容器，装对象（核心概念为IoC/DI）
- Test：单元测试和集成测试

## 2.核心概念

### IoC(Inversion of Control) 控制反转

使用对象时不new，而是**由外部提供对象**，将对象**创建控制权**由程序转移到外部。

#### 实现方法

- 使用**IoC容器**充当上面思想中的**外部**，控制大量对象

- **IoC容器**负责对象的创建和初始化，被创建的对象统称**Bean**

### DI(Dependency Injection) 依赖注入

在容器中建立Bean与Bean直接的依赖关系的整个过程

### 实现目标：充分解耦

- 使用IoC容器管理Bean（IoC）
- 在IoC容器内绑定有依赖关系的Bean（DI）

### 实现效果

使用对象时从IoC容器中获取，并且获取到的Bean已绑定依赖关系