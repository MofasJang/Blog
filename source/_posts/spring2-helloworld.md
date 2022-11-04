---
title: Spring Boot之helloworld(环境配置)
copyright: false
date: 2021-07-03 13:31:45
tags: spring
categories:
- for babe
- spring
---
{% note info %}
这一节讲的是使用IDEA工具进行Spring Boot搭建的方法
{% endnote %}
<!-- more -->
# 一、安装Maven
## 下载
　　1. 点击[Maven下载地址](https://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.8.1/binaries/apache-maven-3.8.1-bin.zip)进行下载
　　2. 下载、解压完，在环境变量path中添加apache-maven-3.8.1\bin文件夹
　　3. 命令行输入`mvn -v`检查是否正确安装

## maven环境配置
　　在apache-maven-3.8.1\conf文件夹中找到settings.xml文件，其中任意位置插入下述代码：
```xml
<mirrors>
      <mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>central</mirrorOf>
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
      </mirror>
  </mirrors>
 
  <profiles>
         <profile>
              <id>jdk-1.8</id>
              <activation>
                <activeByDefault>true</activeByDefault>
                <jdk>1.8</jdk>
              </activation>
              <properties>
                <maven.compiler.source>1.8</maven.compiler.source>
                <maven.compiler.target>1.8</maven.compiler.target>
                <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
              </properties>
         </profile>
  </profiles>
  ```

# 二、IDEA配置
## 新建项目
![新建maven工程](1.jpg)
![给项目取名，再取一个组名，比如com.lyt](2.jpg)
## 引入依赖
{% note warning %}
在打开IDEA时右下角会跳出弹框，选择always download，否则不会自动下载依赖哦贝贝。
{% endnote %}
　　在pom.xml文件中加入下述代码：
```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
    </parent>


    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

    </dependencies>
```

* 如果左边External Libraries没有下面这些包，就在pom.xml里右键Maven->Reload Project
![导入依赖后的各种包](4.jpg)

## 编写主程序
　　1. 在main/java下新建class，类名可以像下面这样com.pjt.boot.MainApplication
　　![新建主程序](3.jpg)
　　2. 在主程序函数上添加`@SpringBootApplication`,表示这是一个SpringBoot应用。
　　3. 主程序完整代码：
```java
package com.pjt.boot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MainApplication {
    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class,args);
    }
}
```

## 编写控制器
　　1. 在com.pjt.boot下创建controller
![创建controller控制器](5.jpg)
　　2. 在函数HelloController上方添加`@RestController`
　　3. 控制器代码：
```java
package com.pjt.boot.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {
    @RequestMapping("/hello")
    public String handle01(){
        return "Hello World!";
    }
}
```

# 三、运行项目
## 添加配置文件
　　在resources文件夹下添加file，名为`application.properties`
![添加配置文件](6.jpg)
```java
server.port=8080 //默认端口为8080，可以修改
```
## 运行Main函数
　　在浏览器中输入`localhost:8080/hello`,即可获得下图结果。
![Hello World!!!](7.jpg)
