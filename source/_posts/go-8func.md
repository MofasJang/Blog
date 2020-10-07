---
title: go语言函数
date: 2020-09-23 18:48:58
tags:
- go语言
categories: 
- backend
- go语言
---
# 函数定义
## Go 语言函数定义格式如下：
```go
func function_name( [parameter list] ) [return_types] {
   函数体
}
```
<!-- more -->

## 函数定义解析：

* func：函数由 func 开始声明
* function_name：函数名称，函数名和参数列表一起构成了函数签名。
* parameter list：参数列表，参数就像一个占位符，当函数被调用时，你可以将值传递给参数，这个值被称为实际参数。参数列表指定的是参数类型、顺序、及参数个数。参数是可选的，也就是说函数也可以不包含参数。
* return_types：返回类型，函数返回一列值。return_types 是该列值的数据类型。有些功能不需要返回值，这种情况下 return_types 不是必须的。
* 函数体：函数定义的代码集合。

## 举例
以下实例为 max() 函数的代码，该函数传入两个整型参数 num1 和 num2，并返回这两个参数的最大值：
```go
/* 函数返回两个数的最大值 */
func max(num1, num2 int) int {
   /* 声明局部变量 */
   var result int

   if (num1 > num2) {
      result = num1
   } else {
      result = num2
   }
   return result 
}
```

## 函数调用
当创建函数时，你定义了函数需要做什么，通过调用该函数来执行指定任务。
调用函数，向函数传递参数，并返回值，例如：
```go
package main
import "fmt"
func main() {
   /* 定义局部变量 */
   var a int = 100
   var b int = 200
   var ret int

   /* 调用函数并返回最大值 */
   ret = max(a, b)

   fmt.Printf( "最大值是 : %d\n", ret )
}

/* 函数返回两个数的最大值 */
func max(num1, num2 int) int {
   /* 定义局部变量 */
   var result int

   if (num1 > num2) {
      result = num1
   } else {
      result = num2
   }
   return result 
}
```

## 函数返回多个值
```go
func swap(x, y string) (string, string) {
   return y, x
}
```

# 函数参数
## 值传递
传递是指在调用函数时将实际参数复制一份传递到函数中，这样在函数中如果对参数进行修改，将不会影响到实际参数。

## 引用传递
引用传递是指在调用函数时将实际参数的地址传递到函数中，那么在函数中对参数所进行的修改，将影响到实际参数。
```go
/* 定义交换值函数*/
func swap(x *int, y *int) {
   var temp int
   temp = *x    /* 保持 x 地址上的值 */
   *x = *y      /* 将 y 值赋给 x */
   *y = temp    /* 将 temp 值赋给 y */
}

//调用
swap(&a, &b)
```

# 函数用法
## 函数作为另外一个函数的实参	
函数定义后可作为另外一个函数的实参数传入
```go
func main(){
   /* 声明函数变量 */
   getSquareRoot := func(x float64) float64 {
      return math.Sqrt(x)
   }

   /* 使用函数 */
   fmt.Println(getSquareRoot(9))

}
```

## 闭包	
闭包是匿名函数，可在动态编程中使用。
以下实例中，我们创建了函数 getSequence() ，返回另外一个函数。该函数的目的是在闭包中递增 i 变量，代码如下：
```go
package main
import "fmt"
func getSequence() func() int {
   i:=0
   return func() int {
      i+=1
     return i  
   }
}

func main(){
   /* nextNumber 为一个函数，函数 i 为 0 */
   nextNumber := getSequence()  

   /* 调用 nextNumber 函数，i 变量自增 1 并返回 */
   fmt.Println(nextNumber())
   fmt.Println(nextNumber())
   fmt.Println(nextNumber())
   
   /* 创建新的函数 nextNumber1，并查看结果 */
   nextNumber1 := getSequence()  
   fmt.Println(nextNumber1())
   fmt.Println(nextNumber1())
}
```

## 方法
方法就是一个包含了接受者的函数
```go
func (variable_name variable_data_type) function_name() [return_type]{
   /* 函数体*/
}
```
下面定义一个结构体类型和该类型的一个方法：
```go
package main

import (
   "fmt"  
)

/* 定义结构体 */
type Circle struct {
  radius float64
}

func main() {
  var c1 Circle
  c1.radius = 10.00
  fmt.Println("圆的面积 = ", c1.getArea())
}

//该 method 属于 Circle 类型对象中的方法
func (c Circle) getArea() float64 {
  //c.radius 即为 Circle 类型对象中的属性
  return 3.14 * c.radius * c.radius
}
```