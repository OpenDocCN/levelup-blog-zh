# 在 Go 中同时调用多个 API(Go routine 和 WaitGroup)

> 原文：<https://levelup.gitconnected.com/calling-multiple-apis-concurrently-in-go-goroutine-and-waitgroup-5535155902c3>

![](img/2e916bf8c90653c1372f1ea9df09a118.png)

如今有这么多的 REST APIs，您会发现自己处于这样一种情况，需要调用多个端点来构建业务对象的适当表示。这不是一个困难的任务，只需创建一个新对象，调用各种 API 获取数据，填充适当的字段，就可以了。如果您的所有调用都很快，这很好，但是如果您的一个或所有 API 调用花费大量时间，这就开始变得很麻烦了。

在本帖中，我们将看看如何使用 Goroutines 和 WaitGroups 在 Go 中同时进行这些调用。我们将编写一些模拟 API 调用的代码，并在每个代码中引入一些延迟。我们将同步调用每个端点并计时。然后，我们将重构代码以同时调用它们并比较结果。我们走吧！

# 设置

在这段代码中，我们将模拟调用多个(三个)不同的 API 来构建自定义结构的所有数据。在每个“API”调用中，我们将引入一个“睡眠”来模拟调用中的延迟。让我们看看代码。

```
package mainimport (
 "encoding/json"
 "fmt"
 "log"
 "time"
)type City struct {
 Name       string
 Population int
 Households int
 Temp       int
 Forecast   string
 Elevation  int
 Lat        float64
 Long       float64
}// Main function
func main() {
 defer timeTrack(time.Now(), "Fetching city info") fmt.Println("Starting synchronous calls...") city := City{Name: "Richmond"} getDemoInfo(&city)
 getCurrentWeather(&city)
 getMapInfo(&city) data, _ := json.Marshal(city)
 fmt.Printf("%s\n", data)
}func getDemoInfo(c *City) {
 // Calling Sleep method (lag)
 time.Sleep(2100 * time.Millisecond) c.Population = 230436
 c.Households = 90301
}func getCurrentWeather(c *City) {
 // Calling Sleep method (lag)
 time.Sleep(1200 * time.Millisecond)

 c.Temp = 32
 c.Forecast = "Snow, turning to rain"
}func getMapInfo(c *City) {
 // Calling Sleep method (lag)
 time.Sleep(3300 * time.Millisecond) c.Elevation = 457
 c.Lat = 37.533333333
 c.Long = -77.466666666
}func timeTrack(start time.Time, name string) {
 elapsed := time.Since(start)
 log.Printf("%s took %s", name, elapsed)
}
```

让我们分解这段代码的每一部分:

*   首先，我们有一个包含三个数据部分的`City`结构:人口统计、天气和地图。
*   在我们的`main`函数中，我们立即推迟对自定义`timeTrack`函数的调用。这只是一个助手，将时间和打印功能需要多长时间运行。
*   接下来，我们新建一个`City`对象，并将其命名为 Richmond (RVA 宝贝！)，然后进行三次调用来获取我们需要的所有数据，通过引用传递`city`对象。
*   在`main`函数的最后，我们简单地打印了一个我们城市的 JSON 表示。
*   在我们的每个“API”调用中，我们简单地调用一个带有随机值的`time.Sleep`。同样，这模拟了您可能在真实 API 调用中发现的延迟。

因为这些是同步调用，所以您实际上可以遍历每个“API”调用，并将我们引入的“休眠”相加，以确定这段代码应该运行多长时间。把这些加起来，我们得到 6.6 秒。运行这段代码，我们得到以下输出:

```
Starting synchronous calls...
{"Name":"Richmond","Population":230436,"Households":90301,"Temp":32,"Forecast":"Snow, turning to rain","Elevation":457,"Lat":37.533333333,"Long":-77.466666666}
2021/01/02 14:03:37 Fetching city info took 6.606641483s
```

好的，这看起来很棒！我们创建了一个`City`对象，称为一组“API ”,填充我们的对象，并打印结果。这看起来不错，但是 6.6 秒对你的用户来说可能太长了(对我的用户来说是这样)。让我们看看如何轻松地修改这段代码，以便同时进行所有这些调用。

# 所有人，立刻

为了实现这一点，我们将介绍两件事情:

*   Go routine:Go routine 是由 Go 运行时管理的轻量级线程。这些是 OS 线程之上的包装器，由 Go 运行时而不是操作系统管理。基本上把`go`放在你的函数调用前面，它处理剩下的。这里有一篇很有深度的文章[。](https://medium.com/technofunnel/understanding-golang-and-goroutines-72ac3c9a014d)
*   WaitGroups:一个 [WaitGroup](https://golang.org/pkg/sync/#WaitGroup) 等待 goroutines 的收集完成。这里有一篇[很棒的写起来要深挖一点](https://medium.com/swlh/using-goroutines-and-wait-groups-for-concurrency-in-golang-78ca7a069d28)。

幸运的是，对于我们的代码来说，没有什么需要修改的地方。让我们来看看修改后的代码:

```
func main() {
 defer timeTrack(time.Now(), "Fetching city info") fmt.Println("Starting concurrent calls...") var waitgroup sync.WaitGroup
 waitgroup.Add(3) city := City{Name: "Richmond"} go func() {
  getDemoInfo(&city)
  waitgroup.Done()
 }() go func() {
  getCurrentWeather(&city)
  waitgroup.Done()
 }() go func() {
  getMapInfo(&city)
  waitgroup.Done()
 }() waitgroup.Wait() data, _ := json.Marshal(city)
 fmt.Printf("%s\n", data)
}
```

现在，让我们打开包装:

*   这个片段中没有显示，我们必须导入`WaitGroup`所在的`sync`包。
*   一旦我们有了导入，我们就可以为我们的 WaitGroup `var waitgroup sync.WaitGroup`创建一个新变量
*   接下来，我们必须告诉我们的 WaitGroup 我们将使用`waitgroup.Add(3)`开始多少 goroutines。
*   然后，我们创建三个匿名函数来包装我们的“API”调用。在这里使用匿名函数可以让我们不去动原来的“API”调用，这很好。当使用 WaitGroup 时，您必须调用`waitgroup.Done()`，这将减少我们之前给 WaitGroup 的计数。这就是它如何知道所有的 goroutines 什么时候完成。如果我们在这里没有使用匿名函数，我们需要更新我们的“API”调用函数，通过引用传递我们的 WaitGroup 对象，然后调用其中的`Done()`方法。在我看来，这更干净，因为它不会混淆实现。

```
go func() {
  getDemoInfo(&city)
  waitgroup.Done()
 }()
```

*   最后，我们需要等待所有调用结束，因此我们有一个`waitgroup.Wait()`调用。这告诉我们的 WaitGroup 阻塞，直到所有的 goroutines 都完成。它知道这一点是因为我们告诉它我们有 3 个要运行，并且每个调用都用一个`waitgroup.Done()`来减少这个计数。一旦计数达到 0，该块就被释放，函数可以继续运行。

既然所有的调用都在同时运行，我们应该看到运行时间等于运行时间最长的“API”调用，即 3.3 秒。运行这段代码，我们得到以下输出:

```
Starting concurrent calls...
{"Name":"Richmond","Population":230436,"Households":90301,"Temp":32,"Forecast":"Snow, turning to rain","Elevation":457,"Lat":37.533333333,"Long":-77.466666666}
2021/01/02 14:34:00 Fetching city info took 3.305725811s
```

好多了！

# 包扎

在这篇文章中，我们快速看了一下如何在 Go 中使用 WaitGroups 进行并发 API 调用。我们花了很长时间，把运行时间缩短了一半！在 Go 中还有其他的并发编程方式。有[通道](https://tour.golang.org/concurrency/2)，允许你接收从 goroutines 返回的数据，还有一起使用[通道和等待组](https://dev.to/sophiedebenedetto/synchronizing-go-routines-with-channels-and-waitgroups-3ke2)。这篇文章希望是对这些概念的快速介绍，现在你可以更深入地了解了！

## 资源

GitHub Repo:[https://GitHub . com/atkinsonbg/calling-APIs-concurrent-go-wait groups](https://github.com/atkinsonbg/calling-apis-concurrently-go-waitgroups)

https://tour.golang.org/concurrency/1

等待组:[https://golang.org/pkg/sync/#WaitGroup](https://golang.org/pkg/sync/#WaitGroup)

[![](img/3515ab52cb6fb5e74c27c7a2e06d3811.png)](https://ko-fi.com/O5O63ENS7)