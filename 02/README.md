Go 的源代码有以三个分支：
~~~
- Go release：最新稳定版，实际开发最佳选择
- Go weekly：包含最近更新的版本，一般每周更新一次
- Go tip：永远保持最新的版本，相当于内测版
~~~

### 安装目录清单
你的 Go 安装目录（$GOROOT）的文件夹结构应该如下所示：
~~~
README.md
AUTHORS
CONTRIBUTORS
LICENSE

/bin：    包含可执行文件，如：编译器，Go 工具
/doc：    包含示例程序，代码工具，本地文档等
/lib：    包含文档模版
/misc：   包含与支持 Go 编辑器有关的配置文件以及 cgo 的示例
/os_arch：包含标准库的包的对象文件（.a）
/src：    包含源代码构建脚本和标准库的包的完整源代码（Go 是一门开源语言）
/src/cmd：包含 Go 和 C 的编译器和命令行脚本
~~~