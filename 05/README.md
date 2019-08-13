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