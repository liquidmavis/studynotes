# Go



## 垃圾收集器

go也认为内存管理不应该由开发者来关系，提供了垃圾收集器，go的垃圾收集器采用标记-清除算法，**貌似没有分代思想**



## 程序基本要素

### 标准库

一般情况下，标准包会存放在 `$GOROOT/pkg/$GOOS_$GOARCH/` 目录下。

如果对一个包进行更改和重新编译，所有引用了这个包的程序都需要重新编译



#### Reader

`io` 包指定了 `io.Reader` 接口，它表示从数据流的末尾进行读取。

Go 标准库包含了该接口的[许多实现](https://go-zh.org/search?q=Read#Global)，包括文件、网络连接、压缩和加密等等。

`io.Reader` 接口有一个 `Read` 方法：

```
func (T) Read(b []byte) (n int, err error)
```

`Read` 用数据填充给定的字节切片并返回填充的字节数和错误值。在遇到数据流的结尾时，它会返回一个 `io.EOF` 错误。





### 可见性规则

当标识符以一个大写字母开头，如：Liquid，那么这种形式的标识符对象可以被外部包的代码所使用，这种被称为**导出**，类似java的public。如果以小写开头，如liquid，那么外部无法访问，类似java的private



### 函数

这是定义一个函数最简单的格式：

```go
func functionName()
```



**Go 语言虽然看起来不使用分号作为语句的结束，但实际上这一过程是由编译器自动完成，因此才会引发像上面这样的错误**

所以函数的{应该紧跟函数名后面



对于带入参数和返回参数的函数

```go
func functionName(parameter_list) (return_value_list) {
   …
}
```

其中：

- `parameter_list` 的形式为 `(param1 type1, param2 type2, …)`
- `return_value_list` 的形式为 `(ret1 type1, ret2 type2, …)`



### 类型

基本类型如：`int`、`float`、`bool`、`string`；

结构类型如：`struct`、`array`、切片 (slice)、`map`、通道 (channel)；

只描述类型的行为的，如：`interface`。



结构化类型的默认值为**nil**



通常可以使用**type**关键字来定义数据的类型或者类型别名

如下，定义a和b的int变量然后输出相减后的值

```go
var a int = 10
	var b int = 5
	liquid.Println(a - b)
```





下面定义一个减法的函数然后打印输出，该函数会返回结果

```go
func main() {

	liquid.Println(test(5, 4))
}
func test(a int, b int) (c int) {
	return a - b
}
```



#### 分片

切片类似于数组，其提供了动态改变大小的特征，切片比数组更为灵活

**这个其实类似于python的**

以下表达式创建了一个切片，它包含 `a` 中下标从 1 到 3 的元素：

```go
a[1:4]
```



切片拥有 **长度** 和 **容量**。

切片的长度就是它所包含的元素个数。

切片的容量是从它的第一个元素开始数，到其底层数组元素末尾的个数。

切片 `s` 的长度和容量可通过表达式 `len(s)` 和 `cap(s)` 来获取。



### 类型转换

go不存在隐式类型转换，都需要显示告知

转换方法类似函数调用

```go
valueOfTypeB = typeB(valueOfTypeA)
```



### 命名规范

干净、可读的代码和简洁性是 Go 追求的主要目标。通过 gofmt 来强制实现统一的代码风格。Go 语言中对象的命名也应该是简洁且有意义的。像 Java 和 Python 中那样使用混合着大小写和下划线的冗长的名称会严重降低代码的可读性。名称不需要指出自己所属的包，因为在调用的时候会使用包名作为限定符。返回某个对象的函数或方法的名称一般都是使用名词，没有 `Get...` 之类的字符，如果是用于修改某个对象，则使用 `SetName()`。有必须要的话可以使用大小写混合的方式，如 `MixedCaps()` 或 `mixedCaps()`，而不是使用下划线来分割多个名称。



### 常量

常量使用关键字 `const` 定义，用于存储不会改变的数据。

存储在常量中的数据类型只可以是布尔型、数字型（整数型、浮点型和复数）和字符串型。

常量的定义格式：`const identifier [type] = value`，例如：

```go
const Pi = 3.14159
```



常量允许并行赋值

```go
const beef, two, c = "eat", 2, "veg"
const Monday, Tuesday, Wednesday, Thursday, Friday, Saturday = 1, 2, 3, 4, 5, 6
const (
	Monday, Tuesday, Wednesday = 1, 2, 3
	Thursday, Friday, Saturday = 4, 5, 6
)
```



常量还可以用作枚举：

```go
const (
	Unknown = 0
	Female = 1
	Male = 2
)
```



### 变量

变量声明一般用`var`

```go
var a int
var b bool
var str string
```



尽管变量的标识符必须是唯一的，但你可以在某个代码块的内层代码块中使用相同名称的变量，则此时外部的同名变量将会暂时隐藏（结束内部代码块的执行后隐藏的外部同名变量又会出现，而内部同名变量则被释放），你任何的操作都只会影响内部代码块的局部变量。



在函数体内声明局部变量推荐用简单语法`:=`

```go
a:=1
```

**注意事项**

如果在相同的代码块中，我们不可以再次对于相同名称的变量使用初始化声明，例如：`a := 20` 就是不被允许的



### init函数

变量除了可以在全局声明中初始化，也可以在 `init()` 函数中初始化。这是一类非常特殊的函数，它不能够被人为调用，而是在每个包完成初始化后自动执行，并且执行优先级比 `main()` 函数高。

每个源文件可以包含多个 `init()` 函数，同一个源文件中的 `init()` 函数会按照从上到下的顺序执行，如果一个包有多个源文件包含 `init()` 函数的话，则官方鼓励但不保证以文件名的顺序调用。初始化总是以单线程并且按照包的依赖关系顺序执行。



### 基本类型和运算符

在 Go 语言中，`&&` 和 `||` 是具有快捷性质的运算符，当运算符左边表达式的值已经能够决定整个表达式的值的时候（`&&` 左边的值为 `false`，`||` 左边的值为 `true`），运算符右边的表达式将不会被执行。利用这个性质，如果你有多个条件判断，应当将计算过程较为复杂的表达式放在运算符的右侧以减少不必要的运算。





`int` 型是计算最快的一种类型。

整型的零值为 `0`，浮点型的零值为 `0.0`。

`float32` 精确到小数点后 7 位，`float64` 精确到小数点后 15 位。由于精确度的缘故，你在使用 `==` 或者 `!=` 来比较浮点数时应当非常小心。你最好在正式使用前测试对于精确度要求较高的运算。

你应该尽可能地使用 `float64`，因为 `math` 包中所有有关数学运算的函数都会要求接收这个类型。



#### 数字值转换

当进行类似 `a32bitInt = int32(a32Float)` 的转换时，小数点后的数字将被丢弃。这种情况一般发生当从取值范围较大的类型转换为取值范围较小的类型时，或者你可以写一个专门用于处理类型转换的函数来确保没有发生精度的丢失。下面这个例子展示如何安全地从 `int` 型转换为 `int8`：

```go
func Uint8FromInt(n int) (uint8, error) {
	if 0 <= n && n <= math.MaxUint8 { // conversion is safe
		return uint8(n), nil
	}
	return 0, fmt.Errorf("%d is out of the uint8 range", n)
}
```





#### 数学随机数

```go
//随机数
for i := 0; i < 10; i++ {
   t := rand.Intn(100)
   fmt.Println(t)
}
```





#### 字符类型

严格来说，这并不是 Go 语言的一个类型，字符只是整数的特殊用例。`byte` 类型是 `uint8` 的别名，对于只占用 1 个字节的传统 ASCII 编码的字符来说，完全没有问题。例如：`var ch byte = 'A'`；字符使用单引号括起来。

在 ASCII 码表中，`'A'` 的值是 `65`，而使用 16 进制表示则为 `41`，所以下面的写法是等效的：

```go
var ch byte = 65 或 var ch byte = '\x41'
```



#### 字符串

字符串是一种值类型，且值不可变，即创建某个文本后你无法再次修改这个文本的内容；更深入地讲，字符串是字节的定长数组。



字符串的内容（纯字节）可以通过标准索引法来获取，在中括号 `[]` 内写入索引，索引从 0 开始计数：

- 字符串 `str` 的第 1 个字节：`str[0]`
- 第 `i` 个字节：`str[i - 1]`
- 最后 1 个字节：`str[len(str)-1]`

需要注意的是，这种转换方案只对纯 ASCII 码的字符串有效。

**注意事项** 获取字符串中某个字节的地址的行为是非法的，例如：`&str[i]`。



**字符串拼接符 `+`**

两个字符串 `s1` 和 `s2` 可以通过 `s := s1 + s2` 拼接在一起。

`s2` 追加在 `s1` 尾部并生成一个新的字符串 `s`。

你可以通过以下方式来对代码中多行的字符串进行拼接：

```go
str := "Beginning of the string " +
	"second part of the string"
```

由于编译器行尾自动补全分号的缘故，加号 `+` 必须放在第一行。

拼接的简写形式 `+=` 也可以用于字符串：

```go
s := "hel" + "lo,"
s += "world!"
fmt.Println(s) //输出 “hello, world!”
```





### 关键字

#### defer

会把defer后面的函数调用推迟到外层函数返回之后执行

```java
defer fmt.Println("world")

	fmt.Println("hello")
```



defer栈

输出结果是倒着输出的

```go
fmt.Println("counting")

	for i := 0; i < 10; i++ {
		defer fmt.Println(i)
	}

	fmt.Println("done")
```





### 指针

类型 `*T` 是指向 `T` 类型值的指针。其零值为 `nil`。

```
var p *int
```

`&` 操作符会生成一个指向其操作数的指针。

```
i := 42
p = &i
```

`*` 操作符表示指针指向的底层值。

```
fmt.Println(*p) // 通过指针 p 读取 i
*p = 21         // 通过指针 p 设置 i
```

这也就是通常所说的“间接引用”或“重定向”。





### 结构体

定义一个student类，并创建对象，从下面来看他默认给出了带参数的构造函数，这是与java不同的

```go
type Student struct {
	name string
	age  int
}

func main() {
 	xiaoming := Student{age: 18, name: "小明"}
	fmt.Println(xiaoming)
}
```



#### 方法

go语言没有类的概念，那么传统面向对象属于类的方法应该放在何处？

提出了为结构体类型定义方法，方法本质就是函数，**只不过带特殊的接受者参数的函数**

在`func`后面跟上结构体的类型，之后必须使用结构体类型的实例才能调用该方法

```go
func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
```



前面我们讲的方法为值方法，他传入进去的是结构体的拷贝

而指针接受者方法可以修改接受者指向的值，如下面例子对比

```go
type person struct {
   name string
}

func (s person) changeName() {
   s.name = "mavis"
}
func (s *person) changename() {
   s.name = "mavis"
}
func main() {
   var s = person{"liuyang"}
   //值接受者，输出还是"liuyang"
   s.changeName()
   fmt.Println(s.name)

   //指针接受者,输出为"mavis"
   s.changename()
   fmt.Println(s.name)
}
```



##### 方法与指针重定向

比较前两个程序，你大概会注意到带指针参数的函数必须接受一个指针：

```
var v Vertex
ScaleFunc(v, 5)  // 编译错误！
ScaleFunc(&v, 5) // OK
```

而以指针为接收者的方法被调用时，接收者既能为值又能为指针：

```
var v Vertex
v.Scale(5)  // OK
p := &v
p.Scale(10) // OK
```

对于语句 `v.Scale(5)`，即便 `v` 是个值而非指针，带指针接收者的方法也能被直接调用。 也就是说，由于 `Scale` 方法有一个指针接收者，为方便起见，Go 会将语句 `v.Scale(5)` 解释为 `(&v).Scale(5)`。





### 接口

**接口类型** 是由一组方法签名定义的集合。

接口类型的变量可以保存任何实现了这些方法的值。

#### 多态性

```go
type animal interface {
	eat()
}
type cat struct {
}
type dog struct {
}

func (d cat) eat() {
	fmt.Println("猫吃")
}
func (d dog) eat() {
	fmt.Println("狗吃")
}
func main() {
	var a animal
	a = cat{}
	a.eat()
	a = dog{}
	a.eat()
}

```



#### 隐式实现

类型通过实现一个接口的所有方法来实现该接口。既然无需专门显式声明，也就没有“implements”关键字。

隐式接口从接口的实现中解耦了定义，这样接口的实现可以出现在任何包中，无需提前准备。



#### 空接口

指定了零个方法的接口值被称为 *空接口：*

```
interface{}
```

空接口可保存任何类型的值。（因为每个类型都至少实现了零个方法。）

空接口被用来处理未知类型的值。例如，`fmt.Print` 可接受类型为 `interface{}` 的任意数量的参数。

```go
func main() {
	var i interface{}
	describe(i)

	i = 42
	describe(i)

	i = "hello"
	describe(i)
}

func describe(i interface{}) {
	fmt.Printf("(%v, %T)\n", i, i)
}
//(<nil>, <nil>)
//(42, int)
//(hello, string)
```



#### 类型判定

通过空接口来进行类型判定

```go
func typeswitch(t interface{}) {
	switch v := t.(type) {
	case int:
		fmt.Println("int类型" + string(v))
	case string:
		fmt.Println("string类型" + string(v))
	}
}
func main() {
	typeswitch(5)
	typeswitch("hello")
}
```



#### Stringer

就是java的tostring方法

[`fmt`](https://go-zh.org/pkg/fmt/) 包中定义的 [`Stringer`](https://go-zh.org/pkg/fmt/#Stringer) 是最普遍的接口之一。

```go
type Stringer interface {
    String() string
}
```

`Stringer` 是一个可以用字符串描述自己的类型。`fmt` 包（还有很多包）都通过此接口来打印值。



### 映射

类似于map

```go
type Vertex struct {
	Lat, Long float64
}

var m map[string]Vertex

func main() {
	m = make(map[string]Vertex)
	m["Bell Labs"] = Vertex{
		40.68433, -74.39967,
	}
	fmt.Println(m["Bell Labs"])
}

```





在映射 `m` 中插入或修改元素：

```
m[key] = elem
```

获取元素：

```
elem = m[key]
```

删除元素：

```
delete(m, key)
```

通过双赋值检测某个键是否存在：

```
elem, ok = m[key]
```

若 `key` 在 `m` 中，`ok` 为 `true` ；否则，`ok` 为 `false`。

若 `key` 不在映射中，那么 `elem` 是该映射元素类型的零值。

同样的，当从映射中读取某个不存在的键时，结果是映射的元素类型的零值。

**注** ：若 `elem` 或 `ok` 还未声明，你可以使用短变量声明：

```
elem, ok := m[key]
```





### 函数传递

函数可以作为参数来传递

```go
//接受tmp作为函数
func mulity(tmp func(a int, b int) int, x int, y int) int {
   return tmp(x, y)
}
func main() {
   //声明了匿名函数用来做加法，赋值给t
   t := func(a int, b int) int {
      return a + b
   }
   //把t作为参数传递给函数
   fmt.Println(mulity(t, 3, 4))
}
```



#### 匿名函数

就是java的匿名函数

```go
//匿名函数
a := func(x int, y int) int {
   return x + y
}

fmt.Println(a(3, 2))
```



也可以直接对匿名函数调用()是对匿名函数的调用

```go
//匿名函数直接调用
	func() {
		sum := 0
		for i := 1; i < 5; i++ {
			sum += i
			fmt.Println(sum)
		}
	}()
```



该匿名函数需要string类型参数，这里传入“hello”作为参数

```go
func(s string) {
   fmt.Println(s)
}("hello")
```





#### 函数闭包

匿名函数同样成为闭包，他们被允许调用定义在其他环境下的变量，闭包可以使得某个函数捕捉到一些外部状态，例如函数创建时的状态



下面将函数作为返回值，下面返回的是func(b int) int 意思是返回该类型函数，该函数需要int型入参数和int类型返回参数

```go
func add2() func(b int) int {
   return func(b int) int {
      return b + 2
   }
}
```



```
package main

import "fmt"

func main() {
	var f = Adder()
	fmt.Print(f(1), " - ")
	fmt.Print(f(20), " - ")
	fmt.Print(f(300))
}

func Adder() func(int) int {
	var x int
	return func(delta int) int {
		x += delta
		return x
	}
}
```

函数 `Adder()` 现在被赋值到变量 `f` 中（类型为 `func(int) int`）。

输出：

```
1 - 21 - 321
```

三次调用函数 `f` 的过程中函数 `Adder()` 中变量 `delta` 的值分别为：1、20 和 300。

我们可以看到，在多次调用中，变量 `x` 的值是被保留的，即 `0 + 1 = 1`，然后 `1 + 20 = 21`，最后 `21 + 300 = 321`：闭包函数保存并积累其中的变量的值，不管外部函数退出与否，它都能够继续操作外部函数中的局部变量。





##### 工厂函数

一个返回值为另一个函数的函数可以被称之为工厂函数，这在您需要创建一系列相似的函数的时候非常有用：书写一个工厂函数而不是针对每种情况都书写一个函数

可以返回其它函数的函数和接受其它函数作为参数的函数均被称之为高阶函数，是函数式语言的特点

```go
func addSuffix(s string) func(filename string) string {
   suffix := s
   return func(filename string) string {
      if !strings.HasSuffix(filename, suffix) {
         return filename + suffix
      }
      return filename
   }
}

txtsuffix := addSuffix(".txt")
	gosuffix := addSuffix(".go")

	filename := "Hello"
	fmt.Println(txtsuffix(filename))
	fmt.Println(gosuffix(filename))
//输出
//Hello.txt
//Hello.go

```



## 多线程

> Go实现并发不依靠共享内存来通信，而是依靠通道来共享内存
>



### Go 程

Go 程（goroutine）是由 Go 运行时管理的轻量级线程。

```go
go f(x, y, z)
```

会启动一个新的 Go 程并执行

```go
f(x, y, z)
```

`f`, `x`, `y` 和 `z` 的求值发生在当前的 Go 程中，而 `f` 的执行发生在新的 Go 程中。

Go 程在相同的地址空间中运行，因此在访问共享的内存时必须进行同步



```go
func say(s string) {
	for i := 0; i < 5; i++ {
		time.Sleep(100 * time.Millisecond)
		fmt.Println(s)
	}
}
func main() {
	go say("liquid")
	say("hello")
}
```





### 信道

信道是带有类型的管道，你可以通过它用信道操作符 `<-` 来发送或者接收值。

```
ch <- v    // 将 v 发送至信道 ch。
v := <-ch  // 从 ch 接收值并赋予 v。
```

（“箭头”就是数据流的方向。）

和映射与切片一样，信道在使用前必须创建：

```
ch := make(chan int)
```

默认情况下，发送和接收操作在另一端准备好之前都会阻塞。这使得 Go 程可以在没有显式的锁或竞态变量的情况下进行同步。





```go
func sum(s []int, ch chan int) {
   sum := int(0)
   for _, num := range s {
      sum += num
   }
   ch <- sum
}
func main() {
   s := []int{1, 2, 3, 4, 5, 6, 7, 8}
   ch := make(chan int)
   go sum(s[:len(s)/2], ch)
   go sum(s[len(s)/2:], ch)

   x, y := <-ch, <-ch
   fmt.Println(x, y, x+y)
}
```





#### 带缓冲的信道

信道可以是 *带缓冲的*。将缓冲长度作为第二个参数提供给 `make` 来初始化一个带缓冲的信道：

```
ch := make(chan int, 100)
```

仅当信道的缓冲区填满后，向其发送数据时才会阻塞。当缓冲区为空时，接受方会阻塞。

修改示例填满缓冲区，然后看看会发生什么。





#### 多线程通信

##### select

当 `select` 中的其它分支都没有准备好时，`default` 分支就会执行。

为了在尝试发送或者接收时不发生阻塞，可使用 `default` 分支：

```go
select {
case i := <-c:
    // 使用 i
default:
    // 从 c 中接收会阻塞时执行
}
```





##### 互斥

Go 标准库中提供了 [`sync.Mutex`](https://go-zh.org/pkg/sync/#Mutex) 互斥锁类型及其两个方法：

- `Lock`
- `Unlock`

我们可以通过在代码前调用 `Lock` 方法，在代码后调用 `Unlock` 方法来保证一段代码的互斥执行。参见 `Inc` 方法。





### 协程

​		在 Go 中，应用程序并发处理的部分被称作 `goroutines（协程）`，它可以进行更有效的并发运算。在协程和操作系统线程之间并无一对一的关系：协程是根据一个或多个线程的可用性，映射（多路复用，执行于）在他们之上的；协程调度器在 Go 运行时很好的完成了这个工作。

​		协程是轻量的，比线程更轻。它们痕迹非常不明显（使用少量的内存和资源）：使用 4K 的栈内存就可以在堆中创建它们。因为创建非常廉价，必要的时候可以轻松创建并运行大量的协程（在同一个地址空间中 100,000 个连续的协程）。并且它们对栈进行了分割，从而动态的增加（或缩减）内存的使用；栈的管理是自动的，但不是由垃圾回收器管理的，而是在协程退出后自动释放。