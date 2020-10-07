---
title: go语言变量
date: 2020-09-22 16:41:26
tags:
- go语言
categories: 
- backend
- go语言
---
# Go 语言变量
## 一般形式
变量来源于数学，是计算机语言中能储存计算结果或能表示值抽象概念。
变量可以通过变量名访问。

Go 语言变量名由字母、数字、下划线组成，其中首个字符不能为数字。
声明变量的一般形式是使用 var 关键字：
```go
var identifier type
```
<!-- more -->
可以一次声明多个变量：
```go
var identifier1, identifier2 type
```

## 变量声明
### 第一种，指定变量类型，如果没有初始化，则变量默认为零值。
```go
// 声明一个变量并初始化，零值为""
var a string = "RUNOOB"

// 数值类型没有初始化就为0
var b int

// bool 零值为 false
var c bool

//以下几种类型零值为 nil：
var a *int
var a []int
var a map[string] int
var a chan int
var a func(string) int
var a error // error 是接口
```
### 第二种，根据值自行判定变量类型。
```go
var v_name = value
```
### 第三种，省略 var, 注意 := 左侧如果没有声明新的变量，就产生编译错误，格式：
```go
v_name := value
```
例
```go
var intVal int 
intVal :=1 // 这时候会产生编译错误
intVal,intVal1 := 1,2 // 此时不会产生编译错误，因为有声明新的变量，因为 := 是一个声明语句,支持多变量声明
```
### 全局声明
```go
var (  // 这种因式分解关键字的写法一般用于声明全局变量
    a int
    b bool
)
```

## 变量赋值
```go
var a, b int
var c string
a, b, c = 5, 7, "abc"
```
上面这行假设了变量 a，b 和 c 都已经被声明，否则的话应该这样使用：
```go
a, b, c := 5, 7, "abc"
```
{% note info %}
单纯地给变量赋值是不够的，这个值必须被使用
{% endnote %}
如果你想要交换两个变量的值，则可以简单地使用` a, b = b, a`，两个变量的类型必须是相同。
