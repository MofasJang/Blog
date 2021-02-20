---
title: php变量和常量
date: 2020-10-16 14:45:15
tags:
- php
categories: 
- frontend
- php
---
# PHP 变量作用域
变量的作用域是脚本中变量可被引用/使用的部分。
PHP 有四种不同的变量作用域：
* local
* global
* static
* parameter
<!-- more -->

## local
　　默认情况下函数内部声明的变量皆为局部变量，仅在函数内部访问。

## global
1. global 关键字用于函数内访问全局变量。

　　在函数内调用函数外定义的全局变量，我们需要在函数中的变量前加上 global 关键字：
```php
<?php
$x=5;
$y=10;

function myTest()
{
    global $x,$y;
    $y=$x+$y;
}
 
myTest();
echo $y; // 输出 15
?>
```

2. PHP 将所有全局变量存储在一个名为 $GLOBALS[index] 的数组中。 index 保存变量的名称。这个数组可以在函数内部访问，也可以直接用来更新全局变量。

　　上面的实例可以写成这样：
```php
<?php
$x=5;
$y=10;
 
function myTest()
{
    $GLOBALS['y']=$GLOBALS['x']+$GLOBALS['y'];
} 
 
myTest();
echo $y;
?>
```

## static
局部变量在函数结束后不删除，且static语句只执行一次。

## parameter
参数在函数调用时被传递进函数内。

# 字符串变量
* 字符串连接用'.'
* strlen() 函数返回字符串长度
* strpos() 函数在字符串内查找一个字符或一段指定的文本，strpos("Hello world!","world")返回6。

[字符串函数](https://www.runoob.com/php/php-ref-string.html)

# 常量
## 设置常量，使用 define() 函数，函数语法如下：
```php
bool define ( string $name , mixed $value [, bool $case_insensitive = false ] )
```
该函数有三个参数:

* name：必选参数，常量名称，即标志符。
* value：必选参数，常量的值。
* case_insensitive ：可选参数，如果设置为 TRUE，该常量则大小写不敏感。默认是大小写敏感的。

以下实例我们创建一个 区分大小写的常量, 常量值为 "欢迎访问 Runoob.com"：
```php
<?php
// 区分大小写的常量名
define("GREETING", "欢迎访问 Runoob.com");
echo GREETING;    // 输出 "欢迎访问 Runoob.com"
echo '<br>';
echo greeting;   // 输出 "greeting"
?>
```

## 常量是全局的
可以在整个脚本的任何地方使用