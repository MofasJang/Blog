---
title: php数组
date: 2020-10-16 16:41:50
tags:
- php
categories: 
- frontend
- php
---
{% note info %}
[数组相关函数](https://www.runoob.com/php/php-ref-array.html)
{% endnote %}
# 分类
* 数值数组 - 带有数字 ID 键的数组
* 关联数组 - 带有指定的键的数组，每个键关联一个值
* 多维数组 - 包含一个或多个数组的数组
<!-- more -->

## 数值数组
### 定义
1. 自动分配 ID 键
```php
$cars=array("Volvo","BMW","Toyota");
```
2. 人工分配 ID 键：
```php
$cars[0]="Volvo";
$cars[1]="BMW";
$cars[2]="Toyota";
```

### count() 函数
返回数组元素数量

## 关联数组
### 定义
1. 
```php
$age=array("Peter"=>"35","Ben"=>"37","Joe"=>"43");
```
2. 
```php
$age['Peter']="35";
$age['Ben']="37";
$age['Joe']="43";
```
### 遍历关联数组
遍历并打印关联数组中的所有值，您可以使用 foreach 循环，如下所示：
```php
<?php
$age=array("Peter"=>"35","Ben"=>"37","Joe"=>"43");
 
foreach($age as $x=>$x_value)
{
    echo "Key=" . $x . ", Value=" . $x_value . PHP_EOL;
}
?>
```

# 数组排序
* sort() - 对数组进行升序排列
* rsort() - 对数组进行降序排列
* asort() - 根据关联数组的值，对数组进行升序排列
* ksort() - 根据关联数组的键，对数组进行升序排列
* arsort() - 根据关联数组的值，对数组进行降序排列
* krsort() - 根据关联数组的键，对数组进行降序排列

