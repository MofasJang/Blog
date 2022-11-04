---
title: Spring Boot中的容器与自动配置
copyright: false
date: 2021-07-07 10:22:05
tags: spring
categories:
- for babe
- spring
---
# 一、依赖管理与自动配置
## 导入父项目进行版本配置
　　只需导入父项目spring-boot-starter-parent及其版本号2.3.4.RELEASE就可以实现对需要的包进行版本控制。
<!-- more -->
```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
    </parent>
```

## 导入需要的starter场景启动器
　　1. spring-boot-starter-\*中的\*指某种场景。
   
　　2. \*-spring-boot-starter-\*是第三方为我们提供的简化开发的场景启动器。
   
　　3. spring-boot-starter-web中包含了Tomcat启动器和webmvc，不需要再次引入。
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-*</artifactId>
    </dependency>
</dependencies>
```

## 修改某个版本号
　　在项目的pom.xml文件中重写某个包的版本,如将mysql版本设定为5.1.43：
```xml
    <properties>
        <mysql.version>5.1.43</mysql.version>
    </properties>
```

## 修改扫描路径
　　程序默认只扫描主程序所在包及其子包，需要改变扫描路径，下述两种方法等同

```java
//第一种，简单方便
//不加括号为默认扫描，加括号为指定路径
@SpringBootApplication(scanBasePackages="com.pjt") 

//第二种
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan("com.pjt.boot") //括号内为需要扫描的根目录
```

# 二、容器
## 组件添加
### 1. @Configuration
#### 配置类
　　
　　在boot目录下添加config.MyConfig类作为配置类，代码如下,其中，[Pet和User](http://www.panjiangtao.cn/posts/spring3-1-pet-user/)为两个不同的类：
```java
@Configuration(proxyBeanMethods = true) //声明这是一个配置类
public class MyConfig {

    @Bean //给容器中添加组件(也就是加入一个对象)，下述方法名user01默认为组件id，
        //返回类型为组件类型，返回的对象为组件在容器中实例
    public User user01(){
       return new User("zhangsan",18);
    }
    //测试组件依赖时替换
    // public User user01(){
    //     User zhangsan=new User("zhangsan",18);
    //     zhangsan.setPet(tomcatPet());
    //     return zhangsan;
    // }

    @Bean("Tom") //加参数修改组件id为Tom
    public Pet tomcatPet(){
        return new Pet("tomcat");
    }
}
```
　　注意啦！
- 1. 在配置类中使用@Bean标注在方法上给容器注册组件，组件默认单实例
- 2. 配置类本身也是组件
- 3. proxyBeanMethods：指bean的代理对象调用方法。为true时使用配置类调用组件时只创建单实例，如组件已有实例则不再创建；为false时会创建同一组件的新实例，一般用于组件依赖时。

#### 主程序进行测试
```java
public class MainApplication {
    public static void main(String[] args) {
        //1. 从容器获得组件,多次获取为同一实例
        Pet tom01 = run.getBean("Tom", Pet.class);
        Pet tom02 = run.getBean("Tom", Pet.class);
        System.out.println("组件："+(tom01==tom02));

        //2. 配置类也为组件，获取到的bean对象为代理对象
        MyConfig bean = run.getBean(MyConfig.class);
        System.out.println(bean);

        //3.1 proxyBeanMethods为true时调用多次组件获得相同实例
        User user = bean.user01();
        User user1 = bean.user01();
        System.out.println(user==user1);

        //3.2 proxyBeanMethods为false时调用多次组件时创建新实例
        User user01 = run.getBean("user01", User.class);
        Pet Tom=run.getBean("Tom",Pet.class);
        System.out.println("用户的宠物:"+(user01.getPet()==Tom));
    }
}
```
　
### 2. @Import
　　在主函数目录下的类中使用，加在类定义之前。
```java
//@Import给容器中自动创建这两个类型的组件,默认组件名字为全类名
@Import({User.class, DBHelper.class})
```

### 3. @Conditional
　　写在类名之上对整个类有效，写在方法名之上对该方法有效。

```java
//@ConditionalOnMissingBean(name = "tom") //不存在tom组件时注入组件
public class MyConfig {
    
    @ConditionalOnBean(name="tom22") //存在tom组件时才注入组件,由于此时tom22还未注入，因此user01无法注入
    @Bean
    public User user01(){
        User zhangsan = new User("zhangsan", 18);
        //user组件依赖了Pet组件
        zhangsan.setPet(tomcatPet());
        return zhangsan;
    }

    @Bean("tom22")
    public Pet tomcatPet(){
        return new Pet("tomcat");
    }
}

```
　　主函数测试

```java
public static void main(String[] args) {
        //返回IOC容器
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);

        boolean tom = run.containsBean("tom");
        System.out.println("容器中Tom组件："+tom);

        boolean user01 = run.containsBean("user01");
        System.out.println("容器中user01组件："+user01);

        boolean tom22 = run.containsBean("tom22");
        System.out.println("容器中tom22组件："+tom22);
    }
```

### 4. @ImportResource
　　xml文件中进行组件注入:

```xml
    <bean id="haha" class="com.pjt.boot.bean.User">
        <property name="name" value="zhangsan"></property>
        <property name="age" value="18"></property>
    </bean>

    <bean id="hehe" class="com.pjt.boot.bean.Pet">
        <property name="name" value="tomcat"></property>
    </bean>
```

　　从xml配置文件中读入组件注入过程，已经将组件放入容器:

```java
@ImportResource("classpath:beans.xml")
public class MyConfig {
}
```

### 5.配置绑定
　　使用Java读取到properties文件中的内容，并且把它封装到JavaBean中，以供随时使用。

#### 方法1、@ConfigurationProperties
　　在需要导入类的默认属性值时使用，需要先将类装进容器。
```java
@Component  //必须将组件加入容器中才能拥有以下功能
@ConfigurationProperties(prefix = "mycar")  //需要导入的配置文件中的对象名
public class Car{
    private String brand;
    private Integer price;
    ···
}
```
　　然后在配置文件application.properties中添加属性值：
```js
mycar.brand=BMW
mycar.price=100
```

#### 方法2、@EnableConfigurationProperties
　　在使用第三方包时，无法在其通过@Component标签注入容器，因此使用EnableConfigurationProperties方法。
　　配置文件写法与方法１相同。
　　在配置类前加入下述代码，完成配置绑定。
```java
@EnableConfigurationProperties(Car.class)
//1、开启Car配置绑定功能
//2、把这个Car这个组件自动注册到容器中
public class MyConfig{
}
```
{% note warning %}
注意：该方法需要加入方法1的@ConfigurationProperties(prefix = "mycar")
{% endnote %}

#### 方法3、@EnableConfigurationProperties


