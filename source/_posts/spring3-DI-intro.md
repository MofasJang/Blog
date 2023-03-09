---
title: Spring中DI入门案例
copyright: false
date: 2023-03-04 21:05:21
tags:
- Spring
- Java
categories:
- backend
- Spring
---
### 思路分析：

1. 基于Ioc管理bean
2. Service中使用new形式创建的Dao对象是否保留?(否)
3. Service中需要的Dao对象如何进入到Service中?(提供方法)
4. 服务与道间的关系如何描述?(配置)
<!-- more -->
### 过程

1. 删除业务层中使用new方式创建的dao对象

```java
//private BookDao bookDao = new BookDaoImpl();
private BookDao bookDao;
```

2. 提供对应的set方法，以创建Dao对象

```java
public void setBookDao(BookDao bookDao){
    this.bookDao = bookDao;
}
```

3. 在配置文件中添加依赖

```xml
<bean id="bookDao" class="com.pjt.dao.impl.BookDaoImpl"/>

<bean id="bookService" class="com.pjt.service.impl.BookServiceImpl">
    <!--7.配置server与dao的关系-->
    <!--property标签表示配置当前bean的属性
    name属性表示配置哪一个具体属性
    ref属性表示参照哪一个bean-->
    <property name="bookDao" ref="bookDao"/>
</bean>
```

