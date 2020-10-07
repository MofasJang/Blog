---
title: go语言指针
date: 2020-09-24 16:11:01
tags:
- go语言
categories: 
- backend
- go语言
---
# 声明指针
一个指针变量指向了一个值的内存地址
类似于变量和常量，在使用指针前你需要声明指针。指针声明格式如下：
```go
var var_name *var-type
```
var-type 为指针类型，var_name 为指针变量名，* 号用于指定变量是作为一个指针。以下是有效的指针声明：
```go
var ip *int        /* 指向整型*/
var fp *float32    /* 指向浮点型 */
```
<!-- more -->

# 使用指针
## 过程
* 定义指针变量。
* 为指针变量赋值。
* 访问指针变量中指向地址的值。
    * 在指针类型前面加上 * 号（前缀）来获取指针所指向的内容。

## 例
```go
var a int= 20   /* 声明实际变量 */
var ip *int        /* 声明指针变量 */
ip = &a  /* 指针变量的存储地址 */

fmt.Printf("a 变量的地址是: %x\n", &a  )
/* 指针变量的存储地址 */
fmt.Printf("ip 变量储存的指针地址: %x\n", ip )
/* 使用指针访问值 */
fmt.Printf("*ip 变量的值: %d\n", *ip )
```

## 空指针
```go
var  ptr *int      /* 不赋值,ptr=0 */
if(ptr != nil)     /* ptr 不是空指针 */
if(ptr == nil)     /* ptr 是空指针 */
```

# 指针数组
```go
var ptr [MAX]*int; //定义
ptr[i] = &a[i] /* 整数地址赋值给指针数组 */
fmt.Printf("a[%d] = %d\n", i,*ptr[i] )  //访问
```

# Go 语言指针作为函数参数
Go 语言允许向函数传递指针，只需要在函数定义的参数上设置为指针类型即可。
以下实例演示了如何向函数传递指针，并在函数调用后修改函数内的值：
```go
swap(&a, &b);   //函数调用

func swap(x *int, y *int) {  //函数定义
    ...
}
```