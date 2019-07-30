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
