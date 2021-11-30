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

