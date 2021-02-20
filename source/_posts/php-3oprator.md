---
title: php运算符
date: 2020-10-16 16:10:42
tags:
- php
categories: 
- frontend
- php
---
# 比较运算符
> 大多与c语言相同
## 不同点：
### ===
绝对等于，如果 x 等于 y，且它们类型相同，则返回true。如5==="5" 返回 false
### <>
不等于，如果 x 不等于 y，则返回 true。如5<>8 返回 true 
### !==
绝对不等于，如果 x 不等于 y，或它们类型不相同，则返回 true。5!=="5" 返回 true
<!-- more -->

# 三元运算符
## 语法格式
```php
(expr1) ? (expr2) : (expr3) 
```
　　对 expr1 求值为 TRUE 时的值为 expr2，在 expr1 求值为 FALSE 时的值为 expr3。

　　自 PHP 5.3 起，可以省略三元运算符中间那部分。表达式 expr1 ?: expr3 在 expr1 求值为 TRUE 时返回 expr1，否则返回 expr3。

## 实例
　　以下实例中通过判断 $_GET 请求中含有 user 值，如果有返回 $_GET['user']，否则返回 nobody：
```php
<?php
$test = '菜鸟教程';
// 普通写法
$username = isset($test) ? $test : 'nobody';
echo $username, PHP_EOL;　//注意：PHP_EOL 是一个换行符，兼容更大平台。
 
// PHP 5.3+ 版本写法
$username = $test ?: 'nobody';
echo $username, PHP_EOL;
?>
```

# 组合比较符(PHP7+)
　　PHP7+ 支持组合比较符（combined comparison operator）也称之为太空船操作符，符号为 <=>。组合比较运算符可以轻松实现两个变量的比较，当然不仅限于数值类数据的比较。

语法格式如下：
```php
$c = $a <=> $b;
```
解析如下：

如果 $a > $b, 则 $c 的值为 1。
如果 $a == $b, 则 $c 的值为 0。
如果 $a < $b, 则 $c 的值为 -1。