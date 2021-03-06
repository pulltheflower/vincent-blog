---
layout: post
title:  "Go 语言学习笔记"
date:   2021-09-07 17:02:31 +0800
categories: jekyll update
---
#### 指令集

- 复杂指令集： 通过增加指令的类型减少需要执行的指令数
- 精简指令集： 使用更少的指令类型完成目标的计算任务


#### 编译原理

go语言编译器的源代码在 `src/cmd/compile` 目录中

##### 编译器
前端： 词法分析、语法分析、类型检查、中间代码生成

后端： 目标代码的生成和优化（将中间代码翻译成目标机器能够运行的二进制机器码）

go的编译器在逻辑上可以被分为四个阶段： 
1. 词法和语法分析
2. 类型检查和AST转换
3. 通用SSA生成
4. 机器代码生成

##### 类型检查
1. 常量、类型和函数名及类型
2. 变量的赋值和初始化
3. 函数和闭包的主体
4. 哈希键值对的类型
5. 导入函数体
6. 外部的声明


##### 中间代码生成

在类型检查后，编译器会通过 `cmd/compile/internal/gc.compileFunctions` 编译整个Go语言项目中的全部函数，这些函数会在一个编译队列中等待几个Goroutine的消费，并发执行的Goroutine会将所有函数对应的抽象语法书转换成中间代码


##### 机器码生成

Go语言源代码的 `src/cmd/compile/internal` 目录中包含了很多机器码生成相关的包，不同类型的CPU分别使用了不同的包生成机器码，其中包括 amd64、arm、arm64、mips、mips64、ppc64、s390x、x86和wasm


## 数据结构


### 数组

表示方法

``` go
[10]int
[200]interface{}
```
`Go语言数组在初始化之后大小就无法改变，存储元素类型相同、但是大小不同的数组类型在Go语言看来也是完全不同的，只有两个条件都相同才是同一类型`

初始化

``` go
arr1 := [3]int{1, 2, 3}
arr2 := [...]int{1, 2, 3}
```

1. 当数组元素数量小于或者等于4个时，会直接将数组中的元素放置到栈上；
2. 当元素的数量大于4个时，会将数组中的元素放置到静态区并在运行时取出；


### 切片

切片，即动态数组，其长度并不固定，我们可以向切片中追加元素，它会在容量不足时自动扩容。

``` go
[]int
[]interface{}
```

三种初始化切片的方式
``` go
1. arr[0:3] or slice[0:3]
2. slice := []int{1, 2, 3}
3. slice := make([]int, 10 )
```


#### 字面量

``` go
[]int{1, 2, 3}
```

当使用字面量创建切片的时候

``` go
var vstat [3]int
vstat[0] = 1
vstat[1] = 2
vstat[2] = 3
var vauto *[3]int = new([3]int)
*vauto = vstat
slice := vauto[:]
```

1. 根据切片中的元素数量对底层数组的大小进行推断并创建一个数组
2. 将这些字面量元素存储到初始化的数组中
3. 创建一个同样指向 `[3]int` 类型的数组指针
4. 将静态存储区的数组 `vstat` 赋值给 `vauto` 指针所在的地址
5. 通过 `[:]` 操作获取一个底层使用 `vauto` 的切片


创建切片的过程中如果发生以下错误会直接触发运行错误并崩溃：
1. 内存空间的大小发生了溢出
2. 申请的内存大于最大可分配的内存
3. 传入的长度小于0或者长度大于容量
