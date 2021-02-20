---
title: php面向对象
date: 2020-10-18 14:02:29
tags:
- php
categories: 
- frontend
- php
---
　　PHP的面向对象大体和C++相同，下面具体阐述php的不同之处。
# PHP 构造函数
　　构造函数是一种特殊的方法。主要用来在创建对象时初始化对象， 即为对象成员变量赋初始值，在创建对象的语句中与 new 运算符一起使用。

　　PHP 5 允许开发者在一个类中定义一个方法作为构造函数，语法格式如下：
```php
void __construct ([ mixed $args [, $... ]] ){}
```
<!-- more -->
在上面的例子中我们就可以通过构造方法来初始化 $url 和 $title 变量：
```php
function __construct( $par1, $par2 ) {
   $this->url = $par1;
   $this->title = $par2;
}
```
现在我们就不需要再调用 setTitle 和 setUrl 方法了：
```php
$runoob = new Site('www.runoob.com', '菜鸟教程');
```
---

# 析构函数
　　析构函数(destructor) 与构造函数相反，当对象结束其生命周期时（例如对象所在的函数已调用完毕），系统自动执行析构函数。

　　PHP 5 引入了析构函数的概念，这类似于其它面向对象的语言，其语法格式如下：
```php
void __destruct ( void ){}
```
---

# 常量
```php
class MyClass
{
    const constant = '常量值';   //定义

    function showConstant() {
        echo  self::constant . PHP_EOL;  //使用
    }
}
```
---

# Static 关键字
　　声明类属性或方法为 static(静态)，就可以不实例化类而直接访问。
　　静态属性访问用‘::’
　　定义静态属性：
```php
<?php
class Foo {
  public static $my_static = 'foo';
}
?>
```
　　访问静态属性：
```php
//直接访问类属性
print Foo::$my_static . PHP_EOL;
//实例化后访问
$foo = new Foo();
print $foo::$my_static . PHP_EOL;
```
---

# 调用父类构造方法
　　PHP 不会在子类的构造方法中自动的调用父类的构造方法。要执行父类的构造方法，需要在子类的构造方法中调用 parent::__construct() 。