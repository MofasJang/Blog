---
title: Spring的注解开发
copyright: false
date: 2023-03-04 21:05:42
tags:
- Spring
- Java
categories:
- backend
- Spring
---
### 注解开发定义bean

- 使用`@Component`定义bean

```java
@Component("bookDao")//标注bean名称，可按名称加载bean
public class BookDaoImpl implements BookDao{
}
@Component//不标注bean名称，可按类型加载bean
public class BookServiceImpl implements BookService{
}
```
<!-- more -->
- 核心配置文件中通过组件扫描加载bean，搜索com.pjt目录下的全部bean

```xml
<context:component-scan base-package="com.pjt"/>
```

- Spring提供`@Component`注解的三个衍生注解
  - `@Controller`:用于表现层bean定义
  - `@Service`:用于业务层bean定义
  - `@Repository`: 用于数据层bean定义

### 纯注解开发

- 用Java类代替spring核心配置文件

```java
@Configuration//设定当前类为配置类
@ComponentScan("com.pjt")//设定扫描路径
//@ComponentScan({"com.pjt.dao","com.pjt.service"})//多路径扫描用数组
public class SpringConfig {
}
```

- 从读取Spring核心**配置文件**初始化容器对象 切换为 读取**Java配置类**初始化容器对象

```java
//加载配置文件初始化容器
ApplicationContext ctx = new ClassPathXmlApplicationContext( "applicationContext.xml" );
//加载配置类初始化容器
ApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
```

### bean管理

#### bean作用范围

- `@Scope`定义bean作用范围

```java
@Repository//该bean为数据层bean定义
//@Scope("prototype")//非单例
@Scope("singleton")//单例
public class BookDaoImpl implements BookDao{
}
```

#### bean生命周期

- 使用`@PostConstruct`、`@PreDestroy`定义bean生命周期

```java
@R=epository
@Scope("singleton")//单例
public class BookDaoImpl implements BookDao{
    public void save(){
        System.out.println("book dao save ...");
    }
    @PostConstruct//初始化（构造方法后运行）
    public void init(){
        System.out.println("init ...");
    }
    @PreDestroy//销毁（销毁前运行）
    public void destroy(){
        System.out.println("destroy ...");
    }
}
```

### 依赖注入

#### 注入引用类型

- 使用`@Autowired`注解开启自动装配模式(按类型)

```java
@Service
public class BookServiceImpl implements BookService{
    @Autowired
    @Qualifier("bookDao2")//按名称装配bean
    private BookDao bookDao;
    
    public void save(){
        System.out.println("book service save ...");
        bookDao.save();
    }
}
```

##### 注意

- 自动装配基于反射设计创建对象并暴力反射对应属性为私有属性初始化数据，因此**无需提供setter方法**
- 自动装配建议使用**无参构造方法**创建对象（默认），如果不提供对应构造方法，请提供唯一的构造方法
- `@Qualifier`注解**无法单独使用**，必须配合`@Autowired`注解使用

#### 注入简单类型

##### 使用`@Value`注释注入

```java
@Value("pjt")
private String name;
```

##### 使用`@PropertySource`注解加载properties文件

在config类中添加属性文件

```java
@Configuration
@ComponentScan("com.pjt")
@PropertySource({"classpath:application.properties","j.properties"})
public class SpringConfig {
}
```

注意︰路径仅支持单一文件配置，多文件请使用数组格式配置，**不允许使用通配符***

### 第三方bean管理

- 使用独立配置管理第三方bean

```java
public class JdbcConfig {
    //1.定义一个方法获得要管理的对象
    //2.添加@bean表示当前方法的返回值是一个bean
    @Bean
    public DataSource dataSource(){
        DruidDataSource ds = new DruidDataSource();
        ds.setDriverClassName("com.mysql.jdbc.Driver");
        ds.setUrl("jdbc:mysql://localhost:3306/spring_db");
        ds.setUsername("root");
        ds.setPassword("***");
        return ds;
    }
}
```

#### 导入式加入核心配置

- 使用`@Import`注解手动加入配置类到核心配置，此注解只能添加一次，多个数据请用数组格式

```java
@Configuration
@Import({JdbcConfig.class})//按类型导入配置
public class SpringConfig {
}
```

#### 扫描式加入核心配置

在独立配置类前增加`@Configuration`注解

```java
@Configuration
public class JdbcConfig {
    @Bean
    public DataSource dataSource(){
        DruidDataSource ds = new DruidDataSource();
		...
        return ds;
    }
}
```

使用`@ComponentScan`注解扫描配置类所在的包，加载对应的配置类信息

```java
@Configuration
@ComponentScan("com.pjt.config")//扫描整个config包导入bean
public class SpringConfig {
}
```

### 第三方bean依赖注入

#### 简单类型依赖注入

使用 `@Value`注释注入

```java
public class JdbcConfig {
   
    @Value("com.mysql.jdbc.Driver")
    private String driver;
    @Value("jdbc:mysql://localhost:3306/spring_db")
    private String url;
    @Value("root")
    private String userName;
    @Value("***")
    private String password;
    @Bean
    public DataSource dataSource(){
        DruidDataSource ds = new DruidDataSource();
        ds.setDriverClassName(driver);
        ds.setUrl(url);
        ds.setUsername(userName);
        ds.setPassword(password);
        return ds;
    }
}
```

#### 引用类型依赖注入

自动装配：引用类型注入只需要为bean定义方法设置形参即可，容器会根据类型自动装配对象

```java
@Bean
public DataSource dataSource(BookDao bookDao){
    System.out.println(bookDao);
    DruidDataSource ds = new DruidDataSource();
    ...
    return ds;
}
```

### 总结

XML配置对比注解配置

![XML配置对比注解配置](http://pic.panjiangtao.cn/img/image-20230309170558849.png)