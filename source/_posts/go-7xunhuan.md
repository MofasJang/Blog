---
title: go语言循环语句
date: 2020-09-23 13:48:29
tags:
- go语言
categories: 
- backend
- go语言
---
# for循环
## 基础语法
1. 和 C 语言的 for 一样：

```go
package main
import "fmt"
func main() {
        sum := 0
        for i := 0; i <= 10; i++ {
                sum += i
        }
        fmt.Println(sum)
}
```
<!-- more -->

2. 和 C 的 while 一样：

```go
package main
import "fmt"

func main() {
        sum := 1
        for sum <= 10{
                sum += sum
        }
        fmt.Println(sum)
}
```

3. 和 C 的 for(;;) 一样：

```go
package main
import "fmt"
func main() {
        sum := 0
        for {
            sum++ // 无限循环下去
        }
        fmt.Println(sum) // 无法输出
}
```

## For-each range 循环
这种格式的循环可以对字符串、数组、切片等进行迭代输出元素。
```go
package main
import "fmt"
func main() {
        strings := []string{"google", "runoob"}
        for i, s := range strings {
                fmt.Println(i, s)
        }

        numbers := [6]int{1, 2, 3, 5} 
        for i,x:= range numbers {
                fmt.Printf("第 %d 位 x 的值 = %d\n", i,x)
        }  
}
```

# 循环控制语句
## break语句
### Go 语言中 break 语句用于以下两方面：
* 用于循环语句中跳出循环，并开始执行循环之后的语句。
  （在多重循环中，可以用标号 label 标出想 break 的循环。）
* break 在 switch（开关语句）中在执行一条 case 后跳出语句的作用。

### label标号举例
```go
package main
import "fmt"
func main() {

    // 不使用标记
    fmt.Println("---- break ----")
    for i := 1; i <= 3; i++ {
        fmt.Printf("i: %d\n", i)
                for i2 := 11; i2 <= 13; i2++ {
                        fmt.Printf("i2: %d\n", i2)
                        break //跳出最近一层循环
                }
        }

    // 使用标记
    fmt.Println("---- break label ----")
    re:
        for i := 1; i <= 3; i++ {
            fmt.Printf("i: %d\n", i)
            for i2 := 11; i2 <= 13; i2++ {
                fmt.Printf("i2: %d\n", i2)
                break re  //直接结束re语句
            }
        }
}
```

## continue 语句
### 使用情况
* for 循环中，执行 continue 语句会触发 for 增量语句的执行。
* 在多重循环中，可以用标号 label 标出想 continue 的循环。

### 举例
```go
package main
import "fmt"
func main() {
    // 不使用标记
    fmt.Println("---- continue ---- ")
    for i := 1; i <= 3; i++ {
        fmt.Printf("i: %d\n", i)
            for i2 := 11; i2 <= 13; i2++ {
                fmt.Printf("i2: %d\n", i2)
                continue  //i2++
            }
    }

    // 使用标记
    fmt.Println("---- continue label ----")
    re:
        for i := 1; i <= 3; i++ {
            fmt.Printf("i: %d\n", i)
                for i2 := 11; i2 <= 13; i2++ {
                    fmt.Printf("i2: %d\n", i2)
                    continue re //i++
                }
        }
}
```

## goto语句
Go 语言的 goto 语句可以无条件地转移到过程中指定的行。
### 举例
goto配合label LOOP使用，相当于for配合continue
```go
package main
import "fmt"
func main() {
   /* 定义局部变量 */
   var a int = 10

   /* 循环 */
   LOOP: for a < 20 {
      if a == 15 {
         /* 跳过迭代 */
         a = a + 1
         goto LOOP
      }
      fmt.Printf("a的值为 : %d\n", a)
      a++     
   }  
}
```