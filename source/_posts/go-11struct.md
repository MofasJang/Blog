---
title: go语言结构体
date: 2020-09-24 17:16:20
tags:
- go语言
categories: 
- backend
- go语言
---
# 定义结构体
结构体定义需要使用 type 和 struct 语句。struct 语句定义一个新的数据类型，结构体中有一个或多个成员。type 语句设定了结构体的名称。结构体的格式如下：
```go
type Books struct {
   title string
   author string
   subject string
   book_id int
}
```
<!-- more -->
一旦定义了结构体类型，它就能用于变量的声明，语法格式如下：
```go
Books{"Go 语言", "www.runoob.com", "Go 语言教程", 6495407}
或
Books{title: "Go 语言", author: "www.runoob.com", subject: "Go 语言教程", book_id: 6495407}
```

# 访问结构体成员
```go
// 结构体.成员名
Book2.title = "Python 教程"
Book2.author = "www.runoob.com"
```

# 结构体作为函数参数
```go
printBook(Book1) /* 打印 Book1 信息 */

func printBook( book Books ) {  
}
```

# 结构体指针
## 定义
```go
var struct_pointer *Books
```
以上定义的指针变量可以存储结构体变量的地址。查看结构体变量地址，可以将 & 符号放置于结构体变量前：
```go
struct_pointer = &Book1
```
使用结构体指针访问结构体成员，使用 "." 操作符：
```go
struct_pointer.title
```

## 实例
```go
var Book Books  /* Declare Book1 of type Book */
printBook(&Book)  //函数调用

func printBook( book *Books ) {  //函数定义
   fmt.Printf( "Book title : %s\n", book.title)
   fmt.Printf( "Book author : %s\n", book.author)
   fmt.Printf( "Book subject : %s\n", book.subject)
   fmt.Printf( "Book book_id : %d\n", book.book_id)
}
```