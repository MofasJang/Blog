---
title: go语言数组
date: 2020-09-24 16:01:14
tags:
- go语言
categories: 
- backend
- go语言
---
# 声明数组
Go 语言数组声明需要指定元素类型及元素个数，语法格式如下：
```go
var variable_name [SIZE] variable_type
```
以上为一维数组的定义方式。例如以下定义了数组 balance 长度为 10 类型为 float32：
```go
var balance [10] float32
```
<!-- more -->

# 初始化数组
```go
var balance = [5]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
//忽略 [] 中的数字不设置数组大小
 var balance = [...]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
 ```

# 二维数组
## 定义
二维数组是最简单的多维数组，二维数组本质上是由一维数组组成的。二维数组定义方式如下：
```go
var arrayName [ x ][ y ] variable_type
```
## 初始化
多维数组可通过大括号来初始值。以下实例为一个 3 行 4 列的二维数组：
```go
a = [3][4]int{  
 {0, 1, 2, 3} ,   /*  第一行索引为 0 */
 {4, 5, 6, 7} ,   /*  第二行索引为 1 */
 {8, 9, 10, 11},   /* 第三行索引为 2 */
}
```

# 向函数传数组
1. 形参设定数组大小：`void myFunction(param [10]int)`

2. 形参未设定数组大小：`void myFunction(param []int)`