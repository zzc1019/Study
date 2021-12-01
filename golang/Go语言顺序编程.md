## 1 变量

### 1.1 变量声明

go语言引入关键字var，类型信息放在变量名之后

```go
var v1 int
var v2 string
var v3 [10]int   //数组
var v4 []int     //数组切片，动态数组
var v5 struct {
  	f int
}
var v6 *int
var v7 map[string]int
var v8 func(a int) int
```

多个变量放置在一起

```go
var (
		v1 int
		v2 string
)
```

### 1.2 变量初始化

```
var v1 int = 10
var v2 = 10
v3 := 10
```

以上三种用法的效果是完全一样的。与第一种用法相比，第三种用法需要输入的字符数大大 减少，是懒程序员和聪明程序员的最佳选择

### 1.3 变量赋值

在Go语法中，变量初始化和变量赋值是两个不同的概念。下面为声明一个变量之后的赋值过程：

`var v10 int v10 = 123`

Go语言的变量赋值与多数语言一致，但Go语言中提供了C/C++程序员期盼多年的多重赋值功 能，比如下面这个交换i和j变量的语句:

`i, j = j, i`

### 1.4 匿名变量

我们在使用传统的强类型语言编程时，经常会出现这种情况，即在调用函数时为了获取一个 值，却因为该函数返回多个值而不得不定义一堆没用的变量。在Go中这种情况可以通过结合使 用多重返回和匿名变量来避免这种丑陋的写法，让代码看起来更加优雅。

假设GetName()函数的定义如下，它返回3个值，分别为firstName、lastName和 nickName:

```go
func GetName() (firstName, lastName, nickName string) { 
  return "May", "Chan", "Chibi Maruko"
}
```

 若只想获得nickName，则函数调用语句可以用如下方式编写:

```go
    _, _, nickName := GetName()
```

## 2 常量

在Go语言中，常量是指编译期间就已知且不可改变的值。常量可以是数值类型(包括整型、浮点型和复数类型)、布尔类型、字符串类型等。

### 2.1 字面常量

所谓字面常量(literal)，是指程序中硬编码的常量，如:

```go
-12
3.14159265358979323846 // 浮点类型的常量 
3.2+12i // 复数类型的常量 
true // 布尔类型的常量 
"foo" // 字符串常量
```

在其他语言中，常量通常有特定的类型，比如-12在C语言中会认为是一个int类型的常量。 如果要指定一个值为12的long类型常量，需要写成-12l，这有点违反人们的直观感觉。Go语言 的字面常量更接近我们自然语言中的常量概念，它是无类型的。只要这个常量在相应类型的值域 范围内，就可以作为该类型的常量，比如上面的常量12，它可以赋值给int、uint、int32、 int64、float32、float64、complex64、complex128等类型的变量。

### 2.2 常量定义

const关键字给常量定义名字

```go
const Pi float64 = 3.14159265358979323846
const zero = 0.0 
const (
	size int64 = 1024 
  eof = -1
)
const u, v float32 = 0, 3  // u = 0.0, v = 3.0，常量的多重赋值
const a, b, c = 3, 4, "foo" //a=3,b=4,c="foo", 无类型整型和字符串常量

```

Go的常量定义可以限定常量类型，但不是必需的。如果定义常量时没有指定类型，那么它 与字面常量一样，是无类型常量。

常量定义的右值也可以是一个在编译期运算的常量表达式，比如`const mask = 1 << 3 `由于常量的赋值是一个编译期行为，所以右值不能出现任何需要运行期才能得出结果的表达式，比如试图以如下方式定义常量就会导致编译错误:

`const Home = os.GetEnv("HOME") `

原因很简单，os.GetEnv()只有在运行期才能知道返回结果，在编译期并不能确定，所以无法作为常量定义的右值。

### 2.3 预定义常量

Go语言预定义了这些常量:true、false和iota。

iota比较特殊，可以被认为是一个可被编译器修改的常量，**在每一个const关键字出现时被重置为0**，然后在下一个const出现之前，每出现一次iota，其所代表的数字会自动增1。

```go
const (
		c0 = iota   //iota=0
    c1 = iota   // 1
    c2 = iota   // 2
)
const (
    a = 1 << iota  //iota=0 a=1
    b = 1 << iota  //iota=1 a=2
    c = 1 << iota  //iota=2 a=4
)
```

如果两个const的赋值语句的表达式是一样的，那么可以省略后一个赋值表达式。因此，上面的前两个const语句可简写为:

```go
const (
   c0 = iota
   c1 
   c2
)
const (
   a = 1 << iota
   b
   c
)
```

### 2.4 枚举

​		枚举指一系列相关的常量，比如下面关于一个星期中每天的定义。通过上一节的例子，我们 看到可以用在const后跟一对圆括号的方式定义一组常量，这种定义法在Go语言中通常用于定义 枚举值。Go语言并不支持众多其他语言明确支持的enum关键字。

​		下面是一个常规的枚举表示法，其中定义了一系列整型常量:

```go
const (
		Sunday = iota
    Monday
    Tuesday
    Wednesday
    Thursday
    Friday
    Saturday
    numberOfDays  // 这个常量没有导出
)
 
```

同Go语言的其他符号(symbol)一样，以大写字母开头的常量在包外可见。

以上例子中`numberOfDays`为包内私有，其他符号则可被其他包访问。

## 3 类型

Go语言内置以下这些基础类型:

- 布尔类型:bool。
- 整型:int8、byte、int16、int、uint、uintptr等。 
- 浮点类型:float32、float64。
- 复数类型:complex64、complex128。
- 字符串:string。
- 字符类型:rune。
- 错误类型:error。

此外，Go语言也支持以下这些复合类型: 

- 指针(pointer)
- 数组(array)
- 切片(slice)
- 字典(map)
- 通道(chan)
- 结构体(struct)
- 接口(interface)