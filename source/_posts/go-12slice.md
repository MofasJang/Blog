---
title: go语言——切片
date: 2020-09-24 17:40:55
tags:
- go语言
categories: 
- backend
- go语言
---
# 定义
Go 语言切片是对数组的抽象。
Go 数组的长度不可改变，在特定场景中这样的集合就不太适用，Go中提供了一种灵活，功能强悍的内置类型切片("动态数组"),与数组相比切片的长度是不固定的，可以追加元素，在追加时可能使切片的容量增大。
<!-- more -->
你可以声明一个未指定大小的数组来定义切片：
```go
var identifier []type;   //切片不需要说明长度,未赋值前identifier==nil
var slice1 []type = make([]type, len) //或使用make()函数来创建切片
slice1 := make([]type, len)  //也可以简写
make([]T, length, capacity) // 也可以指定容量，其中capacity为可选参数
```

# 初始化
有三中方法
```go
s :=make([]int,len,cap) 
s :=[] int {1,2,3 }
s := arr[startIndex:endIndex] 
```

# 相关函数
## len() 和 cap() 函数
切片是可索引的，并且可以由 len() 方法获取长度。
切片提供了计算容量的方法 cap() 可以测量切片最长可以达到多少。
```go
fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
```

## append() 和 copy() 函数
如果想增加切片的容量，我们必须创建一个新的更大的切片并把原分片的内容都拷贝过来。
下面的代码描述了从拷贝切片的 copy 方法和向切片追加新元素的 append 方法。
```go
/* 向切片添加一个元素 */
numbers = append(numbers, 1)

/* 同时添加多个元素 */
numbers = append(numbers, 2,3,4)

/* 拷贝 numbers 的内容到 numbers1 */
copy(numbers1,numbers)
```

# 切片截取
```go
numbers := []int{0,1,2,3,4,5,6,7,8}   //len=9 cap=9 slice=[0 1 2 3 4 5 6 7 8]
number3 := numbers[2:5]   //len=3 cap=7 slice=[2 3 4],cap从[start:end]的start开始向后计算
```