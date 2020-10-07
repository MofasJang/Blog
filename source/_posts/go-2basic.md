---
title: go语言基础语法
date: 2020-09-22 16:15:09
tags:
- go语言
categories: 
- backend
- go语言
---
## 注释
注释不会被编译，每一个包应该有相关注释。
单行注释是最常见的注释形式，你可以在任何地方使用以 // 开头的单行注释。多行注释也叫块注释，均已以 /* 开头，并以 */ 结尾。如：
```
// 单行注释
/*
 Author by 菜鸟教程
 我是多行注释
 */
```
<!-- more -->

## 标识符
标识符用来命名变量、类型等程序实体。一个标识符实际上就是一个或是多个字母(A~Z和a~z)数字(0~9)、下划线_组成的序列，但是第一个字符必须是字母或下划线而不能是数字。
以下是有效的标识符：
```
mahesh   kumar   abc   move_name   a_123
myname50   _temp   j   a23b9   retVal
```
## 字符串连接
Go 语言的字符串可以通过'+'实现

## 关键字
下面列举了 Go 代码中会使用到的 25 个关键字或保留字：

|  break   | default     | func   | interface | select |
| :------: | :---------: | :----: | :-------: | :----: |
|   case   | defer       | go     | map       | struct |
|   chan   | else        | goto   | package   | switch |
|  const   | fallthrough | if     | range     | type   |
| continue | for         | import | return    | var    |

除了以上介绍的这些关键字，Go 语言还有 36 个预定义标识符：

| append | bool    | byte    | cap     | close  | complex | complex64 | complex128 | uint16  |
| ------ | ------- | ------- | ------- | ------ | ------- | --------- | ---------- | ------- |
| copy   | false   | float32 | float64 | imag   | int     | int8      | int16      | uint32  |
| int32  | int64   | iota    | len     | make   | new     | nil       | panic      | uint64  |
| print  | println | real    | recover | string | true    | uint      | uint8      | uintptr |

## Go 语言中变量的声明必须使用空格隔开，如：

```
var age int;
```

## Go 语言数据类型
在 Go 编程语言中，数据类型用于声明函数和变量。
数据类型的出现是为了把数据分成所需内存大小不同的数据，编程的时候需要用大数据的时候才需要申请大内存，就可以充分利用内存。
Go 语言按类别有以下几种数据类型：

| 序号 | 类型和描述                                                   |
| ---- | ------------------------------------------------------------ |
| 1    | **布尔型** 布尔型的值只可以是常量 true 或者 false。一个简单的例子：var b bool = true。 |
| 2    | **数字类型** 整型 int 和浮点型 float32、float64，Go 语言支持整型和浮点型数字，并且支持复数，其中位的运算采用补码。 |
| 3    | **字符串类型:** 字符串就是一串固定长度的字符连接起来的字符序列。Go 的字符串是由单个字节连接起来的。Go 语言的字符串的字节使用 UTF-8 编码标识 Unicode 文本。 |
| 4    | **派生类型:** 包括：(a) 指针类型（Pointer）(b) 数组类型(c) 结构化类型(struct)(d) Channel 类型(e) 函数类型(f) 切片类型(g) 接口类型（interface）(h) Map 类型 |

