### 第四章 基本结构和基本数据类型

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
