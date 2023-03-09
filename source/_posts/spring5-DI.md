---
title: Spring依赖注入
copyright: false
date: 2023-03-04 21:05:40
tags:
- Spring
- Java
categories:
- backend
- Spring
---
注入方法：

- setter注入

  - 简单类型
  - 引用类型
- 构造器注入

  - 简单类型
  - 引用类型
<!-- more -->
### 1.setter注入

在bean的**实现类**中定义简单类型和引用类型属性并提供可访问**set**方法。

```java
//简单类型
public class BookDaoImpl implements BookDao{
    private int connectionNum;
    public void setConnectionNum(int connectionNum) {
        this.connectionNum = connectionNum;
    }
}
//引用类型
public class BookServiceImpl implements BookService{
    private UserDao userDao;
    public void setUserDao(UserDao userDao){
        this.userDao = userDao;
    }
}
```

在**配置文件**中使用property标签的value属性注入简单类型，使用ref属性注入引用类型。

```xml
<!--setter注入简单类型-->
<bean id="bookDao" class="com.pjt.dao.impl.BookDaoImpl">
	<property name="connectionNum" value="10"/>
</bean>
<!--setter注入引用类型-->
<bean id="bookService" class="com.pjt.service.impl.BookServiceImpl">
    <property name="userDao" ref="userDao"/>
</bean>
<!--setter注入集合类型-->
<bean id="bookDao" class="com.pjt.dao.impl.BookDaoImpl">
	<property name="array">
        <array><!--array,list,set-->
            <value>100</value>
            ...
        </array>
    </property>
    <property name="map">
        <map>
            <entry key="country" value="China"/> 
            ...
        </map>
    </property>
    <property name="properties">
        <props>
            <prop key="country">China</prop>
            ...
        </props>
    </property>
</bean>
```

### 2.构造器注入

在**实现类**中定义构造方法

```java
	//简单类型构造方法
	public BookDaoImpl(int connectionNum, String databaseName) {
        this.connectionNum = connectionNum;
        this.databaseName = databaseName;
    }
	//引用类型构造方法
    public BookServiceImpl(BookDao bookDao, UserDao userDao) {
        this.bookDao = bookDao;
        this.userDao = userDao;
    }
```

**配置文件**

```xml
<!--构造器注入简单类型-->
<bean id="bookDao" class="com.pjt.dao.impl.BookDaoImpl">
	<constructor-arg name="connectionNum" value="666"/>
	<constructor-arg name="databaseName" value="mysql"/>
</bean>
<!--构造器注入引用类型-->
<bean id="bookService" class="com.pjt.service.impl.BookServiceImpl">
    <constructor-arg name="bookDao" ref="bookDao"/>
    <constructor-arg name="userDao" ref="userDao"/>
</bean>
```

解决耦合问题：

```xml
<bean id="bookDao" class="com.pjt.dao.impl.BookDaoImpl">
    <!--按类型标注-->
   	<constructor-arg type="int" value="666"/>
	<constructor-arg type="java.lang.String" value="mysql"/>
    <!--按位置标注-->
   	<constructor-arg index="1" value="666"/>
	<constructor-arg index="0" value="mysql"/>
</bean>
```

### 依赖注入方式选择

1. **强制依赖**（运行bean必要的组件）使用构造器进行，使用setter注入有概率不进行注入导致null对象出现
2. 可选依赖使用setter注入进行，灵活性强
3. Spring框架倡导使用**构造器**，第三方框架内部大多数采用构造器注入的形式进行数据初始化，相对严谨
4. 如果有必要可以两者同时使用，使用构造器注入完成强制依赖的注入，使用setter注入完成可选依赖的注入
5. 实际开发过程中还要根据实际情况分析，如果受控对象没有提供setter方法就必须使用构造器注入
6. **自己开发的模块推荐使用setter注入**

## 依赖自动装配

IoC容器根据bean依赖的资源在容器中自动查找并注入到bean中。

 常用自动装配方式：按类型和按名称

写好setter方法后在配置文件中加入autowire属性，按类型装配时注入的**引用类型名称唯一**，按名称装配时容器中必须有指定名称的bean（不推荐）。**不能对简单类型操作。**优先级低于setter注入和构造器注入。

```xml
<bean id="bookService" class="com.pjt.service.impl.BookServiceImpl" autowire="byType">
<bean id="bookService" class="com.pjt.service.impl.BookServiceImpl" autowire="byName">
```

