---
title: Spring中IoC入门案例
copyright: false
date: 2023-03-04 21:05:07
tags:
- Spring
- Java
categories:
- backend
- Spring
---
### 1. 导入spring包

在pom.xml配置文件中的`<dependencies>`模块中加入下述代码以导入spring包：

```xml
 <dependency>
     <groupId>org.springframework</groupId>
     <artifactId>spring-context</artifactId>
     <version>5.2.9.RELEASE</version>
</dependency>
```
<!-- more -->
### 2.新建配置文件

右键resources新建spring xml文件，命名为applicationContext.xml。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--1.导入spring的坐标spring-context-->

    <!--2.配置bean-->
    <!--bean标签表示配置bean
    id属性给bean起名字
    class属性给bean定义类型-->
    <bean id="bookDao" class="com.pjt.dao.impl.BookDaoImpl"/>

    <bean id="bookService" class="com.pjt.service.impl.BookServiceImpl"/>
</beans>
```

### 3.定义spring管理的类（接口）

```java
public interface BookService {
    void save();
}
```

```java
public class BookServiceImpl implements BookService{
    private BookDao bookDao = new BookDaoImpl();
    public void save(){
        System.out.println("book service save ...");
        bookDao.save();
    }
```



### 4.使用bean

```java
public class app2 {

    public static void main(String[] args) {
        //3.获取IoC容器
        ApplicationContext act = new ClassPathXmlApplicationContext("applicationContext.xml");
        //4.获取bean,需要强转为接口类
        BookDao bookdao = (BookDao) act.getBean("bookDao");
        bookdao.save();
    }
}
```

