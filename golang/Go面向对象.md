## 1 类型系统

顾名思义，类型系统是指一个语言的类型体系结构。一个典型的类型系统通常包含如下基本内容：

- 基础类型，如byte、int、bool、float等;
- 复合类型，如数组、结构体、指针等;
- 可以指向任意对象的类型(Any类型);
- 值语义和引用语义;
- 面向对象，即所有具备面向对象特征(比如成员方法)的类型;
- 接口。

### 1.1 为类型添加方法

在Go语言中，你可以给任意类型(包括内置类型，但不包括指针类型)添加相应的方法， 例如

```go
type Integer int

func (a Integer) Less(b Integer) bool {
  return a < b
}
```

Integer和int没有本质不同，只是新增了一个方法

```go
func main() {
  var a Integer = 1
  if a.Less(2) {
    fmt.Println(a,"Less 2")
  }
}
```

面向对象和面向过程对比

```go
func (a Integer) Less(b Integer) bool { // 面向对象 
  	return a < b
}
func Integer_Less(a Integer, b Integer) bool { // 面向过程 
 	 return a < b
}

a.Less(2) // 面向对象的用法
Integer_Less(a, 2) // 面向过程的用法
```

Go语言中的面向对象最为直观，也无需支付额外的成本。如果要求对象必须以指针传递， 这有时会是个额外成本，因为对象有时很小(比如4字节)，用指针传递并不划算。

只有在需**要修改对象的时候，才必须用指针**。它不是Go语言的约束，而是一种自然约束。 举个例子:

```go
func (a *Integer) Add(b Integer) {
  *a += b
}
```

使用

```go
func main() {
  var a Integer = 1
  a.Add(2)
  fmt.Println("a=",a)
}
```

