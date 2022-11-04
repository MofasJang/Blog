---
title: spring3.1-pet-user
copyright: false
date: 2021-07-07 15:31:54
tags: spring
categories:
- for babe
- spring
---
# Pet类
<!-- more -->
```java
public class Pet {
    private String name;

    public Pet(){}

    public Pet(String name) {
        this.name=name;
    }

    public String getName() {return name;}

    public void setName(String name) {this.name=name;}

    @Override
    public String toString(){
        return "Pet{"+"name='"+name+'\''+'}';
    }
}
```

# User类
- 注释部分为后续User类需要依赖Pet类
  
```java
public class User {
    private String name;
    private Integer age;

    // private Pet pet;

    // public Pet getPet(){
    //     return pet;
    // }

    // public void setPet(Pet pet){
    //     this.pet=pet;
    // }

    public User(){
    }

    public User(String name, Integer age){
        this.name=name;
        this.age=age;
    }

    public String getName() {return name;}

    public void setName(String name) {this.name=name;}

    public Integer getAge() {return age;}

    public void setAge(Integer age) {this.age=age;}

    @Override
    public String toString(){
        return "User{"+"name='"+name+'\''+",age="+age+'}';
        // return "User{"+"name='"+name+'\''+",age="+age+",pet="+pet+'}';
    }
}
```