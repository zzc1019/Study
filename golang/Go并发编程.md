## 1 并发基础

-  多进程。多进程是在操作系统层面进行并发的基本模式。同时也是开销最大的模式。Linux平台上，很多工具链正是采用这种模式在工作。比如某个Web服务器，它会有专门 的进程负责网络端口的监听和链接管理，还会有专门的进程负责事务和运算。这种方法 的好处在于简单、进程间互不影响，坏处在于系统开销大，因为所有的进程都是由内核 管理的
- 多线程。多线程在大部分操作系统上都属于系统层面的并发模式，也是我们使用最多的 最有效的一种模式。目前，我们所见的几乎所有工具链都会使用这种模式。它比多进程 的开销小很多，但是其开销依旧比较大，且在高并发模式下，效率会有影响。 3
- 基于回调的非阻塞/异步IO。这种架构的诞生实际上来源于多线程模式的危机。在很多 高并发服务器开发实践中，使用多线程模式会很快耗尽服务器的内存和CPU资源。而这 种模式通过事件驱动的方式使用异步IO，使服务器持续运转，且尽可能地少用线程，降 低开销，它目前在Node.js中得到了很好的实践。但是使用这种模式，编程比多线程要复 杂，因为它把流程做了分割，对于问题本身的反应不够自然。
- 协程。协程(Coroutine)本质上是一种用户态线程，不需要操作系统来进行抢占式调度， 且在真正的实现中寄存于线程中，因此，系统开销极小，可以有效提高线程的任务并发 性，而避免多线程的缺点。使用协程的优点是编程简单，结构清晰;缺点是需要语言的 支持，如果不支持，则需要用户在程序中自行实现调度器。目前，原生支持协程的语言 还很少。

## 2 协程

​		执行体是个抽象的概念，在操作系统层面有多个概念与之对应，比如操作系统自己掌管的 进程(process)、进程内的线程(thread)以及进程内的协程(coroutine，也叫轻量级线程)。与 传统的系统级线程和进程相比，协程的最大优势在于其“轻量级”，可以轻松创建**上百万**个而不 会导致系统资源衰竭，而线程和进程通常最多也不能超过1万个。这也是协程也叫轻量级线程的 原因。

## 3 goroutine

goroutine是Go语言中的轻量级线程实现，由Go运行时(runtime)管理。

```go
func Add(x, y int) { z := x + y
        fmt.Println(z)
}

//执行
go Add(1,2)
```

go是Go语言中最重要的关键字，这一点从Go语言本身的命名即可看出。

在一个函数调用前加上go关键字，这次调用就会在一个新的goroutine中并发执行。当被调用 的函数返回时，这个goroutine也自动结束了。需要注意的是，如果这个函数有返回值，那么这个 返回值会被丢弃。

例子：

```go
package main
import "fmt"
func Add(x, y int) { z := x + y
	fmt.Println(z)
}
func main() {
	for i := 0; i < 10; i++ {
		go Add(i, i) }
}
```

在上面的代码里，我们在一个for循环中调用了10次Add()函数，它们是并发执行的。可是

当你编译执行了上面的代码，就会发现一些奇怪的现象: “什么?!屏幕上什么都没有，程序没有正常工作!”

Go程序从初始化main package并执行main()函数开始，当main()函数返回时，程序退出， 且程序并不等待其他goroutine(非主goroutine)结束。 6 对于上面的例子，主函数启动了10个goroutine，然后返回，这时程序就退出了，而被启动的

执行Add(i, i)的goroutine没有来得及执行，所以程序没有任何输出。

## 4 并发通信

​		事实上，不管是什么平台，什么编程语言，不管在哪，并发都是一个大话题。话题大小通常 也直接对应于问题的大小。并发编程的难度在于协调，而协调就要通过交流。从这个角度看来，并发单元间的通信是最大的问题。 

​		在工程上，有两种最常见的并发通信模型:共享数据和消息。 共享数据是指多个并发单元分别保持对同一个数据的引用，实现对该数据的共享。被共享的数据可能有多种形式，比如内存数据块、磁盘文件、网络数据等。在实际工程应用中最常见的无 疑是内存了，也就是常说的共享内存。

​		Go语言提供的是另一种通信模型，即**以消息机制而非共享内存作为通信方式**。 消息机制认为每个并发单元是自包含的、独立的个体，并且都有自己的变量，但在不同并发 单元间这些变量不共享。**每个并发单元的输入和输出只有一种，那就是消息**。这有点类似于进程 的概念，每个进程不会被其他进程打扰，它只做好自己的工作就可以了。不同进程间靠消息来通信，它们不会共享内存。 Go语言提供的消息通信机制被称为channel，接下来我们将详细介绍channel。现在，让我们用Go语言社区的那句著名的口号来结束这一小节: “不要通过共享内存来通信，而应该通过通信来共享内存。”

## 5 channel

​		channel是Go语言在语言级别提供的goroutine间的通信方式。我们可以使用channel在两个或 多个goroutine之间传递消息。channel是进程内的通信方式**，因此通过channel传递对象的过程和调 用函数时的参数传递行为比较一致**，比如也可以传递指针等。如果需要**跨进程通信，我们建议用分布式系统的方法来解决**，比如使用Socket或者HTTP等通信协议。Go语言对于网络方面也有非常完善的支持。

​		channel是类型相关的。也就是说，一个channel只能传递一种类型的值，这个类型需要在声 明channel时指定。如果对Unix管道有所了解的话，就不难理解channel，可以将其认为是一种类 型安全的管道。

```go
package main
import "fmt"
func Count(ch chan int, num int) {
	//
	ch <- 1
	fmt.Println("go",num,": Counting")

}
func main() {
	chs := make([]chan int, 10)
	for i := 0; i < 10; i++ {
		chs[i] = make(chan int)
		go Count(chs[i],i)
	}
	for j, ch := range(chs) {
		<-ch
		fmt.Println("go",j,": Reading")
	}
}

```

结果：

```go
/private/var/folders/z_/wkr8jg7d23lfr5qlxl89h4sc0000gy/T/GoLand/___go_build_learn3_go
go 0 : Counting
go 0 : Reading
go 1 : Reading
go 2 : Reading
go 3 : Reading
go 4 : Reading
go 5 : Reading
go 6 : Reading
go 7 : Reading
go 1 : Counting
go 8 : Counting
go 2 : Counting
go 7 : Counting
go 8 : Reading
go 9 : Reading
```

在这个例子中，我们定义了一个包含10个channel的数组(名为chs)，并把数组中的每个 channel分配给10个不同的goroutine。在每个goroutine的Add()函数完成后，我们通过ch <- 1语 句向对应的channel中写入一个数据。在这个channel被读取前，这个操作是阻塞的。在所有的 goroutine启动完成后，我们通过<-ch语句从10个channel中依次读取数据。在对应的channel写入 数据前，这个操作也是阻塞的。这样，我们就用channel实现了类似锁的功能，进而保证了所有 goroutine完成后主函数才返回。是不是比共享内存的方式更简单、优雅呢?

### 5.1 基本语法

声明

```go
//一般channel的声明形式
var chanName chan ElementType
var ch chan int
car m map[string] chan bool //我们声明一个map，元素是bool型的channel:
```

定义一个channel

```go
ch := make(chan int)
```

channel读写

```go
ch <- value
//向channel写入数据通常会导致程序阻塞，直到有其他goroutine从这个channel中读取数据。从 channel中读取数据的语法是
value := <-ch
//如果channel之前没有写入数据，那么从channel中读取数据也会导致程序阻塞，直到channel 中被写入数据为止。
```

### 5.2 select

​		早在Unix时代，select机制就已经被引入。通过调用select()函数来监控一系列的文件句柄，一旦其中一个文件句柄发生了IO动作，该select()调用就会被返回。后来该机制也被用于 实现高并发的Socket服务器程序。Go语言直接在语言级别支持select关键字，用于**处理异步IO** 问题。

​		select的用法与switch语言非常类似，由select开始一个新的选择块，每个选择条件由 case语句来描述。与switch语句可以选择任何可使用相等比较的条件相比，select有比较多的 限制，其中最大的一条限制就是每个case语句里必须是一个IO操作，大致的结构如下:

```go
select {
		case <-chan1:
		// 如果chan1成功读到数据，则进行该case处理语句 
 		case chan2 <- 1:
		// 如果成功向chan2写入数据，则进行该case处理语句 
  	default:
		// 如果上面都没有成功，则进入default处理流程 
}
```

每个 case语句都必须是一个面向channel的操作

```go
//随即向ch中写入一个0或者一个1
ch := make(chan int, 1) 
for {
		select {
				case ch <- 0:
				case ch <- 1: 
      }
		i := <-ch
    fmt.Println("Value received:", i)
}
```

### 5.3 缓冲机制

之前我们示范创建的都是不带缓冲的channel，这种做法对于传递单个数据的场景可以接受， 但对于需要持续传输大量数据的场景就有些不合适了。接下来我们介绍如何给channel带上缓冲， 从而达到消息队列的效果。

要创建一个带缓冲的channel，其实也非常容易: 

`c := make(chan int, 1024)`

即使没有读取方，写入方也可以一直往channel里写入，在缓冲区被 填完之前都不会阻塞。

从带缓冲的channel中读取数据可以使用与常规非缓冲channel完全一致的方法，但我们也可 以使用range关键来实现更为简便的循环读取:

```go
for i := range c {
  fmt.Println("Received:",i)
}
```

### 5.4 超时机制

​		在之前对channel的介绍中，我们完全没有提到错误处理的问题，而这个问题显然是不能被忽 略的。在并发编程的通信过程中，最需要处理的就是超时问题，即向channel写数据时发现channel 3 已满，或者从channel试图读取数据时发现channel为空。如果不正确处理这些情况，很可能会导 致整个goroutine锁死。

使用channel时需要小心，比如对于以下这个用法: 

`i := <-ch`

不出问题的话一切都正常运行。但如果出现了一个错误情况，即永远都没有人往ch里写数据，那 么上述这个读取动作也将永远无法从ch中读取到数据，导致的结果就是整个goroutine永远阻塞并 没有挽回的机会。如果channel只是被同一个开发者使用，那样出问题的可能性还低一些。但如果 一旦对外公开，就必须考虑到最差的情况并对程序进行保护。

Go语言没有提供直接的超时处理机制，但我们可以利用select机制。虽然select机制不是 专为超时而设计的，却能很方便地解决超时问题。因为select的特点是只要其中一个case已经 完成，程序就会继续往下执行，而不会考虑其他case的情况。

基于此特性，我们来为channel实现超时机制：

```go
//首先，实现并执行一匿名的超市等待函数
timeout := make(chan bool,1)
go func() {
  time.Sleep(1e9)  //等待1s
  timeout <- true
}()

//利用channel
select {
  case <- ch:
  	//从ch读到数据
  case <-timeout:
  	//一直没有从ch中读到数据，但从timeout中读到了数据
}
```



​		这样使用select机制可以避免永久等待的问题，因为程序会在timeout中获取到一个数据 后继续执行，无论对ch的读取是否还处于等待状态，从而达成1秒超时的效果。

### 5.5 channel的传递

​		需要注意的是，在Go语言中channel本身也是一个原生类型，与map之类的类型地位一样，因 此channel本身在定义后也可以通过channel来传递。

​		我们可以使用这个特性来实现*nix上非常常见的管道(pipe)特性。管道也是使用非常广泛 的一种设计模式，比如在处理数据时，我们可以采用管道设计，这样可以比较容易以插件的方式 增加数据的处理流程。

​		下面我们利用channel可被传递的特性来实现我们的管道。为了简化表达，我们假设在管道中 传递的数据只是一个整型数，在实际的应用场景中这通常会是一个数据块。

```go
type PipeData struct { 
  value int
	handler func(int) int
	next chan int 
}
```

```go
func handle(queue chan *PipeData) { 
  	for data := range queue {
 			data.next <- data.handler(data.value)
    }
}

```

### 5.6 单向channel

单向channel只能用于发送或接收数据。

假如一个channel真的只能读，那么肯定只会是空的，因为你没机会往里面写数 据。同理，如果一个channel只允许写，即使写进去了，也没有丝毫意义，因为没有机会读取里面 的数据。所谓的单向channel概念，其实只是对channel的一种使用限制。

**声明变量**

```go
var ch1 chan int // ch1是一个正常的channel，不是单向的 2
var ch2 chan<- float64// ch2是单向channel，只用于写float64数据
var ch3 <-chan int // ch3是单向channel，只用于读取int数据
```

**初始化**

```go
ch4 := make(chan int)
ch5 := <-chan int(ch4) //ch5 是ch4的单向读取channel
ch6 := chan<- int(ch4) //ch6 是ch4的单向写channel
```

### 5.7 关闭channel

使用Go语言的内置close() 可以关闭

```go
close(ch)
```

如何判断一个channel是否已经被关 闭?我们可以在读取的时候使用多重返回值的方式:

`x, ok := <-ch` 这个用法与map中的按键获取value的过程比较类似，只需要看第二个bool返回值即可，如

果返回值是false则表示ch已经被关闭。

## 6 同步

### 6.1 同步锁

Go语言包中的sync包提供了两种锁类型:sync.Mutex和sync.RWMutex。Mutex是最简单 的一种锁类型，同时也比较暴力，当一个goroutine获得了Mutex后，其他goroutine就只能乖乖等 到这个goroutine释放该Mutex。RWMutex相对友好些，是经典的单写多读模型。在读锁占用的情 况下，会阻止写，但不阻止读，也就是多个goroutine可同时获取读锁(调用RLock()方法;而写 锁(调用Lock()方法)会阻止任何其他goroutine(无论读和写)进来，整个锁相当于由该goroutine 独占。从RWMutex的实现看，RWMutex类型其实组合了Mutex:

```go
type RWMutex struct { 
  w Mutex
	writerSem uint32 
  readerSem uint32 
  readerCount int32 
  readerWait int32
}
```

对于这两种锁类型，任何一个Lock()或RLock()均需要保证对应有Unlock()或RUnlock() 调用与之对应，否则可能导致等待该锁的所有goroutine处于饥饿状态，甚至可能导致死锁。锁的 典型使用模式如下:

```go
var l sync.Mutex
func foo() {
  l.Lock()
  defer l.Unlock()
  //...
}
```

### 6.2 全局唯一性操作

对于从全局的角度只需要运行一次的代码，比如全局初始化操作，Go语言提供了一个Once

类型来保证全局的唯一性操作，具体代码如下:

```go
var a string
var once sync.Once

func setup() {
  a = "hello,world"
  
}

func doprint() {
  once.Do(setup)
  fmt.Print(a)
}

func twoprint() {
  go doprint() 
  go doprint()
}
```

如果这段代码没有引入Once，setup()将会被每一个goroutine先调用一次，这至少对于这个

例子是多余的。在现实中，我们也经常会遇到这样的情况。Go语言标准库为我们引入了Once类 型以解决这个问题。**once的Do()方法可以保证在全局范围内只调用指定的函数一次(这里指 setup()函数)**，而且所有其他goroutine在调用到此语句时，将会先被阻塞，直至全局唯一的 once.Do()调用结束后才继续。

