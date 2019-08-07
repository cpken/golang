### 第四章 基本结构和基本数据类型

- 4.1 文件名、关键字与标识符
- 4.2 Go 程序的基本结构和要素
- 4.3 常量
- 4.4 变量
- 4.5 基本类型和运算符
- 4.6 字符串
- 4.7 strings 和 strconv 包
- 4.8 时间和日期
- 4.9 指针


#### 4.1 文件名、关键字与标识符

Go 文件命名规则
- 小写字母
- 小写字母+下划线
- 文件名不包含空格或其他特殊字符

~~~
scanner.go
scanner_test.go
~~~

Go 语言区分大小写。  

以下是无效的标识符：
- 1ab（以数字开头）
- case （Go 语言的关键字）
- a+b（运算符是不允许的）

*_* 被称为空白标识符。可用于变量的声明或赋值（任何类型都可赋值于它），但赋予的值将被抛弃，因此这些值不能在后续的代码中使用，也不可以使用这个标识符作为变量对其它变量进行赋值或运算。  

#### Go 的 25 个关键字或保留字

<table>
    <tbody>
        <tr>
            <td>break</td>
            <td>default</td>
            <td>func</td>
            <td>interface</td>
            <td>select</td>
        </tr>
        <tr>
            <td>case</td>
            <td>defer</td>
            <td>go</td>
            <td>map</td>
            <td>struct</td>
        </tr>
        <tr>
            <td>chan</td>
            <td>else</td>
            <td>goto</td>
            <td>package</td>
            <td>switch</td>
        </tr>
        <tr>
            <td>const</td>
            <td>fallthrough</td>
            <td>if</td>
            <td>range</td>
            <td>type</td>
        </tr>
        <tr>
            <td>continue</td>
            <td>for</td>
            <td>import</td>
            <td>return</td>
            <td>var</td>
        </tr>
    </tbody>
</table>


#### Go 的 36 个预定义标识符

<table>
    <tbody>
        <tr>
            <td>append</td>
            <td>bool</td>
            <td>byte</td>
            <td>cap</td>
            <td>close</td>
            <td>complex</td>
            <td>complex64</td>
            <td>complex128</td>
            <td>uint16</td>
        </tr>
        <tr>
            <td>copy</td>
            <td>false</td>
            <td>float32</td>
            <td>float64</td>
            <td>imag</td>
            <td>int</td>
            <td>int8</td>
            <td>int16</td>
            <td>uint32</td>
        </tr>
        <tr>
            <td>int32</td>
            <td>int64</td>
            <td>iota</td>
            <td>len</td>
            <td>make</td>
            <td>new</td>
            <td>nil</td>
            <td>panic</td>
            <td>uint64</td>
        </tr>
        <tr>
            <td>print</td>
            <td>println</td>
            <td>real</td>
            <td>recover</td>
            <td>string</td>
            <td>true</td>
            <td>uint</td>
            <td>uint8</td>
            <td>uintptr</td>
        </tr>
    </tbody>
</table>


程序一般由关键字、常量、变量、运算符、类型和函数组成。  

程序中用到的分隔符：<code>()</code>、<code>[]</code>、<code>{}</code>。  

程序中用到的标点符号：<code>.</code>、<code>,</code>、<code>;</code>、<code>:</code> 和 <code>...</code>。  

Go 语言不鼓励多行代码写在同一行。


#### 4.2. Go 程序的基本结构和要素
##### 4.2.1 包的概念、导入与可见性

程序由包组成，包可是自身或从外部导入。  
源文件第一行必须要指明该文件属于哪个包，如：<code>package main</code>。<code>package main</code> 表示一个可独立执行的程序，每个 Go 应用程序都包含一个名为 
<code>main</code> 的包。  
所有的包名都应该使用小写字母。

##### 标准库

可以直接使用的包称为标准库。  

标准库目录

| windows        | Linux 64位      |  Linux 32位    |
| :------------: |:---------------:| :------------:|
| pkg\windows_386| pkg\linux_amd64 | pkg\linux_386 |

标准包存放在 <code>$GOROOT/pkg/$GOOS_$GOARCH/</code> 目录下。  

属于同一个包的源文件必须全部被一起编译，一个包即是编译时的一个单元，因此根据惯例，每个目录都只包含一个包。  

如果对一个包进行更改或重新编译，所有引用了这个包的客户端程序都必须全部重新编译。  

Go 中的包模型采用了显示依赖关系的机制来达到快速编译的目的，编译器会从后缀名为 <code>.o</code> 的对象文件（需要且只需要这个文件）中提取传递依赖类型的信息。  

如果 <code>A.go</code> 依赖 <code>B.go</code>，而 <code>B.go</code> 又依赖 <code>C.go</code>：  
- 编译<code>C.go</code>，<code>B.go</code>，然后是 <code>A.go</code>。
- 为了编译 <code>A.go</code>，编辑器读取的是 <code>B.go</code> 而不是 <code>C.go</code>。

这种编译机制对于编译大型的项目时可以显著地提升编译速度。  

每一段代码只会被编译一次。  

一个 Go 程序是通过 <code>import</code> 关键字将一组包链在一起。  

导入多个包：
~~~go
import "fmt"
import "os"
~~~
或
~~~go
import "fmt"; import "os"
~~~

但是还有更短且更优雅的方法（被称为因式分解关键字，该方法同样适用于 const、var 和 type 的声明或定义）：
~~~go
import (
  "fmt"
  "os"
)
~~~

更短的形式，但使用 gofmt 后将会被强制换行：
~~~go
import ("fmt"; "os")
~~~

如果包名不是以 <code>.</code> 或 <code>/</code> 开头，如 <code>"fmt"</code> 或者 <code>"container/list"</code>，则 Go 会在全局文件进行查找；如果包名以
 <code>./</code> 开头，则 Go 会在相对目录中查找；如果包名以 <code>/</code> 开头（在 Windows 
下也可以这样使用），则会在系统的绝对路径中查找。  

导入包即等同于包含了这个包的所有的代码对象。  

除了符号 <code>_</code>，包中所有代码对象的标识符必须是唯一的，以避免名称冲突。但是相同的标识符可以在不同的包中使用，因为可以使用包名来区分它们。  


##### 可见性规则

当标识符（包括常量、变量、类型、函数名、结构字段等等）以一个大写字母开头，如：Group1，那么使用这种形式的标识符的对象就可以被外部包的代码所使用（客户端程序需要先导入这个包），这被称为导出（像面向对象语言中的 
public）；标识符如果以小写字母开头，则对包外是不可见的，但是他们在整个包的内部是可见并且可用的（像面向对象语言中的 private ）。  

包也可以作为命名空间使用，帮助避免命名冲突（名称冲突）：两个包中的同名变量的区别在于他们的包名，例如 <code>pack1.Thing</code> 和 <code>pack2.Thing</code>。  

包名冲突可以通过设置包的别名来解决。

~~~go
package main

import fm "fmt" // alias3

func main() {
   fm.Println("hello, world")
}
~~~

##### 注意事项
如果你导入一个包而没有使用它，会在构建程序时报错，如 "imported and not used: os"，这正是遵循了 Go 的格言："没有不必要的代码！"。  

##### 包的分级声明和初始化

在使用 <code>import</code> 导入包之后定义或声明 0 个或多个常量（const）、变量（var）和类型（type），这些对象的作用域都是全局的（在本包范围内）。  

#### 4.2.2 函数

函数定义：

~~~go
func functionName()
~~~

在括号 <code>()</code> 中写入 0 个或多个函数的参数（使用逗号 <code>,</code> 分隔），每个参数的名称后面必须紧跟其类型。  

<code>main</code> 函数是每一个可执行程序所必须包含的，一般来说都是在启动后第一个执行的函数（如果有 <code>init ()</code> 函数则会先执行该函数）。
如果你的 <code>main</code> 包的源代码没有包含 <code>main</code> 函数，则会引发构建错误 "undefined: main.main。main" 函数既没有参数，也没有返回类型（与 C 
家族中的其它语言恰好相反）。
如果你不小心为 <code>main</code> 函数添加了参数或者返回类型，将会引发构建错误：

~~~
func main must have no arguments and no return values results.
~~~

在程序开始执行并完成初始化后，第一个调用（程序的入口点）的函数是 <code>main.main()</code>（如：C 语言），该函数一旦返回就表示程序已成功执行并立即退出。  

函数里的代码（函数体）使用大括号 <code>{}</code> 括起来。

左大括号 <code>{</code> 必须与方法的声明放在同一行，这是编译器的强制规定，否则你在使用 gofmt 时就会出现错误提示：

~~~
`build-error: syntax error: unexpected semicolon or newline before {`
~~~

（这是因为编译器会产生 <code>func main() ;</code> 这样的结果，很明显这错误的）  


Go 语言虽然看起来不使用分号作为语句的结束，但实际上这一过程是由编译器自动完成，因此才会引发像上面这样的错误。

右大括号 <code>}</code> 需要被放在紧接着函数体的下一行。如果你的函数非常简短，你也可以将它们放在同一行：

~~~go
func Sum(a, b innt) int { return a + b }
~~~


只有当某个函数需要被外部包调用的时候才使用大写字母开头，并遵循 Pascal 命名法；否则就遵循骆驼命名法，即第一个单词的首字母小写，其余单词的首字母大写。  

当被调用函数的代码执行到结束符 <code>}</code> 或返回语句时就会返回，然后程序继续执行调用该函数之后的代码。  

程序正常退出的代码为 0 即 "Program exited with code 0"；如果程序因为异常而被终止，则会返回非零值，如：1。这个数值可以用来测试是否成功执行一个程序。


#### 4.2.3 注释

<code>//</code> 单行注释。  
<code>/**/</code> 多行注释【块注释】。  

多行注释不可以嵌套使用，多段注释之间应该用空行分阁加以区分。  

几乎所有全局作用域的类型、常量、变量、函数和被导出的对象都应该有一个合理的注释。如果这种注释（称为文档注释）出现在函数面前，例如函数 Abcd，则要以 <code>"Abcd...""</code>。


#### 4.2.4 类型

使用 var 声明的变量的值会自动初始化为该类型的的零值。  

类型定义了某个变量的值的集合与对其进行操作的集合。

#### 基本类型

- int
- float
- bool
- string


#### 结构化（复合）类型

- struct
- array
- slice
- map
- channel


#### 描述类型的行为

- interface


结构化的类型没有真正的值，它使用 nil 作为默认值。Go 语言中不存在类型继承。  

函数的返回类型声明要写在函数名和可选的参数列表之后，例如：

~~~go
func FunctionName (a typea, b typeb) typeFunc
~~~

你可以在函数体的某处返回使用类型为 typeFunc 的变量 var：
~~~go
return var
~~~

一个函数可以拥有多返回值，返回类型之间需要使用逗号分割，并使用小括号 <code>()</code> 将它们括起来，如：

~~~go
func FunctionName (a typea, b typeb) (t1 type1, t2 type2)
~~~

返回的形式：

~~~go
return var1, var2
~~~

这种多返回值一般用于判断某个函数是否执行成功（true/false）或与其它返回值一同返回错误消息。  

使用 type 关键字定义类型，也可以定义已存在的类型的别名，如：

~~~go
type IZ int
~~~

这里并不是真正意义上的别名，因为使用这种方法定义之后的类型可以拥有更多的特性，且在类型转换时必须显示=式转换。  

下面的声明方式：

~~~go
var a IZ = 5
~~~

int 是变量 a 的底层类型，这也使得它们之间存在相互转换的可能。  

如果你有多个类型需要定义，可以使用因式分解关键字的方式，例如：

~~~go
type (
  IZ  int
  FZ  float64
  STR string
)
~~~

每个值都必须在经过编译后属于某个类型（编译器必须能够推断出所有值的类型），因为 Go 语言是一种静态型语言。


#### 4.2.5 Go 程序的一般结构

示例 4.4 gotemplate.go

~~~go
package main

import (
  "fmt"
)

const c = "C"

var v int = 5

type T struct{}

func init() { // initialization of package
}

func main() {
  var a int
  Func1()
  // ...
  fmt.Println(a)
}

func (t T) Method1() {
  // ...
}

func Func1() {// exported function Func1
  // ...
}
~~~

Go 程序的执行（程序启动）顺序如下：

1. 按顺序导入所有被 main 包引用的其它包，然后在每个包中执行如下流程：

2. 如果该包又导入了其它的包，则从第一步开始递归执行，但是每个包只会被导入一次。

3. 然后以相反的顺序在每个包中初始化常量和变量，如果该包含有 init 函数的话，则调用该函数。

4. 在完成这一切之后，main 也执行同样的过程，最后调用 main 函数开始执行程序。


#### 4.2.6 类型转换

Go 语言不存在隐式转换。

~~~go
valueOfTypeB = typeB(valueOfTypeA)
~~~

类型 B 的值 = 类型 B（类型 A 的值）  

示例：

~~~go
a := 5.0
b := int(a)
~~~

以上代码只能在定义正确的情况下转黄成功。
例如从一个取值范围较小的类型转换到一个取值范围较大的类型（例如将 int16 转换为 int32）。
当从一个取值范围较大的转换到取值范围较小的类型时（例如将 int32 转换为 int16 或将 float32 转换为 int），会发生精度丢失（截断）的情况。
当编译器捕捉到非法的类型转换时会引发编译时错误，否则将引发运行时错误。  

具有相同底层类型的变量之间可以互相转换：

~~~go
var a IZ = 5
c := int(a)
d := IZ(c)
~~~


#### 4.2.7 Go 命名规范

返回某个对象的函数或方法的名称一般使用名词，没有 <code>Get...</code> 之类的字符。  

修改对象，使用 <code>SetName</code>。  

大小写混合，如 MixedCaps 或 mixedCaps，不使用下划线分隔多个名称。


#### 4.3 常量

常量使用关键字 <code>const</code> 定义，用于存储不会改变的数据。  

存储在常量中的数据类型
- 布尔型
- 数字型（整数型、浮点型、复数）
- 字符串型 

常量的定义格式
~~~go
const identifier [type] = value
const Pi = 3.1415926
~~~

在 Go 语言中，可以省略类型说明符 <code>[type]</code>，因为编译器可以根据变量的值来推断类型。

- 显示类型定义：<code>const b string = "abc""</code>
- 隐式类型定义：<code>const b = "abc""</code>

一个没有指定类型的常量被使用时，会根据其使用环境而推断出它所需要具备的类型。
换句话说，未定义类型的常量会在必要时刻根据上下文来获得相关类型。

~~~go
var n int
f(n + 5) // 无类型的数字型常量 "5" 它的类型在这里变成了 int
~~~

常量的值必须在编译时就确定。

- 正确的做法：<code>const c1 = 2/3</code>
- 错误的做法：<code>const c2 = getNumber()</code> // 引发构建错误：<code>getNumber() used as value</code>

因为在编译期间自定义函数均属于未知，因此无法用于常量的赋值，但内置函数可以使用，如：len()。  

数字型的常量是没有大小和符号的，并且可以使用任何精度而不会导致溢出：

~~~go
const Ln2= 0.693147180559945309417232121458\
            176568075500134360255254120680009
const Log2E= 1/Ln2 // this is a precise reciprocal
const Billion = 1e9 // float constant
const hardEight = (1 << 100) >> 97
~~~

反斜杠 <code> \ </code> 可以在常量表达式中作为多行的连接符使用。  

常量赋值给一个精度过小的数字型变量时，会导致数值溢出，而引发错误。  

常量允许并行赋值的形式：

~~~go
const beef, two, c = "eat", 2, "veg"
const Monday, Tuesday, Wednesday, Thursday, Friday, Saturday = 1, 2, 3, 4, 5, 6
const (
    Monday, Tuesday, Wednesday = 1, 2, 3
    Thursday, Friday, Saturday = 4, 5, 6
)
~~~

常量枚举：

~~~go
const (
  Unknown = 0
  Female  = 1
  Male    = 2
)
~~~

例子，<code>iota</code> 可以被用作枚举值：

~~~go 
const (
  a = iota
  b = iota
  c = iota 
)
~~~

在没遇到一个新的常量块或单个常量声明时，<code>iota</code> 都会重置为0（每遇到一次 const 关键，iota 就重置为 0）。  

常量是恒定不变的，无法在程序运行过程中修改它的值；修改怎会引发编译错误。  


#### 4.4 变量

变量声明 <code>var</code> 关键字：<code>var identifier type</code>。  

Go 声明变量时将变量的类型放在变量的名称的之后。  

指针类型声明：

~~~go
var a, b *int
~~~

示例

~~~go
var a   int
var b   bool
var str string
~~~

另一种写法：

~~~go
var (
  a   int
  b   bool
  str string
)
~~~

这种因式分解关键字的写法一般用于声明全局变量。  

当一个变量被声明之后，系统自动赋予它该类型的零值：int 为 0，float 为 0.0，bool 为 false，string 为空字符串，指针为 nil。
记住，所有的内存在 Go 中都是经过初始化的。  

变量命名规则：驼峰命名法，首字母小写，每个新单词的首字母大写。如 <code>numShips</code>。  

全局变量能够被外部所使用，需要将首字母也大写。


一个变量（常量、类型或函数）在程序中都有一定的作用范围，称之为作用域。
如果一个变量在函数体外声明，则被认为是全局变量，可以在整个包甚至外部包（被导出后）使用，不管你声明在哪个源文件里或在哪个源文件里调用该变量。  

在函数体内声明的变量称之为局部变量，它们的作用域只在函数体内，参数和返回值变量也是局部变量。
一般情况下，局部变量的作用域可以通过代码块（用大括号括起来的部分）判断。  

变量的标识符必须是唯一的。  

如果内部代码块出现了与外部相同名称的变量，则会使用内部代码块的变量的值（结果内部代码块的执行后隐藏的外部同名变量又会出现，而内部同名变量则被释放），只会影响内部代码块的局部变量。

变量可以编译期间就被赋值，赋值给变量使用运算符号 <code>=</code>，也可以在运行时对变量进行赋值操作。  

示例：

~~~go
a = 15
b = false
~~~

一般情况下，当变量 a 和变量 b 之间类型相同时，才能进行如 <code>a = b</code> 的赋值。  

声明与赋值（初始化）语句也可以组合起来。
示例：

~~~go
var identifier [type] value
var a int = 15
var i = 5
var b bool = false
var str string = "Go says hello to the world~"
~~~

Go 在编译时就完成了类型推断过程，使用下面的代码形式来声明及初始化变量：

~~~go
var a   = 15
var b   = false
var str = "Go says hello to the world!" 
~~~

或：

~~~go
var (
  a        = 15
  b        = false
  str      = "Go says hello to the world!"
  numShips = 50
  city string
)
~~~

自动推断类型非任何时候都适用，当你想要给变量的类型并不是自动推断出的某种类型时，你需要显式指定变量的类型，例如：

~~~go
var n int64 = 2
~~~

<code>var a</code> 语法是错误的，因为编辑器无自动推断类型的一句。  

声明包级别的全局变量，例如：

~~~go
var (
  HOME   = os.Getenv("HOME")
  USER   = os.Getenv("USER")
  GOROOT = os.Getenv("GOROOT")  
)
~~~

在函数体内声明局部变量，可以使用简短声明语法 <code>:=</code>，例如：

~~~go
a := 1
~~~

下面例子
- <code>runtime</code> 获取操作系统类型
- <code>os</code> 包中的 <code>os.Getenv()</code> 获取环境变量中的值，并保存到 string 类型的局部变量 path 中。

~~~go
package main

import (
  "fmt"
  "runtime"
  "os"
)

func main() {
  var goos string = runtime.GOOS
  fmt.Printf("The operating system is: %s\n", goos)
  path := os.Getenv("PATH")
  fmt.Printf("Path is %s\n", path)
}
~~~


#### 4.4.2 值类型和引用类型

通过 &i 来获取变量 i 的内存地址。值类型的变量的值存储在栈中。  

内存地址被称为指针。  

同一个引用类型的指针指向的多个字可以是在连续的内存地址中（内存布局是连续的），
也会将这些字分散存放在内存中，每个字都指示了下一个字所在的内存地址。  

当使用赋值语句 <code>r2 = r1</code> 时，只有引用（地址）被复制。  

如果 r1 的值被改变了，这个值的所有引用都会指向被修改后的内容。  

在 Go 语言中，指针属于引用类型。  

其他引用类型
- slices
- maps
- channel

被引用的变量会存储在堆中，以便进行垃圾回收，且比栈拥有更大的内存空间。


#### 4.4.3 打印

函数 <code>Printf</code> 主要用于打印输出到控制台。大写字母表示可以外部使用。  

~~~go
func Printf(format string, list of variables to be printend)
~~~

格式化字符串可以含有一个或多个的格式化标识符。

- <code>%..</code> 可以被不同类型所对应的标识符替换
- <code>%s</code> 代表字符串标识符
- <code>%v</code> 代表使用类型的默认输出格式的标识符

这些标识符所对应的值从格式化字符串后的第一个逗号开始按照相同顺序添加，如果参数超过 1 个则同样需要使用逗号分隔。  


#### 4.4.4 简短形式，使用 := 赋值操作符

<code>:=</code> 赋值操作符只能被用在函数体内，而不能用于全局变量的声明与赋值。
其可以高效地创建一个新的变量，称之为初始化声明。

##### 注意事项

在同一代码块中，不可以再次对于相同名称的变量使用初始化声明，例如：<code>a := 50</code>，再进行 <code>a := 20</code>不是不允许的，
编辑器会提示错误 <code>no new variables on left side of :=</code>，但是 <code>a = 20</code> 是可以的，
因为这是给相同的变量赋予一个新的值。  

如果你在定义变量 a 之前使用它，则会得到编译错误 <code>undefined: a</code>。  

如果你声明一个局部变量却没有在相同的代码块中使用它，同样会得到编译错误，例如下例当中的变量 a：

~~~go
func main() {
  var a string = "abc"
  fmt.Println("hello, world")
}
~~~

尝试编译这段代码将得到错误 <code>a declared and not used</code>。  

此外，单纯地给 a 赋值也是不够的，这个值必须被使用，所以使用 <code>fmt.Println("hello, world", a)</code> 会移除错误。  

但是全局变量是允许声明但不使用。

简短形式的变量声明：

~~~go
var a, b, c int
~~~

多变量同一行赋值：

~~~go
a, b, c = 5, 7, "abc"
~~~

或

~~~go
a, b, c := 5, 7, "abc"
~~~

右边的这些值以相同的顺序赋值给左边的变量，所以 a 的值是 <code>5</code>，b 的值是 <code>7</code>，c 的值是 <code>"abc"</code>。

这被称为 **并行** 或 **同时** 赋值。  

如果你想要交换两个变量的值，则可以简单地使用 <code>a, b = b, a</code>。  

（在 GO 语言中，这样省去了使用交换函数的必要）  

空白标识符 <code> _ </code> 也被用于抛弃值，如值 <code>5</code> 在：<code>_, b = 5, 7</code> 中被抛弃。  

<code>_</code> 是一个只写变量，不能得到它的值。因为 Go 语言中必须使用所有被声明的变量，但是有时并不需要使用从一个函数得到的所有返回值。  

并行赋值也被用于当一个函数返回多个返回值时，
比如这里的 <code>val</code> 和错误 <code>err</code> 是通过调用 <code>Func1</code> 函数同时得到：<code>val, err 
= Func1(var1)</code>。


#### 4.4.5 init 函数

init 函数会在每个包完成初始化后自动执行，并且执行优先级高于 main 函数。  

每个源文件都只能包含一个 init 函数。初始化总是以单线程执行，并且按照包的依赖关系顺序执行。  

示例 4.6 init.go ：

~~~go
package trans

import "math"

var Pi float64

func init() {
  Pi = 4 * math.Atan(1) // init() function computes Pi
}
~~~

在它的 init 函数中计算变量 Pi 的初始值。  

示例 4.7 user_init.go 中导入了包 trans（需要 init.go 目录为 ./trans/init.go）并且使用到了变量 Pi：

~~~go
package main

import (
  "fmt"
  "./trans"
)

var twoPi = 2 * trans.Pi

func main() {
  fmt.Printf("2*Pi = %g\n", twoPi) // 2*Pi = 6.283185307179586
}
~~~

init 函数也经常被用在当一个程序开始之前调用后台执行的 goroutine，如下例子中的 <code>backend</code>：

~~~go
func init() {
  // setup preparations
  go backend()
}
~~~


#### 4.5 基本类型和运算符

一元运算符只可以用于一个值的操作（作为后缀），而二元运算符则可以和两个值或者操作数结合（作为中缀）。

#### 4.5.1 布尔类型 bool

布尔型的值只可以是常量 true 或 false。  

运算符相等 <code>==</code> 或不等 <code>!=</code> 进行比较并获得一个布尔型的值。在比较时，会同时比较类型与值，
相同返回 true，反则 false。  

示例：

~~~go
var aVar = 10
aVar == 5  -> false
aVar == 10 -> true
~~~

Go 对于值之间的比较有非常严格的限制，只有两个类型相同的值才可以进行比较，
接口比较必须都实现了相同的接口。
常量，必须和该常量类型相兼容的。
如果条件都不满足，则其中一个值的类型必须在被转换为和另外一个值的类型相同之后才可以进行比较。  

Go 语言中包含以下逻辑运算符：  

**非**运算符：<code>!</code>

~~~go
!T -> false
!F -> true
~~~

**非**运算符用于取得和布尔值相反的结果。  

**和**运算符：<code>&&</code>

~~~go
T && T -> true
T && F -> false
F && T -> false
F && F -> false
~~~

只有当两边的值都为 true 的时候，**和**运算符的结果才是 true。  

**或**运算符：<code>||</code>

~~~go
T || T -> true
T || F -> true
F || T -> true
F || F -> false
~~~

只有当两边的值都为 false 的时候，**或**运算符的结果才是 false，其中任意一边的值为 true 就能够使得该表达式的结果为 true。  

在 Go 语言中，&& 和 || 是具有快捷性质的运算符，当运算符左边表达式的值已经能够决定整个表达式的值的时候（&& 左边的值为 false，|| 左边的值为 true），运算符右边的表达式将不会被执行。
利用这个性质，如果你有多个条件判断，应当将计算过程较为复杂的表达式放在运算符的右侧以减少不必要的运算。  

利用括号同样可以升级某个表达式的运算优先级。  

在格式化输出时，你可以使用 %t 来表示你要输出的值为布尔型。  

布尔值（以及任何结果为布尔值的表达式）最常用在条件结构的条件语句中，例如：if、for 和 switch 结构（第 5 章）。  

对于布尔值的好的命名能够很好地提升代码的可读性，例如以 <code>is</code> 或者 <code>Is</code> 开头的 
<code>isSorted</code>、<code>isFinished</code>、<code>isVisible</code>，使用这样的命名能够在阅读代码的获得阅读正常语句一样的良好体验，例如标准库中的 
<code>unicode.IsDigit(ch)</code>（第 4.5.5 节）。


#### 4.5.2 数字类型

#### 4.5.2.1  整型 int 和浮点型 float

Go 语言支持整型和浮点型数字，并且原生支持复数，其中位的运算采用补码。  

Go 基于架构的类型，例如：int、uint 和 uintptr。  

架构类型的长度是根据运行程序所在的操作系统类型所决定的：

- <code>int</code> 和 <code>uint</code> 在 32 位操作系统上，均使用 32 位（4 个字节），在 64 位操作系统上，均使用 64 位（8 个字节）。
- <code>uintptr</code> 的长度被设定为足够存放一个指针即可。

Go 语言中没有 float 类型。 // 应该是 go 没有 double 类型吧？  

##### 整数：

- int8（-128 -> 127）
- int16（-32768 -> 32767）
- int32（-2,147,483,648 -> 2,147,483,647）
- int64（-9,223,372,036,854,775,808 -> 9,223,372,036,854,775,807）


##### 无符号整数：

- uint8（0 -> 255）
- uint16（0 -> 65,535）
- uint32（0 -> 4,294,967,295）
- uint64（0 -> 18,446,744,073,709,551,615）


##### 浮点型（IEEE-754 标准）：

- float32（+- 1e-45 -> +- 3.4 * 1e38）
- float64（+- 5 1e-324 -> 107 1e308）


int 型是计算最快的一种类型。  

整型的零值为 0，浮点型的零值为 0.0。  

float32 精确到小数点后 7 位，float64 精确到小数点后 15 位。  

应该尽可能地使用 float64，因为 math 包中所有有关数学运算的函数都会要求接收这个类型。  

通过增加前缀 0 来表示 8 进制数（如：077），增加前缀 0x 来表示 16 进制数（如：0xFF），以及使用 e 来表示 10 的连乘（如： 1e3 = 1000，或者 6.022e23 = 6.022 x 1e23）。  

使用 <code>a := uint64(0)</code> 来同时完成类型转换和赋值操作，这样 a 的类型就是 uint64。  

Go 中不允许不同类型之间的混合使用，但是对于常量的类型限制非常少，因此允许常量之间的混合使用，如下（该程序无法通过编译）：  

示例 4.8 type_mixing.go 

~~~go
package main

func main() {
    var a int
    var b int32
    a = 15
    b = a + a    // 编译错误
    b = b + 5    // 因为 5 是常量，所以可以通过编译
}
~~~

如果你尝试编译该程序，则将得到编译错误 <code>cannot use a + a (type int) as type int32 in assignment</code>。  

同样地，int16 也可以被隐式转换为 int32.  

下面这个程序展示了通过显式转换来避免这个问题（第 4.2 节）。  

示例 4.9 casting.go

~~~go
package main

import "fmt"

func main() {
    var n int16 = 34
    var m int32
    // compiler error: cannot use n (type int16) as type int32 in assignment
    //m = n
    m = int32(n)

    fmt.Printf("32 bit int is: %d\n", m)
    fmt.Printf("16 bit int is: %d\n", n)
}
~~~

输出：

~~~go
32 bit int is: 34
16 bit int is: 34
~~~

#### 格式化说明符

- %d  格式化整数（%x 和 %X 用于格式化 16 进制表示的数字）
- %g  格式化浮点型（%f 输出浮点数，%e 输出科学计数表示法）
- %0d 规定输出定长的整数，其中开头的数字 0 是必须的。

<code>%n.mg</code> 用于表示数字 n 并精确到小数点后 m 位，除了使用 g 之外，还可以使用 e 或者 f，例如：使用格式化字符串 <code>%5.2e</code> 来输出 3.4 的结果为 <code>3
.40e+00</code>。


#### 数字值转换

当进行类似 <code>a32bitInt = int32(a32Float)</code> 的转换时，小数点后的数字将被丢弃。
这种情况一般发生当从取值范围较大的类型转换为取值范围较小的类型时，或者你可以写一个专门用于处理类型转换的函数来确保没有发生精度的丢失。
下面这个例子展示如何安全地从 int 型转换为 int8：

~~~go
func Uint8FromInt(n int) (uint8, error) {
    if 0 <= n && n <= math.MaxUint8 { // conversion is safe
        return uint8(n), nil
    }
    return 0, fmt.Errorf("%d is out of the uint8 range", n)
}
~~~

或者安全地从 float64 转换为 int：

~~~go
func IntFromFloat64(x float64) int {
    if math.MinInt32 <= x && x <= math.MaxInt32 { // x lies in the integer range
        whole, fraction := math.Modf(x)
        if fraction >= 0.5 {
            whole++
        }
        return int(whole)
    }
    panic(fmt.Sprintf("%g is out of the int32 range", x))
}
~~~

不过如果你实际存的数字超出你要转换到的类型的取值范围的话，则会引发 panic（第 13.2 节）。


#### 4.5.2.2 复数