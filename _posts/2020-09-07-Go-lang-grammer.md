---
title: Go语言-基础语法（一）
categories: [Go-Lang, 基础语法]
tags: [Go语言学习]    
---

# Go语言基础语法

## 1. Go 语言切片(Slice)

* Go 语言切片是对数组的抽象。

* Go 数组的长度不可改变，在特定场景中这样的集合就不太适用，Go中提供了一种灵活，功能强悍的内置类型切片("动态数组"),与数组相比切片的长度是不固定的，可以追加元素，在追加时可能使切片的容量增大。

**定义切片**

你可以声明一个未指定大小的数组来定义切片：

```go
var identifier []type
```

* 切片不需要说明长度。

* 或使用make()函数来创建切片:

```go
var slice1 []type = make([]type, len)
```

也可以简写为

```go
slice1 := make([]type, len)
```

也可以指定容量，其中capacity为可选参数，make([]T, length, capacity)，这里 len 是数组的长度并且也是切片的初始长度。

 

## 2. Go 语言范围(Range)

​		Go 语言中 range 关键字用于 for 循环中迭代数组(array)、切片(slice)、通道(channel)或集合(map)的元素。在数组和切片中它返回元素的索引和索引对应的值，在集合中返回 key-value 对。

**实例**

```go
package main
import "fmt"
func main() {
  //这是我们使用range去求一个slice的和。使用数组跟这个很类似*
  nums := []int{2, 3, 4}
  sum := 0
  for _, num := **range** nums {
  	sum += num
  }
  fmt.Println("sum:", sum)
  //在数组上使用range将传入index和值两个变量。上面那个例子我们不需要使用该元素的序号，所以我们使用空白符"_"省略了。有时侯我们确实需要知道它的索引。*
  for i, num := **range** nums {
	if num == 3 {
     fmt.Println("index:", i)
    }
  }
	//range也可以用在map的键值对上。
  kvs := map[string]string{"a": "apple", "b": "banana"}
  for k, v := range kvs {
      fmt.Printf("%s -> %s**\n**", k, v)
  }
  //range也可以用来枚举Unicode字符串。第一个参数是字符的索引，第二个是字符（Unicode的值）本身。*
  for i, c := range "go" {
      fmt.Println(i, c)
  }
}
```

## 3. Go 语言接口

​	Go 语言提供了另外一种数据类型即接口，它把所有的具有共性的方法定义在一起，任何其他类型只要实现了这些方法就是实现了这个接口。

**实例**

```go
/* 定义接口 */
type interface_name interface {
  method_name1 [return_type]
  method_name2 [return_type]
  method_name3 [return_type]
  ...
  method_namen [return_type]
}

/* 定义结构体 */
type struct_name struct {
  /* variables */
}
/* 实现接口方法 */

func (struct_name_variable struct_name) method_name1() [return_type] {
  /* 方法实现 */
}
...
func (struct_name_variable struct_name) method_namen() [return_type] {
  /* 方法实现*/
}
```

