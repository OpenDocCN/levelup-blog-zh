# 我关于 Go 的原始笔记——最佳实践、并发性、内存等等

> 原文：<https://levelup.gitconnected.com/my-raw-notes-on-go-best-practices-concurrency-memory-and-beyond-5d82c1dae110>

![](img/147d65c95deff6c4b6ab7b883f8c510c.png)

在过去的几个月里，我对一些我感兴趣并想深入了解的关于围棋的话题做了一些深入的研究。虽然大多数已经掌握这门语言相当长时间的人可能已经知道这些话题，但总有一些新手可能会有所帮助。所以我没有把笔记藏在心里，而是把它们公之于众。

# 内存:堆栈与堆

*   Go 的运行时为每个 goroutine 创建一个堆栈。
*   Go 的运行时并不在每次 goroutine 退出时清除堆栈，它只是将它标记为无效，以便其他程序或例程可以使用它。
*   Go 运行时足够敏锐，知道对 salutation 变量的引用仍被持有，因此会将内存转移到堆中，以便 goroutines 可以继续访问它。这就是所谓的**逃逸分析。**
*   内存分配的经验法则

```
Sharing down **typically** stays on the stackSharing up **typically** escapes to the heap
```

只有编译器知道什么时候典型地不是典型地

为了获得准确的信息，用 gcflags 构建您的程序

```
go build -gcflags=”-m -l” program.go
```

*   什么时候变量会转义到堆中？

```
- If it could possibly be referenced after the function returns- When a value is too big for the stack- When the compiler doesn’t know the size in compile time
```

*   不要做过早的优化，依靠数据，在解决问题之前发现问题。使用分析器和分析工具找到根。

# 戈鲁廷斯

*   每个 Go 程序至少有一个 goroutine:主 Go routine*，它是在程序开始时自动创建并启动的。*
*   *goroutine 是一个并发运行的函数。注意并发和并行的区别。*
*   *它们不是操作系统线程，而是一种更高层次的抽象，称为协程。协程只是简单的非抢占式并发子程序(它们不能被中断)。*
*   *Go 遵循 fork-join 模型来启动和等待 goroutines。*
*   ***防泄漏**:成功缓解这种情况的方法是在父 goroutine 和其子之间建立一个信号，允许父向其子发送取消信号。按照惯例，这个信号通常是一个名为 *done* 的只读通道。父 goroutine 将该通道传递给子 goroutine，然后在想要取消子 goroutine 时关闭该通道。*
*   *如果一个 goroutine 负责创建一个 goroutine，它还负责确保它可以停止该 goroutine。*
*   *为了更好地处理 goroutines 内部的错误，创建一个包装可能的结果和可能的错误的结构。返回此结构的通道。*

# *同步包*

***等待组***

*当您不关心并发操作的结果，或者您有其他方法收集它们的结果，而必须等待一组并发操作完成时，请使用它。*

***互斥体和 rw 互斥体***

*锁定一段代码，这样只有一个 goroutine 可以访问它。最佳实践是使用 defer 语句锁定并在下一行解锁。*

*RWMutex 为您提供了对何时授予读或写权限的细粒度控制。*

# *频道*

*   *渠道是信息流的管道；值可以沿着通道传递，然后向下游读出*
*   *双向或单向*
*   *Go 中的频道被阻塞。这意味着，任何试图写入已满通道的 goroutine 都将等待，直到该通道被清空；任何试图从空通道读取的 goroutine 都将等待，直到至少有一个项目被放置在该通道上。*
*   *读数发送两个值，value 和 ok。如果通道关闭，则 value 是通道类型的默认值，ok 为 false。通道范围检查正常值。*
*   *通道的创建可能应该与将要在通道上执行写操作的 goroutines 紧密耦合，这样我们就可以更容易地推断通道的行为和性能。*
*   *拥有通道的 goroutine 应该:实例化通道，执行写操作，或者将所有权传递给另一个 goroutine，关闭通道，封装前面的三个东西，并通过 reader 通道公开它们。*

# *Select 和 for-select 语句*

*   *在 select 语句中，对分支的评估是同时进行的，首先执行满足条件的分支。如果有多个选项，go 的运行时会进行伪随机选择*
*   *在 Go 中使用*
*   *Default clause in a select statement is executed if none is ready. Mixed with for loop to create fallthrough and check for other options every time*

```
*for { select { case <-done: 

      return default: // Do non-preemptable work }}*
```

# *Pipelines*

*When constructing pipelines use a generator function to convert input to channel*

```
*func IntGenerator(done <-chan interface{}, integers ...int) <-chan int { 
  intStream := make(chan int) 
  go func() {
    defer close(intStream)
    for _, i := range integers {
     select {
       case <-done:
        return
       case intStream <- i:
     }
    }
  }()
 return intStream
}*
```

*Fan-out, fan-in: reuse a single stage of our pipeline on multiple goroutines in an attempt to parallelise pulls from an upstream stage.*

*When to fan-out?*

*• It doesn’t rely on values that the stage had calculated before.*

*• It takes a long time to run.*

# *Slices, declaration vs initialisation*

```
*var arr []string // declares slice, nil valuearr := make([]string, 3) //declares and initialises slice [“”,””,””]*
```

# *Interfaces*

## *Compile time check*

*Interface implementation is done implicitly and checked in runtime. If you do not conform to an interface then an error will raise in production.*

*Add this line to do a compile time check of your interface implementation, will fail to compile if for example 【 ever stops matching the 【 interface.*

```
*var _ http.Handler = (*Handler)(nil)*
```

## *Receivers matter*

```
*package mainimport (“fmt”)type Animal interface { Speak() string}type Dog struct {}func (d Dog) Speak() string { return “Woof!”}type Cat struct {}func (c *Cat) Speak() string { return “Meow!”}func main() { animals := []Animal{Dog{}, Cat{}} for _, animal := range animals { fmt.Println(animal.Speak()) }}// Output./prog.go:26:32: cannot use Cat literal (type Cat) as type Animal in slice literal:Cat does not implement Animal (Speak method has pointer receiver*
```

## *Interface segregation principle*

```
**A great rule of thumb for Go is* ***accept interfaces, return structs****.
–Jack Lindamood**
```

## *Resources*

*   *[内存管理](https://deepu.tech/memory-management-in-golang/)*
*   *[了解分配:堆栈和堆—GopherCon SG 2019(Jacob Walker)](https://www.youtube.com/watch?v=ZMZpH4yT7M0&list=WL&index=14&t=14s&ab_channel=SingaporeGophers)*
*   *【2016 年 Golang 英国会议—戴夫·切尼— SOLID Go 设计*
*   *[MapReduce 在走](https://appliedgo.net/mapreduce/)*
*   *[围棋中的并发，凯瑟琳·考克斯-布戴](https://www.oreilly.com/library/view/concurrency-in-go/9781491941294/)*
*   *[https://github.com/uber-go/guide/blob/master/style.md](https://github.com/uber-go/guide/blob/master/style.md)*
*   *[https://github.com/golang-standards/project-layout](https://github.com/golang-standards/project-layout)*
*   *[https://github.com/mastanca/go-concurrent-utils](https://github.com/mastanca/go-concurrent-utils)*