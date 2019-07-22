### 第三章 编辑器、集成开发环境与其他工具

在命令行输入 **gofmt –w program.go** 会格式化该源文件的代码然后将格式化后的代码覆盖原始内容（如果不加参数 -w 则只会打印格式化后的结果而不重写文件）；<b>gofmt -w *.go</b> 会格式化并重写所有 Go 源文件；gofmt map1 会格式化并重写 map1 目录及其子目录下的所有 Go 源文件。

**gofmt** 也可以通过在参数 -r 后面加入用双引号括起来的替换规则实现代码的简单重构，规则的格式：<原始内容> -> <替换内容>。  

实例：
~~~go
gofmt -r '(a) -> a' –w *.go
~~~

上面的代码会将源文件中没有意义的括号去掉。

~~~go
gofmt -r 'a[n:len(a)] -> a[n:]' –w *.go
~~~

上面的代码会将源文件中多余的 **len(a)** 去掉。（ 译者注：了解切片（slice）之后就明白这为什么是多余的了 ）

~~~
gofmt –r 'A.Func1(a,b) -> A.Func2(b,a)' –w *.go
~~~


### 生成代码工具


**go doc** 工具会从 Go 程序和包文件中提取顶级声明的首行注释以及每个对象的相关注释，并生成相关文档。  

一般用法

* <b>go doc package</b> 获取包的文档注释，例如：<b>go doc fmt</b> 会显示使用 godoc 生成的 fmt 包的文档注释。

* <b>go doc package/subpackage</b> 获取子包的文档注释，例如：<b>go doc container/list</b>。

* <b>go doc package function</b> 获取某个函数在某个包中的文档注释，例如：<b>go doc fmt Printf</b> 会显示有关 <b>fmt.Printf()</b> 的使用说明。


这个工具只能获取在 Go 安装目录下 <b>../go/src</b> 中的注释内容。此外，它还可以作为一个本地文档浏览 web 服务器。在命令行输入 <b>godoc -http=:6060</b>，然后使用浏览器打开 <b>http://localhost:6060</b> 后，你就可以看到本地文档浏览服务器提供的页面。


### 其它工具

* <b>go install</b> 是安装 Go 包的工具。主要用于安装非标准库的包文件，将源代码编译成对象文件。

* <b>go fix</b> 用于将你的 Go 代码从旧的发行版迁移到最新的发行版。

* <b>go test</b> 是一个轻量级的单元测试框架。