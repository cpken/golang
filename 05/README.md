#### 第五章 控制结构

- 5.1 if-eles 结构
- 5.2 测试多返回值函数的错误
- 5.3 switch 结构
- 5.4 for 结构
- 5.5 Break 与 continue
- 5.6 标签与 goto


#### 5.1. if-else 结构

if 是用于测试某个条件（布尔型或逻辑型）的语句，如果该条件成立，则会执行 if 后由大括号括起来的代码块，否则就忽略该代码块继续执行后续的代码。

~~~go
if condition {
  // do something
}
~~

如果存在第二个分支，则可以在上面代码的基础上添加 else 关键字以及另一代码块，这个代码块中的代码只有在条件不满足时才会执行。if 和 else 后的两个代码块是相互独立的分支，只可能执行其中一个。

~~~go
if condition {
    // do something 
} else {
    // do something 
}
~~~

如果存在第三个分支，则可以使用下面这种三个独立分支的形式：

~~~go
if condition1 {
    // do something 
} else if condition2 {
    // do something else    
} else {
    // catch-all or default
}
~~~

else-if 分支的数量是没有限制的，但是为了代码的可读性，还是不要在 if 后面加入太多的 else-if 结构。
如果你必须使用这种形式，则把尽可能先满足的条件放在前面。  

关键字 if 和 else 之后的左大括号 { 必须和关键字在同一行，如果你使用了 else-if 结构，则前段代码块的右大括号 } 必须和 else-if 关键字在同一行。这两条规则都是被编译器强制规定的。  

非法的 Go 代码：

~~~go
if x{
}
else {  // 无效的
}
~~~

在有些情况下，条件语句两侧的括号是可以被省略的；当条件比较复杂时，则可以使用括号让代码更易读。  

示例 5.1 booleans.go

~~~go
package main
import "fmt"
func main() {
    bool1 := true
    if bool1 {
        fmt.Printf("The value is true\n")
    } else {
        fmt.Printf("The value is false\n")
    }
}
~~~

输出：

~~~go
The value is true
~~~

当 if 结构内有 break、continue、goto 或者 return 语句时，Go 代码的常见写法是省略 else 部分。  

##### 注意事项
不要同时在 if-else 结构的两个分支里都使用 return 语句，这将导致编译报错 function ends without a return statement（你可以认为这是一个编译器的 Bug 或者特性）。
（ 译者注：该问题已经在 Go 1.1 中被修复或者说改进 ）


#### 5.2. 测试多返回值函数的错误

Go 语言的函数经常使用两个返回值来表示执行是否成功：返回某个值以及 true 表示成功；返回零值（或 nil）和 false 表示失败。  

当不使用 true 或 false 的时候，也可以使用一个 error 类型的变量来代替作为第二个返回值：成功执行的话，error 的值为 nil，否则就会包含相应的错误信息，这样的形式又称之为 comma,ok 
模式（pattern）。  



#### 5.3. switch 结构

switch 接受任意形式的表达式：

~~~go
switch var1 {
    case val1:
        ...
    case val2:
        ...
    default:
        ...
}
~~~

变量 var1 可以是任何类型，而 val1 和 val2 则可以是同类型的任意值。
类型不被局限于常量或整数，但必须是相同的类型；或者最终结果为相同类型的表达式。
前花括号 { 必须和 switch 关键字在同一行。  

您可以同时测试多个可能符合条件的值，使用逗号分割它们，例如：case val1, val2, val3。  

每一个 case 分支都是唯一的，从上至下逐一测试，直到匹配为止。
（ Go 语言使用快速的查找算法来测试 switch 条件与 case 分支的匹配情况，直到算法匹配到某个 case 或者进入 default 条件为止。）  

一旦成功地匹配到某个分支，在执行完相应代码后就会退出整个 switch 代码块，也就是说您不需要特别使用 break 语句来表示结束。  

如果在执行完每个分支的代码后，还希望继续执行后续分支的代码，可以使用 fallthrough 关键字来达到目的。  

~~~go
switch i {
    case 0: // 空分支，只有当 i == 0 时才会进入分支
    case 1:
        f() // 当 i == 0 时函数不会被调用
}
~~~

并且：

~~~go
switch i {
    case 0: fallthrough
    case 1:
        f() // 当 i == 0 时函数也会被调用
}
~~~

在 case ...: 语句之后，您不需要使用花括号将多行语句括起来，但您可以在分支中进行任意形式的编码。当代码块只有一行时，可以直接放置在 case 语句之后。  

switch 语句的第三种形式是包含一个初始化语句：

~~~go
switch initialization {
    case val1:
        ...
    case val2:
        ...
    default:
        ...
}
~~~

这种形式可以非常优雅地进行条件判断：

~~~go
switch result := calculate(); {
    case result < 0:
        ...
    case result > 0:
        ...
    default:
        // 0
}
~~~

在下面这个代码片段中，变量 a 和 b 被平行初始化，然后作为判断条件：

~~~go
switch a, b := x[i], y[j]; {
    case a < b: t = -1
    case a == b: t = 0
    case a > b: t = 1
}
~~~


#### 5.4. for 结构

#### 5.4.1 基于计数器的迭代

基本形式：

~~~go
for 初始化语句; 条件语句; 修饰语句 {}
~~~

示例 5.6 for1.go

~~~go
package main

import "fmt"

func main() {
	for i := 0; i < 5; i++ {
		fmt.Printf("This is the %d iteration\n", i)
	}
}
~~~

输出：

~~~
This is the 0 iteration
This is the 1 iteration
This is the 2 iteration
This is the 3 iteration
This is the 4 iteration
~~~

特别注意，永远不要在循环体内修改计数器。  

同时使用多个计数器：

~~~go
for i, j := 0, N; i < j; i, j = i+1, j-1 {}
~~~

for 循环嵌套使用：

~~~go
for i:=0; i<5; i++ {
	for j:=0; j<10; j++ {
		println(j)
	}
}
~~~

ASCII 编码的字符占用 1 个字节，既每个索引都指向不同的字符，而非 ASCII 编码的字符（占有 2 到 4 个字节）不能单纯地使用索引来判断是否为同一个字符。  


#### 5.4.2 基于条件判断的迭代

for 结构的第二种形式是没有头部的条件判断迭代（类似其它语言中的 while 循环），基本形式为：for 条件语句 {}。  

您也可以认为这是没有初始化语句和修饰语句的 for 结构，因此 <code>;;</code> 便是多余的了。  

Listing 5.8 for2.go

~~~go
package main

import "fmt"

func main() {
    var i int = 5

    for i >= 0 {
        i = i - 1
        fmt.Printf("The variable i is now: %d\n", i)
    }
}
~~~

输出：

~~~
The variable i is now: 4
The variable i is now: 3
The variable i is now: 2
The variable i is now: 1
The variable i is now: 0
The variable i is now: -1
~~~


#### 5.4.3 无限循环

条件语句是可以被省略的，如 <code>i:=0; ; i++</code> 或 <code>for { }</code> 或 <code>for ;; { }</code>（;; 会在使用 gofmt 
时被移除）：这些循环的本质就是无限循环。
最后一个形式也可以被改写为 <code>for true { }</code>，但一般情况下都会直接写 <code>for { }</code>。  

如果 for 循环的头部没有条件语句，那么就会认为条件永远为 true，因此循环体内必须有相关的条件判断以确保会在某个时刻退出循环。  

想要直接退出循环体，可以使用 break 语句（第 5.5 节）或 return 语句直接返回（第 6.1 节）。  

但这两者之间有所区别，break 只是退出当前的循环体，而 return 语句提前对函数进行返回，不会执行后续的代码。  

无限循环的经典应用是服务器，用于不断等待和接受新的请求。

~~~go
for t, err = p.Token(); err == nil; t, err = p.Token() {
    ...
}
~~~


#### 5.4.4 for-range 结构

可以迭代任何一个集合（包括数组和 map，详见第 7 和 8 章）。  

语法上很类似其它语言中 foreach 语句，但您依旧可以获得每次迭代所对应的索引。一般形式为：for ix, val := range coll { }。


要注意的是，val 始终为集合中对应索引的值拷贝，因此它一般只具有只读性质，对它所做的任何修改都不会影响到集合中原有的值（译者注：如果 val 为指针，则会产生指针的拷贝，依旧可以修改集合中的原值）。  

迭代 Unicode 编码的字符串：

~~~go
for pos, char := range str {
...
}
~~~

每个 rune 字符和索引在 for-range 循环中是一 一对应的。它能够自动根据 UTF-8 规则识别 Unicode 编码的字符。  

常用英文字符使用 1 个字节表示，而汉字（译者注：严格来说，“Chinese: 日本語” 的 Chinese 应该是 Japanese）使用 3 个字符表示。


#### 5.5. Break 与 continue

一个 break 的作用范围为该语句出现后的最内部的结构，它可以被用于任何形式的 for 循环（计数器、条件判断等）。但在 switch 或 select 语句中（详见第 13 章），break 
语句的作用结果是跳过整个代码块，执行后续的代码。  

关键字 continue 忽略剩余的循环体而直接进入下一次循环的过程，但不是无条件执行下一次循环，执行之前依旧需要满足循环的判断条件。  


#### 5.6. 标签与 goto

特别注意 使用标签和 goto 语句是不被鼓励的：它们会很快导致非常糟糕的程序设计，而且总有更加可读的替代方案来实现相同的需求。

定义但未使用标签会导致编译错误：label … defined and not used。  

如果您必须使用 goto，应当只使用正序的标签（标签位于 goto 语句之后），但注意标签和 goto 语句之间不能出现定义新变量的语句，否则会导致编译失败。  

continue 示例：

~~~go
package main

import "fmt"

func main() {

LABEL1:
    for i := 0; i <= 5; i++ {
        for j := 0; j <= 5; j++ {
            if j == 4 {
                continue LABEL1
            }
            fmt.Printf("i is: %d, and j is: %d\n", i, j)
        }
    }

}
~~~

break 示例：

~~~go
package main

func main() {
    i:=0
    HERE:
        print(i)
        i++
        if i==5 {
            return
        }
        goto HERE
}
~~~
