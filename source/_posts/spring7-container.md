---
title: Spring中的容器
copyright: false
date: 2023-03-04 21:05:42
tags:
- Spring
- Java
categories:
- backend
- Spring
---
### 创建容器

* 方式一：类路径加载配置文件

```java
ApplicationContext act = new ClassPathXmlApplicationContext("applicationContext.xml");
```
<!-- more -->
* 方式二：文件路径加载配置文件

```java
ApplicationContext act2 = new FileSystemXmlApplicationContext("F:\\projects\\Spring2_base_config\\src\\main\\resources\\applicationContext.xml")
```

* 加载多个配置文件

```java
ApplicationContext act = new ClassPathXmlApplicationContext("bean1.xml","bean2.xml");
```

### 获取bean

* 方式一：使用bean名称获取

```java
BookDao bookDao = (BookDao) act.getBean("bookDao");
```

* 方式二：使用bean名称获取并指定类型

```java
BookDao bookDao = act.getBean("bookDao", BookDao.class);
```

* 方式三：使用bean类型获取（容器中只能有一个该类型的bean）

```java
BookDao bookDao = act.getBean(BookDao.class);
```

### 复古方法

```java
//创建容器
Resource resources = new ClassPathResource("applicationContext.xml");
BeanFactory bf = new XmlBeanFactery(resources);
//获取bean
BookDao bookDao = bf.getBean(BookDao.class);
```

该方法为懒加载，`ApplicationContext`为立即加载。

当在配置文件的bean标签中加入`<lazy-init>`标签也可实现懒加载。

```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl" lazy-init="true"/>
```

## 容器类层次结构图

![容器类层次结构图](http://pic.panjiangtao.cn/img/image-20230307171226004.png)

- BeanFactory是IoC容器的顶层接口，初始化BeanFactory对象时，加载的bean延迟加载
- ApplicationContext接口是Spring容器的核心接口，初始化时bean立即加载
- ApplicationContext接口提供基础的bean操作相关方法，通过其他接口扩展其功能
- ApplicationContext接口常用初始化类
  - ClassPathXmlApplicationContext
  - FileSystemXmlApplicationContext

