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


#### 6.3. 传递变长参数

如果函数的最后一个参数是采用 ...type 的形式，那么这个函数就可以处理一个变长的参数，这个长度可以为 0，这样的函数称为变参函数。  

~~~go
func myFunc(a, b, arg ...int) {}
~~~

这个函数接受一个类似某个类型的 slice 的参数（详见第 7 章），该参数可以通过第 5.4.4 节中提到的 for 循环结构迭代。  

示例函数和调用：

~~~go
func Greeting(prefix string, who ...string)
Greeting("hello:", "Joe", "Anna", "Eileen")
~~~

在 Greeting 函数中，变量 who 的值为 []string{"Joe", "Anna", "Eileen"}。  

如果参数被存储在一个 slice 类型的变量 slice 中，则可以通过 slice... 的形式来传递参数调用变参函数。  

示例 6.7 varnumpar.go

~~~go
package main

import "fmt"

func main() {
    x := min(1, 3, 2, 0)
    fmt.Printf("The minimum is: %d\n", x)
    slice := []int{7,9,3,5,1}
    x = min(slice...)
    fmt.Printf("The minimum in the slice is: %d", x)
}

func min(s ...int) int {
    if len(s)==0 {
        return 0
    }
    min := s[0]
    for _, v := range s {
        if v < min {
            min = v
        }
    }
    return min
}
~~~

输出：

~~~go
The minimum is: 0
The minimum in the slice is: 1
~~~


####  6.4. defer 和追踪

关键字 defer 允许我们推迟到函数返回之前（或任意位置执行 return 语句之后）一刻才执行某个语句或函数（为什么要在返回之后才执行这些语句？因为 return 语句同样可以包含一些操作，而不是单纯地返回某个值）。  

defer 一般用于释放某些已分配的资源。

示例 6.8 defer.go：

~~~go
package main
import "fmt"

func main() {
    function1()
}

func function1() {
    fmt.Printf("In function1 at the top\n")
    defer function2()
    fmt.Printf("In function1 at the bottom!\n")
}

func function2() {
    fmt.Printf("function2: Deferred until the end of the calling function!")
}
~~~

输出：

~~~go
In Function1 at the top
In Function1 at the bottom!
Function2: Deferred until the end of the calling function!
~~~

defer 语句可以接受参数。

~~~go
func a() {
    i := 0
    defer fmt.Println(i)
    i++
    return
}
~~~

当有多个 defer 行为被注册时，它们会以逆序执行（类似栈，即后进先出）：

~~~go
func f() {
    for i := 0; i < 5; i++ {
        defer fmt.Printf("%d ", i)
    }
}
~~~

输出：

~~~
4 3 2 1 0
~~~

1. 关闭文件流

~~~go
// open a file
defet file.Close()
~~~

2. 解锁一个加锁的资源

~~~go
mu.Lock()
defer mu.Unlock()
~~~

3. 打印最终报告

~~~go
printHeader()
defer printFooter()
~~~

4. 关闭数据库链接

~~~go
// open a database connection
defer disconnectFromDB()
~~~

合理使用 defer 语句能够使得代码更加简洁。  

以下代码模拟了上面描述的第 4 种情况：

~~~go
package main

import "fmt"

func main() {
    doDBOperations()
}

func connectToDB() {
    fmt.Println("ok, connected to db")
}

func disconnectFromDB() {
    fmt.Println("ok, disconnected from db")
}

func doDBOperations() {
    connectToDB()
    fmt.Println("Defering the database disconnect.")
    defer disconnectFromDB() //function called here with defer
    fmt.Println("Doing some DB operations ...")
    fmt.Println("Oops! some crash or network error ...")
    fmt.Println("Returning from function here!")
    return //terminate the program
    // deferred function executed here just before actually returning, even if
    // there is a return or abnormal termination before
}
~~~

输出：

~~~
ok, connected to db
Defering the database disconnect.
Doing some DB operations ...
Oops! some crash or network error ...
Returning from function here!
ok, disconnected from db
~~~

##### 使用 defer 语句实现代码追踪

代码追踪方案就是在进入和离开某个函数打印相关的消息。以下函数：

~~~go
func trace(s string) { fmt.Println("entering:", s) }
func untrace(s string) { fmt.Println("leaving:", s) }
~~~


##### 使用 defer 语句来记录函数的参数与返回值


示例 6.12 defer_logvalues.go：

~~~go
package main

import (
    "io"
    "log"
)

func func1(s string) (n int, err error) {
    defer func() {
        log.Printf("func1(%q) = %d, %v", s, n, err)
    }()
    return 7, io.EOF
}

func main() {
    func1("Go")
}
~~~

输出：

~~~
Output: 2011/10/04 10:46:11 func1("Go") = 7, EOF
~~~


 #### 6.5. 内置函数
 

<table>
<thead>
  <tr>
      <th>名称</th>
      <th>说明</th>
  </tr>
</thead>
<tbody>
  <tr>
      <td>close</td>
      <td>用于管道通信</td>
  </tr>
  <tr>
      <td>len、cap</td>
      <td>len 用于返回某个类型的长度或数量（字符串、数组、切片、map 和管道）；cap 是容量的意思，用于返回某个类型的最大容量（只能用于切片和 map）</td>
  </tr>
  <tr>
      <td>new、make</td>
      <td>
          new 和 make 均是用于分配内存：new 用于值类型和用户定义的类型，如自定义结构，make 用于内置引用类型（切片、map 和管道）。
          它们的用法就像是函数，但是将类型作为参数：new (type)、make (type)。
          new (T) 分配类型 T 的零值并返回其地址，也就是指向类型 T 的指针（详见第 10.1 节）。
          它也可以被用于基本类型：<code>v := new(int)</code>。make (T) 返回类型 T 的初始化之后的值，因此它比 new 进行更多的工作（详见第 7.2.3/4 节、第 8.1.1 节和第 14.2.1 节）<strong>new () 是一个函数，不要忘记它的括号</strong>
      </td>
  </tr>
  <tr>
      <td>copy、append</td>
      <td>用于复制和连接切片</td>
  </tr>
  <tr>
      <td>panic、recover</td>
      <td>两者均用于错误处理机制</td>
  </tr>
  <tr>
      <td>print、println</td>
      <td>底层打印函数（详见第 4.2 节），在部署环境中建议使用 fmt 包</td>
  </tr>
  <tr>
      <td>complex、real imag</td>
      <td>用于创建和操作复数（详见第 4.5.2.2 节）</td>
  </tr>
</tbody>
</table>
          

#### 6.6. 递归函数

当一个函数在其函数体内调用自身，则称之为递归。  

最经典的例子便是计算斐波那契数列，即前两个数为 1，从第三个数开始每个数均为前两个数之和。  

数列如下所示：

~~~
1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, 610, 987, 1597, 2584, 4181, 6765, 10946, …
~~~

下面的程序可用于生成该数列（示例 6.13 fibonacci.go）：

~~~go
package main

import "fmt"

func main() {
    result := 0
    for i := 0; i <= 10; i++ {
        result = fibonacci(i)
        fmt.Printf("fibonacci(%d) is: %d\n", i, result)
    }
}

func fibonacci(n int) (res int) {
    if n <= 1 {
        res = 1
    } else {
        res = fibonacci(n-1) + fibonacci(n-2)
    }
    return
}
~~~

输出：

~~~
fibonacci(0) is: 1
fibonacci(1) is: 1
fibonacci(2) is: 2
fibonacci(3) is: 3
fibonacci(4) is: 5
fibonacci(5) is: 8
fibonacci(6) is: 13
fibonacci(7) is: 21
fibonacci(8) is: 34
fibonacci(9) is: 55
fibonacci(10) is: 89
~~~


在使用递归函数时经常会遇到的一个重要问题就是栈溢出：一般出现在大量的递归调用导致的程序栈内存分配耗尽。
这个问题可以通过一个名为懒惰求值的技术解决，在 Go 语言中，我们可以使用管道（channel）和 goroutine（详见第 14.8 节）来实现。
练习 14.12 也会通过这个方案来优化斐波那契数列的生成问题。  

Go 语言中也可以使用相互调用的递归函数：多个函数之间相互调用形成闭环。
因为 Go 语言编译器的特殊性，这些函数的声明顺序可以是任意的。
下面这个简单的例子展示了函数 odd 和 even 之间的相互调用（示例 6.14 mut_recurs.go）：

~~~go
package main

import (
    "fmt"
)

func main() {
    fmt.Printf("%d is even: is %t\n", 16, even(16)) // 16 is even: is true
    fmt.Printf("%d is odd: is %t\n", 17, odd(17))
    // 17 is odd: is true
    fmt.Printf("%d is odd: is %t\n", 18, odd(18))
    // 18 is odd: is false
}

func even(nr int) bool {
    if nr == 0 {
        return true
    }
    return odd(RevSign(nr) - 1)
}

func odd(nr int) bool {
    if nr == 0 {
        return false
    }
    return even(RevSign(nr) - 1)
}

func RevSign(nr int) int {
    if nr < 0 {
        return -nr
    }
    return nr
}
~~~

#### 6.7. 将函数作为参数

函数可以作为其它函数的参数进行传递，然后在其它函数内调用执行，一般称之为回调。  

下面是一个将函数作为参数的简单例子（function_parameter.go）：

~~~go
package main

import (
    "fmt"
)

func main() {
    callback(1, Add)
}

func Add(a, b int) {
    fmt.Printf("The sum of %d and %d is: %d\n", a, b, a+b)
}

func callback(y int, f func(int, int)) {
    f(y, 2) // this becomes Add(1, 2)
}
~~~

输出：

~~~
The sum of 1 and 2 is: 3
~~~

将函数作为参数的最好的例子是函数 strings.IndexFunc()：  

该函数的签名是 func IndexFunc(s string, f func(c int) bool) int，它的返回值是在函数 f(c) 返回 true、-1 或从未返回时的索引值。  


#### 6.8. 闭包

匿名函数还可以配合 go 关键字来作为 goroutine 使用（详见第 14 章和第 16.9 节）。  

匿名函数也称闭包（函数式语言的术语）：它们被允许调用定义在其它环境下的变量。闭包可使得某个
函数捕捉到一些外部状态，例如：函数被创建时的状态。另一种表示方式为：一个闭包继承了函数所声明时的作
用域。这种状态（作用域内的变量）都被共享到闭包的环境中，因此这些变量可以在闭包中被操作，直到被销
毁。闭包经常被用作包装函数：它们会预先定义好 1 个或多个参数以用于包装。

##### defer 语句和匿名函数

关键字 defer （详见第 6.4 节）经常配合匿名函数使用，它可以用于改变函数的命名返回值。  

匿名函数

~~~go
func(x, y int) int {return x + y}
~~~

这样子的函数不能够独立存在（编译器会返回错误：non-declaration statemet outside function body），
匿名函数可保存于变量中，将函数的地址保存在变量中：fplus := func(x, y int) int {return x + y}，
然后通过变量名对函数进行调用：fplus(3, 4)。  

匿名函数直接调用：func(x, y int) int {reeturn x + y} (3, 4)。  

因为匿名函数没有名称，花括号 {} 涵盖着函数体，最后的一对括号表示对该匿名函数的调用。