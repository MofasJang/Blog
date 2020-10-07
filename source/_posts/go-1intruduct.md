---
title: go语言入门（环境安装）
date: 2020-09-22 15:07:01
tags:
- go语言
categories: 
- backend
- go语言
---
# go语言简介
## Go 语言特色
* 简洁、快速、安全
* 并行、有趣、开源
* 内存管理、数组安全、编译迅速

## Go 语言用途
* Go 语言被设计成一门应用于搭载 Web 服务器，存储集群或类似用途的巨型中央服务器的系统编程语言。
* 对于高性能分布式系统领域而言，Go 语言无疑比大多数其它语言有着更高的开发效率。它提供了海量并行的支持，这对于游戏服务端的开发而言是再好不过了。
<!-- more -->

# 环境搭建
## go语言环境安装
* 访问[go语言网站](https://golang.google.cn/dl/)，选择系统并下载
* 点击.msi文件进行安装
* 在cmd中输入 `go version` 测试环境是否安装

## 代码测试
* 在任意位置新建hello.go文件
```go  
//  定义了包名package, main表示一个可独立执行的程序
//  每个文件必须声明包
//  每个 Go 应用程序都包含一个名为 main 的包。
package main
//  这个程序需要使用 fmt 包（的函数，或其他元素），fmt 包实现了格式化 IO（输入/输出）的函数。
import "fmt"
//  入口函数
func main(){   //{ 不能在单独的行上
	// 打印，最后会换行
	fmt.Println("hello world")
}
```
* cmd定位到当前文件夹输入 `go run hello.go`
* 输出结果为 `hello world`
* `go build hello.go` 可以将代码打包成exe文件

## VSCode插件安装
### 安装下图两个插件
![go插件](go插件.jpg)
![代码运行插件](coderunner.jpg)
### 编写代码，右键run code运行
{% note info %}
vscode需要以管理员身份运行
{% endnote %}


