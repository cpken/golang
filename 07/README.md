#### 第七章 数组与切片

#### 7.1 声明和初始化

##### 7.1.1 概念

数组是具有相同 唯一类型 的一组已编号且长度固定的数据项序列（这是一种同构的数据结构）；这种类型可以是任意的原始类型
例如整形、字符串或者自定义类型，数组长度必须是一个常量表达式，并且必须是一个非负整数，数组长度也是数组类型的一部分，
所以 [5]int 和 [10]int 是属于不同类型的。数组的编译时值初始化是按照数组顺序完成的。  

注意事项：如果我们想让数组元素类型为任意类型的话可以使用空接口作为类型。当使用值时我们必须先做一个类型判断。  

数组元素可以通过 索引（位置）来读取（或修改），索引从 0 开始，第一个索引为 0，第二个索引为 1，以此类推。
元素的数目，也称为长度或者数组大小必须是固定的并且在声明该数组时就给出（编译时需要知道数组长度以便分配内存）；
数组长度最大为 2 GB。  

声明格式

~~~go
var identifier [len]type
~~~

例如：

~~~go
var arr1 [5]int
~~~

在内存中的结构是：

![image](https://github.com/cpken/theWayToGoNote/blob/master/07/07-1.png)

每个元素是一个整形值，当声明数组时所有的元素都会被自动初始化为默认值 0。  

arr1 的长度是 5，索引范围从 0 到 len(arr1)-1。  

使用 for 结构遍历数组：

- 通过 for 初始化数组项
- 通过 for 打印数组元素
- 通过 for 依次处理元素

示例 7.1 for_arrays.go

~~~go
package main
import "fmt"

func main() {
	var arr1 [5]int
	
	for i:=0; i < len(arr1); i++ {
		arr1[i] = i * 2
	}
	
	for i:=0; i < len(arr1); i++ {
		fmt.Printf("Array at idnex %d is %d\n", i, arr1[i])
	}
}
~~~

输出结果：

~~~
Array at index 0 is 0
Array at index 1 is 2
Array at index 2 is 4
Array at index 3 is 6
Array at index 4 is 8
~~~

Go 语言中的数组是一种 值类型（不像 C/C++ 中是指向首元素的指针），所以可以通过 new() 来创建：
var arr1 = new([5]int)。  

那么这种方式和 var arr2 [5]int 的区别是什么？ arr1 的类型是 *[5]int，而 arr2 的类型是 [5]int。  

这样的结果就是当把一个数组赋值给另一个时，需要在做一次数组内存的拷贝操作，例如：

~~~go
arr2 := arr1
arr2[2] = 100
~~~

在函数中数组作为参数传入时，会产生一次数组拷贝，拷贝不会修改原始的数组。  

通过使用 & 操作符以引用方式传过来，可以修改原始的数组。

<br><br>


#### 7.1.2 数组常量

通过 数组常量 的方法初始化数组，而不用依次使用 []= 方法（所有的组成元素都有相同的常量语法）。  

示例 7.3 array_literals.go

~~~go
package main
import "fmt"

func main() {
	//var arrAge      = [5]int{18, 20, 15, 22, 16}
	//var arrLazy     = [...]int{5, 6, 7, 8, 22}
	//var arrLazy     = []int{5, 6, 7, 8, 22}
	var arrKeyValue = [5]string{3:"Chris", 4:"Ron"}
	//var arrKeyValue = []string{3:"Chris", 4:"Ron"}
	
	for i:=0; i < len(arrKeyValue); i++ {
		fmt.Printf("Person at %d is %s\n", i, arrKeyValue[i])
	}
}
~~~

第一种变化：
~~~
var arrAge = [5]int{18, 20, 15, 22, 16}
~~~

注意 [5]int 可以从左边起开始忽略：[10]int {1, 2, 3} 这是一个有 10 个元素的数组，除了前三个元素外其它元素都为 0。  

第二种变化：  
~~~
var arrLazy = [...]int{5, 6, 7, 8}
~~~

<code>...</code> 可同样可以忽略，从技术上说它们其实变化成了切片。  

第三种变化：key: value syntax

~~~
var arrKeyValue = [5]string{3:"Chris", 4:"Ron"}
~~~

只有索引 3 和 4 被赋予实际的值，其它元素都被设置为空的字符串，所以输出结果为：

~~~
Person at 0 is
Person at 1 is
Person at 2 is
Person at 3 is Chris
Person at 4 is Ron
~~~

<br/>
<br/>

#### 7.1.3 多维数组

多维数组：[3][5]int，[2][2][2]float64。  

内部数组总是长度相同的。Go 语言的多维数组是矩形式的（唯一的例外是切片的数组）。  

示例 7.5 multidim_array.go

~~~go
package main
const (
	WIDTH   = 1920
	HEIGHT  = 1000
)

type pixel int
var screen [WIDTH][HEIGHT]pixel

func main () {
	for y := 0; y < HEIGHT; y++ {
		for x := 0; x < WIDTH; x++ {
			screen[x][y] = 0
		}
	}
}
~~~

<br/>
<br/>


#### 7.1.4 将数组传递给函数

将第一个大数组传递给函数会消耗很多内存。

- 传递数组的指针
- 使用数组的切片

<br/>
<br/>

##### 7.2.1 概念

切片（slice）是对数组一个连续片段的引用（该数组我们称之为相关数组，通过是匿名的）。切片是 引用类型。  

切片是可索引的，并且可以由 len() 函数获取长度。  

给定项的切片索引可能比相关数组的相同元素的索引小。和数组不同的是，切片的长度可以在运行时修改，最小
为 0 最大为相关数组的长度：切片是一个 长度可变的数组。  

切片提供了计算容量的函数 cap() 可以测量切片最长可以达到多少：它等于切片的长度 + 数组除切片之外的长度。
如果 s 是一个切片，cap(s) 就是从 s[0] 到数组末尾的数组长度。切片的长度永远不会超过它的容量，所以对于 切片s 来说
该不等式永远成立：0 <= len(s) <= cap(s)。  

多个切片如果表示同一个数组的片段，它们可以共享数据：因此一个切片和相关数组的其他切片是共享存储的，相反，
不同的数组总是代表不同的存储。数组实际上是切片的构建块。  

优点 因为切片是引用，所以它们不需要使用额外的内存并且比使用数组更有效率，所以 GO 代码中切片比数组更常用。  

声明切片：var identifier []type （不需要说明长度）。  

一个切片在未初始化之前默认为 nil，长度为 0 。

切片初始化：var slicel []type = arr1[start:end]。  

这表示 slice1 是由数组 arr1 从 start 索引到  end-1 索引之间的元素构成的子集（切分数组，start:end 被称
为 slice 表达式）。所以 slice1[0] 就等于 arr1[start] 。  

如果某个人写： var slice1 []type = arr1[:] 那么 slice1 就等于完整的 arr1 数组（所以这种表示方
式是 arr1[0:len(arr1)] 的一种缩写）。  

另外一种表述方式是： slice1 = &arr1 。  
arr1[2:] 和  arr1[2:len(arr1)] 相同，都包含了数组从第二个到最后的所有元素。  
arr1[:3] 和  arr1[0:3] 相同，包含了从第一个到第三个元素（不包括第三个）。  
如果你想去掉 slice1 的最后一个元素，只要 slice1 = slice1[:len(slice1)-1] 。  
 
一个由数字 1、2、3 组成的切片可以这么生成： s := [3]int{1,2,3} 甚至更简单的
s := []int{1,2,3} 。  



