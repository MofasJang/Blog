---
title: Spring中的Bean
copyright: false
date: 2023-03-04 21:05:30
tags:
- Spring
- Java
categories:
- backend
- Spring
---
## Bean的基本配置

1. bean基础配置

![bean基础配置](http://pic.panjiangtao.cn/img/image-20230227175408721.png)
<!-- more -->
1. 别名

```xml
	<!--bean中name属性代表别名,用逗号、分号、空格分割-->
    <bean id="bookService" name="service" class="com.pjt.service.impl.BookServiceImpl">
```

3. 创建非单例对象，默认为**单例**

```xml
<--scope控制创建对象是否为单例，prototype为非单例-->
<bean id="bookDao" name="dao" class="com.pjt.dao.impl.BookDaoImpl" scope="prototype"/>
```

- 适合交给容器进行管理的bean
  - 表现层对象
  - 业务层对象
  - 数据层对象
  - 工具对象
- 不适合交给容器进行管理的bean
  - 封装实体的域对象

## Bean实例化

### 1.构造方法

#### 在定义类时提供可访问的构造方法

```java
public class BookDaoImpl implements BookDao{
    public BookDaoImpl(){
        System.out.println("book dao constructor is running");
    }
	...
}
```

- 创建Bean时调用**无参**构造方法。
- 无参构造方法不存在时，将抛出异常BeanCreationException

#### 配置

```xml
<bean id="bookDao" class="com.pjt.dao.impl.BookDaoImpl" />
```

### 2.静态工厂

#### 工厂类实现

```java
public class OrderDaoFactory {
    public static OrderDao getOrderDao(){
        return new OrderDaoImpl();
    }
}
```

#### 配置

```xml
<bean id="orderDao" class="com.pjt.factory.OrderDaoFactory" factory-method="getOrderDao"/> 
```

- class为工厂类名
- factory-method实例化方法

### 3.实例工厂

#### 工厂类实现

```java
public class UserDaoFactory {
    public UserDao getUserDao(){
        return new UserDaoImpl();
    }
}
```

#### 配置

```xml
<bean id="userFactory" class="com.pjt.factory.UserDaoFactory" />
<bean id="userDao" factory-method="getUserDao" factory-bean="userFactory"/>
```

- factory-bean指使用该工厂类进行实例化

### 4.FactoryBean

#### 工厂类实现

```java
public class UserDaoFactoryBean implements FactoryBean<UserDao> {
    //代替原始实例工厂中创建对象的方法
    //得到bean实例
    public UserDao getObject() throws Exception {
        return new UserDaoImpl();
    }
    //得到bean类型
    public Class<?> getObjectType() {
        return UserDao.class;
    }
    //设定bean单例
    public boolean isSingleton() {
        return false;
    }
}
```

#### 配置

```xml
<bean id="userDao" class="com.pjt.factory.UserDaoFactoryBean"/>
```

## Bean生命周期

bean从创建到销毁的整体过程

- 初始化容器
  1. 创建对象(内存分配 )
  2. 执行构造方法
  3. 执行属性注入 ( set操作 )
  4. **执行bean初始化方法**
- 使用bean
  1. 执行业务操作
- 关闭/销毁容器
  1. **执行bean销毁方法**

### 1.使用函数实现初始化和销毁操作

在实现类中添加init()函数以初始化bean，destroy()函数处理销毁bean前的操作。

```java
public class BookDaoImpl implements BookDao{
    ...
    //表示bean初始化对应的操作
    public void init(){
        System.out.println("init");
    }
    //表示bean销毁前对应的操作
    public void destroy(){
        System.out.println("destroy");
    }
}
```

在配置文件中给bean添加init-method和destroy-method属性。

```xml
<bean id="bookDao" class="com.pjt.dao.impl.BookDaoImpl" init-method="init" destroy-method="destroy"/>
```

### 2.使用spring自带接口

使用InitializingBean，DisposableBean接口实现bean的初始化和销毁，重写`afterPropertiesSet()`函数以在属性设置setBookDao后进行初始化操作，重写`destroy()`函数实现销毁前操作。

```java
public class BookServiceImpl implements BookService, InitializingBean, DisposableBean {
	...
    public void setBookDao(BookDao bookDao){
    }
    
    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("service init");
    }
    @Override
    public void destroy() throws Exception {
        System.out.println("service destroy");
    }
}
```

### 3.bean的销毁时机

容器关闭前触发bean的销毁

关闭容器方式：

- 手动关闭

`ClassPathXmlApplicationContext`接口`close()`操作

```java
ClassPathXmlApplicationContext act = new ClassPathXmlApplicationContext("applicationContext.xml");
//申请关闭钩子
act.registerShutdownHook();
...
//直接关闭容器
act.close();
```

- 注册关闭钩子

`ClassPathXmlApplicationContext`接口`registerShutdownHook()`操作

