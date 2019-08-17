#### 第六章 函数

#### 6.1 介绍

函数的执行通过 return 来退出，return 语句可以带有零个或多个参数；这些参数将作为返回值供调用者使用。
简单的 return 可以用来结束 for 死循环，或者结束一个协程（goroutine）。  

Go 的函数类型[三种]：

- 普通的带有名字的函数
- 匿名函数或者 lambda 函数
- 方法（Methods）


除了 main()、init() 函数外，其它所有类型的函数都可以有参数与返回值。函数参数、返回值以及它们的类型被统称为函数签名。  

函数语法：

~~~go
func g() {
	
}
~~~

函数被调用的基本格式如下：

~~~go
pack1.Function(arg1, arg2, ..., argn)
~~~

Function 是 pack1 包里面的一个函数，括号里的是被调用函数的实参（argument）；这些值被传递给被调用函数的形参（parameter）。  

理论上，函数调用其他函数的次数是无穷的（直到函数调用栈被耗尽）。  


函数调用例子：

~~~go
package main

func main() {
	printf("In main befor calling greeting")
	greeting()
	printfln("In main after calling greeting")
}

func greeting() {
	println("In greeting: Hi")
}
~~~

代码输出：

~~~
In main before calling greeting
In greeting: Hi!
In main after calling greeting
~~~

函数可以将其他函数调用作为它的参数，只要这个被调用函数的返回值个数、返回值类型和返回值的顺序与调用函数所需求的实参是一致的，  

例如：假设 f1 需要 3 个参数 f1(a, b, c int)，同时 f2 返回 3 个参数 f2(a, b int) (int, int, int)，就可以这样子调用 f1：f1(f2(a, b))。  

函数重载（function overloading）：可以编写多个同名函数，只要它们拥有不同的形参与/或者不同的返回值，在 Go 里面函数重载是不被允许的。这将导致一个编译错误：

~~~
funcName redeclared in this book, previous declaration at lineno
~~~

Go 语言不支持这项特性的主要原因是函数重载需要进行多余的类型匹配影响性能；没有重载意味着只是一个简单的函数调度。
所以你需要给不同的函数使用不同的名字，我们通常会根据函数的特征对函数进行命名。  

如果需要申明一个在外部定义的函数，你只需要给出函数名与函数签名，不需要给出函数体：

~~~go
func flushICache(begin, end uintptr) // implemented externally
~~~

函数也可以以申明的方式被使用，作为一个函数类型，就像：

~~~go
type binOp func(int, int) int
~~~

在这里，不需要函数体 {}。  

函数值（functions value）之间可以相互比较：如果它们引用的是相同的函数或者都是 nil 的话，则认为它们是相同的函数。  

函数不能在其它函数里面声明（不能嵌套），通过使用匿名函数是例外。  

目前 Go 没有泛型（generic）的概念，不支持支持多种类型的函数。但可以通过接口（interface），特别是空接口与类型选择（type switch）与/或者通过使用反射（reflection）来实现相似的功能。
但会增加代码复杂度，性能也会降低。  


#### 6.2 函数参数与返回值

Go 可以定义没有形参名的函数，只有相应的形参类型，像这样：func f(int, int, float64)。  

没有参数的函数通常被称为 niladic 函数（niladic function），就像 main.main()。


#### 6.2.1 按值传递（call by value） 按引用传递（call by reference）

Go 默认使用按值传递来传递参数，也就是传递参数的副本。函数接收参数副本之后，在使用变量的过程中可能对副本的值进行更改，但不会影响到原来的变量，比如 Function(arg1)。  

引用传值：&variable。例如 Function(&arg1),此时传递给函数的是一个指针。  

引用传值传递给函数的是一个指针，指针的值（一个地址）会被复制，但指针的值所指向的地址上的值不会被复制；通过这个指针的值来修改这个值所指向的地址上的值。    

指针也是变量类型，有自己的地址和值，通常指针的值指向一个变量的地址。所以，按引用传递也是按值传递。  

传递指针（一个 32 位或者 64 位的值）的消耗都比传递副本来得少。  

在函数调用时，像切片（slice）、字典（map）、接口（interface）、通到（channel）的引用类型默认使用引用传递（即使没有显示的支出指针）。  


示例 6.2 simple_function.go

~~~go
package main

import "fmt"

func main() {
    fmt.Printf("Multiply 2 * 5 * 6 = %d\n", MultiPly3Nums(2, 5, 6))
    // var i1 int = MultiPly3Nums(2, 5, 6)
    // fmt.Printf("MultiPly 2 * 5 * 6 = %d\n", i1)
}

func MultiPly3Nums(a int, b int, c int) int {
    // var product int = a * b * c
    // return product
    return a * b * c
}
~~~

输出显示：

~~~
Multiply 2 * 5 * 6 = 60
~~~


如果一个函数需要返回四到五个值，我们可以传递一个切片给函数（如果返回值具有相同类型）或者是传递一个结构体（如果返回值具有不同的类型）。
因为传递一个指针允许直接修改变量的值，消耗也更少。


#### 6.2.2 命名的返回值（named return variables）

当需要返回多个非命名返回值时，需要使用 () 把它们括起来，比如 (int, int)。  

命名返回值作为结果形参（result parameters）被初始化为相应类型的零值，当需要返回的时候，我们只需要一条简单的不带参数的 return 语句。
需要注意的是，即使只有一个命名返回值，也需要使用 () 括起来（参考 第 6.6 节的 fibonacci.go 函数）。  

示例 6.3 multiple_return.go

~~~go
package main

import "fmt"

var num int = 10
var numx2, numx3 int

func main() {
    numx2, numx3 = getX2AndX3(num)
    PrintValues()
    numx2, numx3 = getX2AndX3_2(num)
    PrintValues()
}

func PrintValues() {
    fmt.Printf("num = %d, 2x num = %d, 3x num = %d\n", num, numx2, numx3)
}

func getX2AndX3(input int) (int, int) {
    return 2 * input, 3 * input
}

func getX2AndX3_2(input int) (x2 int, x3 int) {
    x2 = 2 * input
    x3 = 3 * input
    // return x2, x3
    return
}
~~~

输出结果：

~~~
num = 10, 2x num = 20, 3x num = 30    
num = 10, 2x num = 20, 3x num = 30 
~~~

警告：

- return 或 return var 都是可以的。
- 不过 return var = expression（表达式） 会引发一个编译错误：syntax error: unexpected =, expecting semicolon or newline or }。


任何一个非命名返回值在 return 语句里面都要明确指出包含返回值的变量或是一个可计算的值。  

尽量使用命名返回值：会使代码更清晰、更简短，同时更加容易读懂。  


#### 6.2.3 空白符（blank identifier）

空白符用来匹配一些不需要的值，然后丢弃掉。


#### 6.2.4 改变外部变量（outside variable）

传递指针给函数不但可以节省内存（因为没有复制变量的值），而且赋予了函数直接修改外部变量的能力，所以被修改的变量不再需要使用 return 返回。

~~~go
package main

import (
    "fmt"
)

// this function changes reply:
func Multiply(a, b int, reply *int) {
    *reply = a * b
}

func main() {
    n := 0
    reply := &n
    Multiply(10, 5, reply)
    fmt.Println("Multiply:", *reply) // Multiply: 50
}
~~~