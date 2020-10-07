---
title: go语言接口
date: 2020-09-25 19:21:21
tags:
- go语言
categories: 
- backend
- go语言
---
Go 语言提供了另外一种数据类型即接口，它把所有的具有共性的方法定义在一起，任何其他类型只要实现了这些方法就是实现了这个接口。

<!-- more -->
实例
```go
package main
import "fmt"

type Phone interface {    //接口定义
    call()
}

type NokiaPhone struct {    //结构体定义
}

func (nokiaPhone NokiaPhone) call() {
    fmt.Println("I am Nokia, I can call you!")
}

type IPhone struct {
}

func (iPhone IPhone) call() {
    fmt.Println("I am iPhone, I can call you!")
}

func main() {
    var phone Phone

    phone = new(NokiaPhone)
    phone.call()

    phone = new(IPhone)
    phone.call()

}
```

