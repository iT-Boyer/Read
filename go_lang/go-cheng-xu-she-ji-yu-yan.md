# Go程序设计语言

## 1. 入门

hello world

```golang
package main

import "fmt"

func main() {
    fmt.Println("Hello World")
}
```

直接执行

```bash
go run helloWorld.go
```

编译为二进制文件, 方便后续多次执行

```bash
go build helloWorld.go
./helloWorld.go
```

以下几种声明字符串变量的方式是等价的

```go
s := ""
var s string
var s = ""
var s string = ""
```
## 2. 程序结构


### 2.3 变量

声明变量的通用形式: `var name type = expression`

Go里不存在未初始化的变量

### 2.3.2指针

```go
x := 1
p := &x // p是整形指针, 指向x
fmt.Println(*p)  // 1
*p = 2 // 等于 x = 2
fmt.Println(x) // 结果为2
```

指针的零值为`nil`

两个指针在都为nil或者指向同一变量时才会相等

函数返回局部变量的地址是非常安全的

```go
var p = f()
func f() *int {
    v := 1
    return &v
}

fmt.Println(f() == f()) // false
```

### 2.3.3 new函数

`new(T)`创建一个未命名的T类型变量, 并返回其地址(地址类型为 *T)

```go
p := new(int)
fmt.Println(*p) // 0
*p = 2 
fmt.Println(*p) // 2

```

### 2.3.4 变量的生命周期

包级别变量的生命周期是整个个程序的执行时间

局部变量是一个动态的生命周期, 变量会一直生存到它变为不可访问

### 2.4.1 多重复制

需要两个边路交互值时

```go
a{i],a[j] = a[j], a[i]
```

## 2.5 类型声明

语法

`type name underlying-type`

类型声明一般出现在包级别, 如果名字开头是大写字母, 其他的包也可以访问它

```go
package tempconv

import "fmt"

type Celsius float64
type Fahrenheit float64

const (
    AbsoluteZeroC Celsius = -273.15
    FreezingC    Celsius = 0
    BoilingC     Celsius = 100
)

func CToF (c Celsius) Fahrenheit { return Fahrenheit(c*9/5 + 32)}
func FToC (f Fahrenheit ) Celsius { return Celsius((f - 32) * 5 / 9)}
```

`Celsius`和`Fahrenheit`的底层即使都使用了float64, 但他们不是相同的类型

所以不能用算数表达式进行比较和合并

对于每一种类型T, 都可以进行类型转换操作T(x), 如果两个类型具有相同的底层类型或者两者都指向相同底层类型变量的未命名指针类型

则两者是可以互相转换的

任何情况下, 运行时的转换不会失败

```go
var c Celsius
var f Fahrenheit

fmt.Println(c == 0) // true
fmt.Println(f >= 0) // true
fmt.Println(c == f) // 编译错误
fmt.Println(c == Celsius(f)) // true
```

下面的声明中Celsius参数c出现在函数名字前面, 名字叫String的方法关联到Celsius类型


```go
func (c Celsius) String() string { return fmt.Sprintf("%g℃", c)}
```

变量通过fmt包作为字符串输出时, 可以控制类型值的线上方式

```go
c := FToC(212.0)

fmt.Println(c.String()) // 100℃
fmt.Println("%v\n", c) // 100℃
fmt.Println("%s\n", c) // 100℃
fmt.Println(c) // 100℃
fmt.Println("%g\n", c) // 100
fmt.Println(float65(c)) // 100
```

## 2.6 包和文件

`gopl.io/ch1/helloworld`包的文件就存储在`$GOPATH/src/gopl.io/chi1/helloworld`中

在Go中, 包中导出的标识符是以大写字母开头

### 2.6.2 包初始化

```go
func init() { ... }
```


init函数不能被调用和被引用

在每一个文件里, 当程序启动时, init函数按照他们的声明顺序自动执行

```go
var cwd string
func init() {
    cwd, err := os.Getwd() //编译错误, 未使用cwd
    if err != nil {
        log.Fatalf(err)
    }
}
```

这里的cwd并不会影响到外部的cwd, 在同一作用域中, cwd并未被声明, 所以`:=`后cwd是init函数的局部变量

并未影响到外部的cwd

可以更改为

```go
var cwd string
func init() {
    var err error
    cwd, err = os.Getwd() 
    if err != nil {
        log.Fatalf(err)
    }
}
```

## 3. 基本数据

### 3.1整数

有符号整数: int8, int16, int32, int64

无符号整数: uint8, uint16, uint32, uint64

此外还有两种类型init和unit 在特定平台上, 大小与原生的有符号整数/无符号整数数值相同

还要衶无符号整数uintptr, 大小不明确, 但足够存放指针, 一般用于底层编程

int、uint、uintptr都不同于类似的整形, 例如int和init32要显示的进行转换

取模运算符`%`仅能运用于整数

整数与整数相除, 都会舍弃小数部分

溢出的高位数部分会被丢弃, 如果原本计算结果是有符号类型, 并且最左侧位是1则会形成负值

```go
var u uint8 = 255
fmt.Println(u, u+1, u*u) // 255 0 1

var i int8 = 127
fmt.Println(i, i+1, i*i) // 127 -128 1
```

## 3.2 浮点数

go的两大浮点数类型有float32, float64

## 3.5 字符串

```go
s := "hello world"
c := s[len(s)] //宕机, 下标越界
```

子串生成语法`s[i,j]`, 包括第一个, 但不包括第j个, 长度为 j -i

i的默认值为0, j的默认值为len(s), j的值小于则宕机

```go
fmt.Println(:5) // hello
fmt.Println(7:) // world
fmt.Println(:) // hello world
```

因为字符串是不可改变的, 所以字符串内部不允许修改

```go
s[0] = 'L' //编译错误
```

### 3.5.1 字符串字面量

"Hello World"

原生字符串字面量的书写形式是`...`, 会把回车符删除(换行符保留)

所以可以用原生字符串写多行字符

## 4. 复合数据类型

### 4.1 数组

```go
var q [3]int = [3]int{1,2,3} //与下面等价
q := [...]int{1,2,3}
```

`...`表示数组长度由初始化数组的元素决定

变量数组具有长度不可变性

```go
q := [3]int{1, 2, 3}
q = [4]int{1,2,3,4} //编译错误
```

指定key的初始化

```go
type Currency int
const (
    USD Currency = iota
    EUR
    GBP
    RMB
)

symbol := [...]string{USD: "$", EUR: "e", GBP: "e", RMB: "¥"}

fmt.Println(RMB, symbol[RMB]) // 3 ¥
```

在这种情况下索引可以任意顺序, 并且有时可省略, 

还有这种情况

```go
r := [...]{99: -1}
```

定义了一个含有100个元素的数组r, 除了最后一个元素值是-1外, 该数组中的其他元素值都为0

如果一个数组的元素是可比较的, 那么这个数组也是可比较的

`==`比较的结果是两边元素的值是否完全相同

```go
a := [2]int{1, 2}
b := [...]int{1, 2}
c := [2]int{1, 3}
fmt.Println(a == b, a == c, b == c) // true false false
d := [3]int{1, 2}
fmt.Println(a == d) // 编译失败 长度不一样无法比较
```

Go把数组和其他类型都看为`值传递`

### 4.2 slice

`slice`表示一个拥有相同元素的可变长度序列

slice一般写成`[]T`

slice有3个属性: 指针, 长度和容量

指针指向数组的第一个可以从slice中访问的元素

长度是slice的元素个数

它不能超过slice的容量

容量大小通常从slice的起始元素到底层数组的最有一个元素间元素的个数

go的内置函数len和cap用来返回slice的长度和容量

slice无法用==来比较是否拥有相同元素

slice唯一可以用`==`的就是判断是否为nil

`if summer == nil {...}`

slice的零值是`nil`

值为`nil`的slice的长度和容量都为0

```go
var s []int // len(s) == 0, s == nil
s = nil // len(s) == 0, s == nil
s = []int(nil) // len(s) == 0, s == nil
s = []int{} // len(s) == 0, s != nil
```

如果想坚持slice是否为空, 最好是`len(s) == 0` 而不是 `s == nil`

使用`make`来创建slice

```go
make([]T, len)
make([]T, len, cap)// 和make([]T, cap)[:len]功能一样
```

第一行创建了容量和长度都是len

而第二行只引用了前len个元素, 但是容量是数组的长度, 为未来的slice元素留出空间

### 4.2.1 append函数

```go
func appendInt(x []int, y int) []int {
    var z []int
    zLen := len(x) + 1
    if zLen <= cap(x) {
        // x仍有空间
        z = x[:zLen]
    } else {
        // slice 已无空间
        // 为了达到分摊线性复杂性, 容量扩展一倍
        zCap := zLen
        if zCap < 2*len(x) {
            zCap = 2 * len(x)
        }
        z = make([]int, zLen, zCap)
        copy(z, x)
    }
    z[len(x)] = y
    return z
}
```

copy函数中, 源slice和目标slice 可能对应同一个底层数组

甚至可能出现元素重叠

copy函数的返回值是 返回实际复制的元素个数

要更新一个slice的指针, 长度或容量都必须这样

```go
runes = append(runes, r)
```

### 4.2.2 slice就地修改

例如 删除一个slice里的空字符串

```go
func noEmpty(strings []string) []string {
    i := 0
    for _, s := range strings {
        if s != "" {
            strings[i] = s
            i++
        }
    }
    return strings[:i]
}
```

这样会导致底层数组会被更改

```go
data := []string{"one", "", "three"}
fmt.Printf("%q\n", noEmpty(data)) // ["one", "three"]
fmt.Printf("%q\n", data) // ["one", "three", "three"]
```

所以通常我们这么写

```go
data = noEmpty(data)
```

在slice中取出某一个元素

```go
func remove(slice []int ,i int ) []int {
    sLen := len(slice)
    if i < 0 || i >= sLen {
        return slice
    } 
    copy(slice[i:], slice[i+1:])
    return slice[:len(slice) - 1]
}

func main() {
    s :=  remove([]int{5, 6, 7, 8, 9}, 2)
	fmt.Println(s)
}
```

copy函数覆盖的逻辑为, 源slice(第二个参数)按对应索引位复制到目标slice

所以上面的copy函数是, `copy([]int{7, 8, 9}, []int{8, 9})`

源slice的第0位复制目标slice的第0位, 源slice的第1位复制目标slice的第1位

所以最终`slice[i:]`会被改变成`[8, 9, 9]` 

整个slice就位`[5, 6, 8 ,9 ,9]`, 再删掉最后一位即可

## 4.3 map

散列表是设计精妙, 用途广泛的数据结构之一

是一个拥有键值对元素的无序集合

键的值是唯一的

无论这个散列表有多大, (获取/ 更新/ 删除) 这些操作基本是通过常量时间的键比较就可以完成的

map中所有的键 都拥有相同的数据类型, 所有的值也拥有相同的类型

> 创建

创建map的方式

```go
args := make(map[string]int)

// 或通过字面量

args := map[string]int{
    "alice": 14,
    "charlie": 34
}

// 等价于

args := make(map[string]int)

args["slice"] = 14
args["charlie"] = 34

```

> 删除

```go
delete(args, "alice") //移除元素args["slice"]
```

即使键不在map中, 上面的操作也是安全的

map通过键来找值, 如果对应值不存在, 就返回该类型的零值

例如下面的代码是可行的

```go
args["boo"] = args["boo"] + 1 // 1
```

> 获取

但是map元素不是一个变量, 不可以获取其地址, 因为map增长可能导致已有元素被重新散列到新的位置

遍历map

```go
for name, age := range args {
    fmt.Printf("%s\t%d\n", name, age)
}
```

map的元素迭代顺序是不固定的

map的零值是nil

```go
var ages map[string]int
fmt.Println(ages == nil ) // true
fmt.Println(len(ages) == 0 ) // true

ages["carol"] = 21 //宕机, 为零值map中的项赋值
```

设置元素之前, 必须初始化map

通过下标的方式访问map中的元素总是会有值

- 键在map中, 返回对应的值
- 键不在map中, 返回值的零值

判断键是否存在

```go
if age, ok := ages["bob"]; !ok {
    /* bob 不是map中的键 */
}
```

map是不可比较的, 只能跟nil对比

map的键必须是可比较的

## 4.4 结构体

结构体是将零个或者多个任意类型的命名变量组合在一起的聚合数据类型

点号同样可以用在结构体的指针上

```go
var employeeOfTheMonth *Employee = &dilbert
employeeOfTheMonth.Position += " (proactive team player)"

// 最后一句等价于

(*employeeOfTheMonth).Position += " (proactive team player)"
```

结构体A的属性中不能嵌套同一结构体A

但是结构体A中的属性可以嵌套结构A的指针类型

对创建递归的数据结构比较有用, 比如链表和数

下面实现一个二叉树插入排序的例子

```go
type tree struct {
    value int 
    lef, right *tree
}

func Sort(values []int) {
    var root *tree
    for _, v := range values {
        add(root, v)
    }
    appendValues(values[:0], root)
}

func appendValues(values []int, t *tree) []int {
    if t != nil {
        values = appendValues(values, t.left)
        values = append(values, t.value)
        values = appendValues(values, t.right)
    }
    return values
}


func add(t *tree, v) *tree {
    if (t == nil) {
        // 等价于 *tree{value: value}
        t = new(tree)
        t.value = value
        return t
    }
    if (value < t.value) {
        t.left = add(t.left, value)
    } else {
        t.right = add(t.right, value)
    }
    return t
}


```

结构体的零值是由结构体成员的零值组成

## 12 反射

在编译时不知道类型的情况下, 可更新变量、在运行时查看值、调用方法以及直接对它们的布局进行操作

这种机制称为反射

### 12.1 为什吗使用反射

需要写一个函数有能力统一处理各种值类型的函数, 而这些类型可能

- 无法共享同一接口
- 布局未知
- 在我们设计函数时还粗存在(例如`fmt.Printf`)

### 12.2 reflect.Type和reflect.Value

反射功能主要由`reflect`包提供, 定义了两个重要的类型

`Type`: Go语言的一个类型

`reflect.TypeOf`函数接受任何的`interface{}`参数, 并把接口中的动态类型以`reflect.Type`形式返回

```go
t := reflect.Typeof(3) // 一个reflect.Type
fmt.Println(t.String()) // int
fmt.Println(t) // int
```

把一个具体值赋给接口类型时会发生一个隐式类型转换, 转换会生成包含两个部分的接口值

- 动态类型部分是操作数的类型(int)
- 动态值部分是操作数的值(3)

reflect.Typeof返回一个接口值对应的动态类型, 所以它返回总是具体类型(而不是接口类型)

```go
var w io.Writer = os.Stdout
fmt.Println(reflect.Typeof(w)) // 返回是"*os.File" 而非io.Writer
```

reflect.Valueof函数接受任意的interface{}, 并将接口的动态值以reflect.Value的形式返回

reflect.Valuof的逆操作是reflect.Value.Interface, 返回一个interface{}接口值

```go
v := reflect.Valueof(3) // 一个reflect.Value
x := v.Interface() // an interface{}
i := x.(int) // an int
fmt.Printf("%d\n", i) // 3
```

reflect.Value.Kind() 方法可以获取少数的几种类型

- 基础类型Bool, string, 各种数字类型
- 聚合类型Array和Struct
- 引用类型: Chan, Func, Ptr, Slice, Map, Interface
- Invalid类型 表示没有任何值, reflect.Value的零值就是Invalid类型

```go
package format

import (
    "reflect"
    "strconv"
)

func Any(value interface{}) string {
    return formatAtom(reflect.Valueof(value))
}

func formatAtom(v reflect.Value) string {
    switch v.Kind() {
        case reflect.Invalid:
            return "invalid"
        case reflect.Int, reflect.Int8, reflect.Int16,
            reflect.Int32, reflect.Int64:
            return strconv.formatUnit(v.Unit(), 10)
        // 省略浮点数和复数类型
        case reflect.Bool:
            return strconv.FormatBool(v.Bool())
        case reflect.String:
            return strconv.Quote(v.String())
        case reflect.Chan, reflect.Func, reflect.Ptr, reflect.Slice, reflect.Map:
            return v.Type().String() + " 0x" + strconv.FormatUnit(uint64(v.Pointer(), 16))
        default: // reflect.Array, reflect.Struct, reflect.Interface
            return v.Type().String() + " value"
    }
}

var x int64 = 1
var d time.Duration = 1 * time.Nanosecond
fmt.Println(format.Any(x)) // 1
fmr.Println(format.Any(d)) // 1
fmt.Println(format.Any([]int64{x})) // []int64 0x8202b87b0
fmt.Println(format.Any([]time.Duration{d})) // []time.Duration 0x8202b87e0
```