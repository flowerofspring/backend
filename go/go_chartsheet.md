## 值类型与引用类型

不管是Java还是golang中，都有值类型和引用类型的概念。在使用两者时，发现这两种语言之间还是有差异的。

**值类型**：值拷贝。这些类型的变量直接指向存在内存中的值，值类型的变量的值存储在栈中。当使用等号=将一个变量的值赋给另一个变量时，如 j = i ,实际上是在内存中将 i 的值进行了拷贝。可以通过 &i 获取变量 i 的内存地址。 

**引用类型**：一个引用变量r1的引用变量存储的是r1的值所在的内存地址，或内存地址中第一个元素所在的位置，这个内存地址被称之为指针，这个指针实际上被存储在引用变量中。从这个角度来看，任何变量都是值类型。

## 数组是值类型

如下代码片段定义了一个数组a，它是值类型，复制给b是值拷贝copy，当b发生变化后a并不会发生任何变化

```go
//由 main 函数作为程序入口点启动
func main() {
	a :=[5]int{1,2,3,4,5}    //数组Array类型，带有具体的长度
	b := a   //值拷贝，等价于a :=[5]int{1,2,3,4,5}, b :=[5]int{1,2,3,4,5}
	b[2] = 6 //b :=[5]int{1,2,3,4,5}，b[2]=8, b = [5]int{1,2,6,4,5}
	fmt.Println(a, b)
}
```

切片则不然，由于切片是引用类型，其拷贝（赋值）属于地址拷贝，所以其中一个元素的值发生变化，拷贝的另一方也会发生改变。

## 没有类

Golang中没有class关键字来定义类，对于事物的抽象以struct来定义，因此一个struct变量可以被看做一个类实例。但是这有别于java，因为struct在方法中传参时是值类型而非引用类型，所以当我们需要在方法内改变这个对象的字段值时，应该使用的是struct变量的指针，而非struct变量。

## 数组

go语言支持多维数组

##type 别名

```go
type ElemType int

type FuncHandler func(interface{})
```

##条件、循环

```go
if a > 3 {
}

// if for 不要加括号
for m := 1; m < 10; m++ {
}

// 没有while，可以用for break实现
```

##没有三元运算符

为了避免难以理解的复杂表达式

可以用以下形式代替

```go
if expr {
  n = trueVal
} else {
  n = falseVal
}
```

##没有专门的字符类型

存放单个ascii字符，可以用byte保存



##没有隐式类型转换

go中的类型转换分为强制类型转换和类型断言

## 字符串

字符串可以按切片处理

```go
	  var str = "Hello world"
    for i := 0; i < len(str); i++ {
        fmt.Printf("str[%d]=%c\n", i, str[i])
    }
```



## 字符类型

**Go**语言的字符有以下两种： 一种**是**uint8 **类型**，或者叫byte 型，代表了ASCII 码的一个字符。 另一种**是rune 类型**，代表一个UTF-8 字符，当需要处理中文、日文或者其他复合字符时，则需要用到**rune 类型**。 **rune 类型**等价于int32 **类型**。



## 包

package main is special

import exactly the packages you need



## 变量

```go
var name [type] [= exp]
var s string = "abc"
var s string
var s = "abc"

var i, j, k int
var a, b, c = true, 2.3, "four"
var f, err = os.Open(name)

s := ""  // := is declaration
i, j := 0, 1
f, err := os.Open(name)
out, err := os.Open(name)
f, err = os.Create(outfile)


```

## 整型和浮点型

整数：

- int8（-128 -> 127）

- int16（-32768 -> 32767）

- int32（-2,147,483,648 -> 2,147,483,647）

- int64（-9,223,372,036,854,775,808 -> 9,223,372,036,854,775,807）

- uint8（0 -> 255）

- uint16（0 -> 65,535）

- uint32（0 -> 4,294,967,295）

- uint64（0 -> 18,446,744,073,709,551,615）

  

浮点型（IEEE-754 标准）：

- float32（+- 1e-45 -> +- 3.4 * 1e38）
- float64（+- 5 * 1e-324 -> 107 * 1e308）

基于架构的类型：`int` 和 `uint` 在 32 位操作系统上，它们均使用 32 位（4 个字节），在 64 位操作系统上，它们均使用 64 位（8 个字节） 

可以通过增加前缀 0 来表示 8 进制数（如：077），增加前缀 0x 来表示 16 进制数（如：0xFF） 

float32 精确到小数点后 7 位，float64 精确到小数点后 15 位。 

**类型别名**

type TZ int

**字符类型**

```
var ch byte = 65 或 var ch byte = '\x41'
var ch int = '\u0041'
var ch2 int = '\U00101234'
```

## strings

```
strings.HasPrefix(str, prefix string) bool
strings.Contains(s, substr string) bool
strings.Index(s, str string) int
strings.LastIndex(s, str string) int
strings.Replace(str, old, new, n) string
strings.Count(s, str string) int
strings.Repeat(s, count int) string
strings.ToLower(s) string
strings.ToUpper(s) string
strings.TrimSpace(s)
strings.Trim(s, "cut")
strings.Fields(s)
strings.Split(s, sep)
strings.Join(s1 []string, sep string)
```

## strconv

```
strconv.Itoa(i int) string
strconv.Atoi(s string) (i int, err error)
strconv.ParseFloat(s string, bitSize int) (f flaot64, err error)
```



## 格式化说明符

在格式化字符串里，`%d` 用于格式化整数（`%x` 和 `%X` 用于格式化 16 进制表示的数字），`%g` 用于格式化浮点型（`%f` 输出浮点数，`%e` 输出科学计数表示法），`%0d` 用于规定输出定长的整数，其中开头的数字 0 是必须的。

`%n.mg` 用于表示数字 n 并精确到小数点后 m 位，除了使用 g 之外，还可以使用 e 或者 f，例如：使用格式化字符串`%5.2e` 来输出 3.4 的结果为 `3.40e+00`

## 运算符

带有 `++` 和 `--` 的只能作为语句，而非表达式，因此 `n = i++` 这种写法是无效的 。其它像 `f(i++)` 或者`a[i]=b[i++]` 这些可以用于 C、C++ 和 Java 中的写法在 Go 中也是不允许的。 

优先级

```
优先级   运算符
 7      ^ !
 6      * / % << >> & &^
 5      + - | ^
 4      == != < <= >= >
 3      <-
 2      &&
 1      ||
```

## 控制结构

### if 

```go
// simple if
if x > 0 {
  fmt.Println(x)
}

// init
if err := file.Chmod(0644); err != nil {
  fmt.Println(err)
}

// the code below reads well
f, err := file.Open(name)
if err != nil {
  return err
}

d, err := file.Stat(name)
if err != nil {
  f.Close()
  return err
}
codeUsing(f, d)
```

* mandotary braces
* accept an initialization statement
* the unnecessary else is omitted



### for

The go for loop is similar to C, but not the same.

* unifies for and while
* there is no do-while

```go
// like a C for
for init; condition; post {}
// like a C while
for condition {}
// infinate loop
for {}
```



use `range` clause to loop array, slice, string, or map, or reading from a channel

```go
for key, value := range aMap {
  fmt.Println(key, value)
}
```

if you only need the first item(the key or index), just drop the second

```go
for key := range m {
  fmt.Println(key)
}
```

if you only need the second, use the bland identifier to discard the first

```go
for _, value := range arr {
  fmt.Println(value)
}
```

### switch

* The expressions need not  be constants or even integers
* the cases are evaluated top to bottom until a match is found
* if the switch has no expression it switches on true（可以把`if-else if-else`写成switch）
* switch可通过逗号列表相同的条件

```go
func shouldEscape(c byte) bool {
  switch c {
    case ' ', '?', '=':
    	return true
  }
  return false
}
```

###  

type switch

switch可用来判断接口变量的动态类型



### break

In Go, `break` can break to a label

```go
Loop:
for(n := 0; n < len(src); n += size) {
  switch {
    case src[n] < sizeOne:
    if validateOnly {
      break
    }
    size = 1
    update(src[1])
    case src[n] < sizeTwo:
    if n + 1 >= len(src) {
      err = errShortInput
      break Loop
    }
    if validateOnly {
      break
    }
    size = 2
  }
}
```

### continue

`continue`也可接受一个标签，不过只能在循环内使用

### commar

Go没有逗号表达式

### 自增，自减

`++`, `--`是语句，而非表达式



```go
if condition1 {
    // do something 
} else if condition2 {
    // do something else    
}else {
    // catch-all or default
}

switch var1 {
    case v1:
        ...
    case v2:
        ...
    default:
    	...    
}

switch initialization  {
    case condition1:
        f1()
    case condition2:
        f2()
    default:
        f3()
}

for initialization; condition; post {
    // statements
}
for condition {
    //
}
for {
    //
}
for pos, char := range str {
...
}

os.Exit(1)
```

## 函数

* can return multiple values
* named results, initialized as zero values

```go
func name(parameter-list) (result-list) {
    body
}
```

go不支持函数重载、



## 方法的接收者

The rule about pointers vs. values for receivers is that value methods can be invoked on pointers and values, but pointer methods can only be invoked on pointers.

## defer

two advantages

1. garantees that you never forget to release resources
2. the code sits near the function, it't more clearer

* the arguments of the defer function are evaluated when the defer executed
* defer functions are executed in FIFO order

```go
func trace(s stirng) string {
  fmt.Println("Entering %s", s)
  return s
}

func un(s string) {
  fmt.Println("Leaveing %s", s)
}

func a() {
  defer un(trace("a"))
  fmt.Println("in a")
}
```



## composite literals（复合字面量）

```go 
func NewFile(fd int, name string) *File {
	if fd < 0 {
		return nil
	}
	f := File{fd, name, nil, 0}
	return &f
  // or just 
  // return &File{fd, name, nil, 0}
}
```

**note**: 在Go中，返回一个局部变量的地址是没有问题的。

* 使用复合字面量时，会创建一个全新的实例
* the fields of composite literal are laied out in order and must all be present
* 也可以按 field: value形式列出，就可以不按顺序，未给出的字段会被赋零值
* new(File) and &File{} are equivalent
* composite litetals can also be created for arrays, slices or maps

## new

build-in function

`new(T)` allocates zeroed storage for a new item of type T and return it's address

## make

* `make` creates slices, mapss and channel only
* returns an initialized value of type T(not *T)
* For slices, maps, and channels, make initializes the internal data structure and prepare the value for use.

这三种类型为引用数据类型，在使用之前必须初始化。例如，切片包含一个指向数组的指针，长度以及容量。

## map

map 的读取和设置也类似 slice 一样，通过 key 来操作，只是 slice 的index 只能是｀int｀类型，而 map 多了很多类型，可以是 int ，可以是 string及所有完全定义了 == 与 != 操作的类型。

```go
// 1. 先声明再初始化最后赋值
var m map[int]int
m = make(map[int]int)
m[10] = 5

// 2.make初始化后赋值
m := make(map[int]int)
m[10] = 5

// 3. 直接初始化赋值
m := map[int]int{1: 1, 10: 5}

// 遍历
for key, value := range m {
  fmt.Println(key, value)
}

// 判断键值是否存在
if v, ok := m[10]; ok {
  
}
```



## slice

## range

_, arg := range os.Args[1:]  // :=是简略写法，自动判断类型；range返回index, value

## Args

import os

os.Args

## Join

```go
import "strings"
strings.Joint(s, ",")
```

## rand

`rand.Float32` 和 `rand.Float64` 返回介于 [0.0, 1.0) 之间的伪随机数，其中包括 0.0 但不包括 1.0。函数 `rand.Intn`返回介于 [0, n) 之间的伪随机数。 

## 函数类型实现接口

函数和其他类型一样都属于“一等公民”，其他类型能够实现接口，函数也可以，本节将对结构体与函数实现接口的过程进行对比。

```go
package main

import (
	"fmt"
)

type Invoker interface {
	Call(interface{})
}

type Struct struct {

}

func (s *Struct) Call(p interface{}) {
	fmt.Println("from struct", p)
}

type FuncCaller func(interface{})

func (f FuncCaller) Call(p interface{}) {
	f(p)
}

func main() {
	var invoker Invoker

	s := new(Struct)
	invoker = s

	invoker.Call("hello")

	invoker = FuncCaller(func(v interface{}) {
		fmt.Println("from function", v)
	})
	invoker.Call("hello")

```



## struct

### struct的嵌套/组合

```go
package main

import (
	"fmt"
)

type Base struct {
	bx int
	by int
}

type Student struct {
	Base
	x int
	y int
}

func main() {
	s := new(Student)
	s.bx = 1
	s.by = 100

	s.x = 2
	s.y = 3

	fmt.Println(s)
}
```

