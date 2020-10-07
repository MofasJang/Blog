---
title: go语言常量
date: 2020-09-22 17:15:20
tags:
- go语言
categories: 
- backend
- go语言
---
# 常量的定义格式：
## 一般格式
```go
const identifier [type] = value
```
你可以省略类型说明符 [type]，因为编译器可以根据变量的值来推断其类型。

* 显式类型定义： `const b string = "abc"`
* 隐式类型定义： `const b = "abc"`
<!-- more -->

## 多个相同类型的声明可以简写为：
```go
const c_name1, c_name2 = value1, value2
```

## 常量还可以用作枚举：
(常量若未赋值，取值和上一个相同)
```go
const (
    Unknown = 0
    Female = 1
    Male = 2
)
```

# iota
* iota，特殊常量，可以认为是一个可以被编译器修改的常量。
* iota 在 const关键字出现时将被重置为 0(const 内部的第一行之前)，const 中每新增一行常量声明将使 iota 计数一次(iota 可理解为 const 语句块中的行索引)。
iota 可以被用作枚举值：
```go
const (
    a = iota
    b = iota
    c = iota
)
```
第一个 iota 等于 0，每当 iota 在新的一行被使用时，它的值都会自动加 1；所以 a=0, b=1, c=2 
举例：
```go
package main

import "fmt"

func main() {
    const (
        a = iota   //0
        b          //1
        c          //2
        d = "ha"   //独立值，iota += 1
        e          //"ha"   iota += 1
        f = 100    //iota +=1
        g          //100  iota +=1
        h = iota   //7,恢复计数
        i          //8
    )
    fmt.Println(a,b,c,d,e,f,g,h,i)
}
```
结果为`0 1 2 ha ha 100 100 7 8`



