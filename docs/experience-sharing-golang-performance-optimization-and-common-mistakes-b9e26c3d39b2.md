# 经验分享:Golang 性能优化和常见错误

> 原文：<https://levelup.gitconnected.com/experience-sharing-golang-performance-optimization-and-common-mistakes-b9e26c3d39b2>

Golang 性能优化

![](img/fed0bd2bdedce8caa00f5ba448b11a43.png)

照片由[布里特·福勒](https://unsplash.com/@brittfowler?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

**内存优化。**

***#1。小对象合并。***

在堆内存上频繁创建和销毁小对象，会导致内存碎片，一般使用内存池。

Golang 的内存机制也是一个内存池，每个 span 大小为 4KB，并维护一个缓存，缓存中有一个列表数组

数组存储一个链表，就像 HashMap 的 zipper 方法一样，数组的每个网格代表的内存大小不一样，64 位机是以 8 字节为单位的。

例如，下标 0 是一个大小为 8 字节的链表节点，下标 1 是一个大小为 16 字节的链表节点。每个下标的内存是不同的，使用最近按需分配的内存。

再比如，一个结构的内存其实是 31 字节，分配的时候会分配 32 字节。

带下标的链表每个节点存储的内存是一致的。

所以建议将小对象合并到一个结构中。

```
**for** k, v := **range** m {
    // copy for capturing by the goroutine
    x := **struct** {k , v **string**} {k, v} 
    **go** **func**() {
        // using x.k & x.v
    }()
}
```

***#2。合理使用*** `***buff***` ***缓存。***

当协议编码需要频繁操作 buff 时，可以使用`bytes.Buffer`作为缓冲对象，它会一次性分配足够的内存，避免内存不够时动态申请内存，减少内存分配的次数，buff 可以重复使用(推荐复用)

例如，在创建切片和地图时，预先估计大小并指定容量。

提前分配内存，可以减少动态扩展带来的开销。

```
t := make([]**int**, 0, 100)
m := make(**map**[**string**]**int**, 100)
```

如果你不确定`slice`是否会初始化，使用`var`不会分配内存，`make([]int,0)`会分配内存空间。

```
**var** t []**int**
```

提示:在容量小于或等于 1024 之前，切片容量加倍。容量大于 1024 后，每次增加为`1/4`。

地图的扩展机制更复杂。每次展开都是 2 的倍数。结构中有一个桶`oldBuckets`实现增量扩展。

***#3。长调用栈避免了申请更多的临时对象。***

`goroutine`的默认堆栈大小是 4KB。

在 Golang1.7 中，改为 2KB，采用连续堆栈机制。当堆栈空间不够时，goroutine 会继续扩展，每次扩展都和切片的扩展一样。

它涉及新堆栈空间的应用和旧堆栈空间的复制。如果`GC`发现当前空间只有之前的 1/4，就会再次收缩，频繁的内存申请和复制会带来额外的开销。

建议:控制函数调用栈框架的复杂度，避免创建过多的临时对象，如果确实需要长的调用栈或者作业类型代码，可以考虑池化 goroutines

***#4。避免频繁创建临时变量。***

GC STW 的时间已经优化到最差的 1 毫秒，但仍有混合写入障碍会降低性能。如果临时变量太多，GC 性能损失会很大。

建议:缩小变量的范围，使用局部变量，最小化可见性，并将多个变量合并到一个结构数组中(减少扫描次数)

***#5。大型结构通过指针传递。***

Golang 是全值复制，特别是当`struct`被压入栈帧时，它会一个一个的压入变量，频繁的申请内存，可以使用指针转移来优化性能。

**并发优化。**

***#1。使用*** `***goroutine***` ***汇集。***

Go 是轻量级的，但是对于高度并发的轻量级任务，比如高度并发的作业类型代码。

考虑使用 goroutine 池来减少 go routine 的创建和销毁。

***#2。减少系统调用。***

`goroutine`的实现是通过同步来模拟异步操作。例如，下面的操作不会阻塞`runtime`的线程调度。

*   网络 IO。
*   渠道。
*   `time.Sleep`。
*   基于底层异步`SysCall`。

下面的阻塞创建了一个新的线程调度。

*   本地 IO。
*   `SysCall`基于低级同步。
*   CGO 调用 IO 或其他阻塞。

建议同步调用:隔离到可控的 goroutine 中，而不是直接的高级 go routine 调用。

***#3。合理降低锁的粒度。***

Go 建议使用通道调用，而不是共享内存。存在通道之间的锁定范围太大的问题，这会降低锁定的强度。

另外，不要在`channel`中传递大数据，会有值复制的问题。

`channel`的底层是链表+锁。

不要用`channel`传递图片等数据，任何队列的性能都很低，可以尝试用指针优化大型对象

***#4。合理使用*** `***protobuf***` ***。***

`protobuf`在存储和解析上比`json`更高效。建议使用`protobuf`代替`json`进行持久化或数据传输。

***#5。基于业务场景聚合数据。***

对于网关接口，通常需要聚合多个模块的数据。当这些业务模块的数据之间没有依赖关系时，可以进行并行请求以减少耗时。

```
ctxTimeout, cf := context.WithTimeout(context.Background(), time.Second)
**defer** cf()
g, ctx := errgroup.WithContext(ctxTimeout)
**var** urls = []**string**{
    "http://www.golang.org/",
    "http://www.google.com/",
    "http://www.foo.com/",
}
**for** _, url := **range** urls {
    // Launch a goroutine to fetch the URL.
    url := url // https://golang.org/doc/faq#closures_and_goroutines
    g.Go(**func**() **error** {
        // Fetch the URL.
        resp, err := http.Get(url)
        **if** err == **nil** {
            resp.Body.Close()
        }
        **return** err
    })
}
// Wait for all HTTP fetches to complete.
**if** err := g.Wait(); err == **nil** {
    fmt.Println("Successfully fetched all URLs.")
}
**select** {
**case** <-ctx.Done():
    fmt.Println("Context canceled")
**default**:
    fmt.Println("Context not canceled")
}
```

**容易犯的错误。**

***#1。关于*** `***channels***` ***的常见故障。***

*   关闭已关闭的通道将`panic`。
*   向封闭通道发送数据将`panic`。
*   从封闭通道读取数据是初始值默认值。

渠道封闭原则。

*   不要关闭接收器上的`channel`。
*   不要关闭有多个发送器的`channels`。
*   当只有一个发送方，以后没有数据发送时，可以关闭`channel`。

另外，请注意，缓存的频道不一定是有序的。

如何优雅地关闭频道？

【https://go101.org/article/channel-closing.html 

***#2。关于*** `***defer***` ***的常见故障。***

应额外注意`defer`中的变量。

*   调用时传递参数。

```
i := 1
**defer** println("defer", i)
i++
// defer 1
```

*   非参数闭包。

```
i := 1
**defer** **func**() {
    println("defer", i)
}()
i++
// defer 2
```

*   命名返回是相同的闭包，将修改命名返回的返回值。

```
**func** **main**(){
   fmt.Printf("main: %v\n", getNum())
   // defer 2
   // main: 2
}

**func** **getNum**() (i **int**) {
   **defer** **func**() {
      i++
      println("defer", i)
   }()
   i++
   **return**
}
```

特别要注意不要在`for loop`中调用`defer`。

因为延迟只会在函数返回后执行，这将累积大量的延迟，并且非常容易出错。

建议:将需要 defer 的 for 循环的代码逻辑封装到一个函数中。

***#3。关于*** `***http***` ***的常见故障。***

Golang 的 HTTP 默认请求没有超时，这是个大问题。

因为如果服务器不响应，不断开连接，客户端就会一直等待，导致客户端阻塞，请求量巨大时服务就会崩溃。

另外，HTTP 请求框架的响应必须用`Close`方法关闭，否则可能会出现内存泄漏。

***#4。关于*** `***interface***` ***的常见故障。***

什么时候一个`interface`等于`nil`？

注意:`interface{}`和`struct`接口类型不同。接口的底层有两个成员，一个是`type`，一个是`value`。

只有当`type`和`value`都是`nil`时，`interface{}`才等于`nil`。

```
**var** u **interface**{} = (***interface**{})(**nil**)
**if** u == **nil** {
    t.Log("u is nil")
} **else** {
    t.Log("u is not nil")
}
// u is not nil
```

接口示例。

```
**var** u Car = (Car)(**nil**)
**if** u == **nil** {
    t.Log("u is nil")
} **else** {
    t.Log("u is not nil")
}
// u is nil
```

自定义结构。

```
**var** u *user = (*user)(**nil**)
**if** u == **nil** {
    t.Log("u is nil")
} **else** {
    t.Log("u is not nil")
}
// u is nil
```

***#5。关于*** `***map***` ***的常见故障。***

`Map`并发读写将`panic`，需要锁定或使用`sync.Map`。

`map`也不能直接更新`value`的字段。

```
**type** User **struct**{
   name **string**
}
**func** **TestMap**(t *testing.T) {
   m := make(**map**[**string**]User)
   m["1"] = User{name:"1"}
   m["1"].name = "2"
   // Compilation failed, you cannot directly modify a field value of map
}
```

需要单独拿出来。

```
**func** **TestMap**(t *testing.T) {
   m := make(**map**[**string**]User)
   m["1"] = User{name: "1"}
   u1 := m["1"]
   u1.name = "2"
}
```

***#6。关于*** `***slice***` ***的常见故障。***

数组是值类型，切片是引用类型(指针)。

```
**func** **TestArray**(t *testing.T) {
   a := [1]**int**{}
   setArray(a)
   println(a[0])
   // 0
}**func** **setArray**(a [1]**int**) {
   a[0] = 1
}**func** **TestSlice**(t *testing.T) {
   a := []**int**{
      1,
   }
   setSlice(a)
   println(a[0])
   // 1
}**func** **setSlice**(a []**int**) {
   a[0] = 1
}
```

`range`方法将创建每个元素的副本，并且将有一个值的副本。如果数组存储大型结构，可以使用索引遍历或指针优化。

因为`value`是副本，所以不能修改原值。

`append`方法改变地址。

`slice`型的本质是一种结构。

```
**type** slice **struct** {
   array unsafe.Pointer
   len   **int**
   cap   **int**
}
```

复制函数值将使修改无效。

```
**func** **TestAppend1**(t *testing.T) {
   **var** a []**int**
   add(a)
   println(len(a))
   // 0
}

**func** **add**(a []**int**) {
   a = append(a, 1)
}
```

***#7。关于*** `***closure***` ***的常见故障。***

```
**for** i := 0; i < 3; i++ {
    **go** **func**() {
        println(i)
    }()
}
time.Sleep(time.Second)
// 2
// 2
// 2
```

因为闭包导致`i`变量转义到堆空间，所以所有 goroutines 共享`i`变量，从而导致并发问题。

解决方案 1:局部变量。

```
**for** i := 0; i < 3; i++ {
    ii := i
    **go** **func**() {
        println(ii)
    }()
}
time.Sleep(time.Second)
// 2
// 0
// 1
```

解决方案 2:参数传递。

```
**for** i := 0; i < 3; i++ {
    **go** **func**(ii **int**) {
        println(ii)
    }(i)
}
time.Sleep(time.Second)
// 2
// 0
// 1
```

***#8。关于*** `***select***` ***的常见故障。***

`for`中的`default`会在`select`中执行，不会一直占用 CPU，导致 CPU 空闲。

示例代码。

```
**func** **TestForSelect**(t *testing.T) {
   **for** {
      **select** {
        **case** <-time.After(time.Second * 1):
            println("hello")
        **default**:
            **if** math.Pow10(100) == math.Pow(10, 100) {
                println("equal")
            }
        }
    }
}
```

执行`top`命令。

```
top - 15:00:50 up 1 day, 15:55,  0 users,  load average: 1.36, 0.85, 0.35
  PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND   
28632 root      20   0 2168296   1.4g   2244 S 252.8  11.7   1:04.15 __debug_bin
```

感谢阅读。

如果你喜欢这样的故事，想支持我，请给我鼓掌。

你的支持对我来说非常重要，谢谢你。