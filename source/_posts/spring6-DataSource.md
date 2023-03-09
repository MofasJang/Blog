---
title: Spring数据源对象管理案例——导入数据库连接池插件gruid
copyright: false
date: 2023-03-04 21:05:41
tags:
- Spring
- Java
categories:
- backend
- Spring
---
### 使用xml文件配置
<!-- more -->
#### 1.在pom.xml文件中导入gruid坐标

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.16</version>
</dependency>
```

##### 2.配置数据源对象作为spring管理的bean

```xml
<bean id="DataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/spring_db"/>
    <property name="username" value="root"/>
    <property name="password" value="***"/>
</bean>
```

##### 3.获取bean并使用

```java
ApplicationContext act = new ClassPathXmlApplicationContext("applicationContext.xml");
DataSource dataSource = (DataSource) act.getBean("DataSource");
Connection connection = dataSource.getConnection();
System.out.println(connection);
```

### 使用properties文件配置

#### 1.开启context命名空间

一共修改5处

```xml
<beans xmlns="http://www.springframework.org/schema/beans"<!--修改此行-->
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"<!--开启context命名空间-->
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
    ">
```

#### 2.使用context空间加载properties文件

```xml
<context:property-placeholder location="jdbc.properties,jdbc2.properties" system-properties-mode="NEVER"/>
<!--加载项目下的所有properties文件-->
<context:property-placeholder location="classpath:*.properties" system-properties-mode="NEVER"/>
<!--从类路径或jar包搜索或加载所有properties文件-->
<context:property-placeholder location="classpath*:*.properties" system-properties-mode="NEVER"/>
```

`location`为配置文件名，多个文件用','分割，可以使用通配符。

`system-properties-mode`为never时不加载系统配置文件。

#### 3.使用`${}`读取加载的属性值

```xml
<property name="driverClassName" value="${jdbc.driver}"/>
```

