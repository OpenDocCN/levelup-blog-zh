# Go With Go(Lang):特性和测试框架

> 原文：<https://levelup.gitconnected.com/go-with-go-lang-features-and-testing-frameworks-ab39c51d0c66>

![](img/67040fef59a42725379c9f82ac19f75d.png)

在 [Unsplash](https://unsplash.com/search/photos/software-development?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Helloquence](https://unsplash.com/photos/5fNmWej4tAA?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

使用 GoLang 进行自动化测试

在本文的第一部分中， [Go With Go(郎):为什么要走？](https://medium.com/@iryna.suprun/go-with-golang-e863cf874e0e)我们讨论了如何为测试自动化选择编程语言，以及为什么 Go 是一个强有力的竞争者。在这一部分中，我们将看一看使自动化变得更加容易的几个更令人兴奋的特性，并简要概述最流行的 Go 测试框架。

我最喜欢的 GoLang 功能是 **goroutines。自动化测试通常需要等待一些事情发生。等待时间可能不同，可能是几毫秒或几秒，甚至几分钟。当被测产品向内部或外部的消息服务(Kafka，PubSub)发布消息时，这种情况很常见。当被测系统软件使用微服务架构构建时，就会出现这种情况。当测试查询巨大的数据集(现在大数据无处不在)或下载大文件时，也会发生这种情况。它发生在我们看到异步或事件驱动行为的任何地方。**

处理这种情况最常见的错误是使用睡眠。这在测试自动化中是一个大问题。这是反模式的。睡眠使自动化测试变得缓慢或古怪，或者两者兼而有之。如果你的测试中有它们，就尽快把它们去掉。

自动化测试应该等待事件发生或超时，而不是休眠，在其他情况下，它们应该能够处理异步。Goroutines 使实现异步逻辑变得容易。那么这是什么——戈鲁丁？官方定义称 *goroutine* 是一个轻量级的执行线程。对于本文的范围来说，这就是我们需要知道的全部内容。

有几个特性使 goroutine 非常适合自动化:

*   你甚至可以在普通的笔记本电脑上旋转许多 goroutine，甚至不会注意到它(每个 go routine 只使用 2KB 的内存)。此外，他们只使用他们需要的内存。
*   使用通道在 goroutines 之间实现和通信很容易。
*   goroutines 易于使用，不需要高级编码技能

最后一点非常重要，在测试中使用 goroutines 和实现多线程，你不需要成为一个围棋大师。您需要知道的所有内容都包含在基础的 [go 教程](https://tour.golang.org/welcome/1)中(如果您编写了一个需要在开始之前阅读和学习更多关于并发性的产品代码，请不要误解我的意思)。

我以前使用 Java 和 Python 来自动化复杂的测试用例，用这些语言实现异步的、事件驱动的逻辑(读多线程)需要高级的编码技能。如果你使用 Go，那就简单多了。

在下面的例子中，test 验证了通过点击 API 端点创建的消息被发送到 Kafka 队列。如果在队列中找到我们发送的消息，测试应该通过，如果没有或超时，测试应该失败。

```
**func** TestPostToKafkaQueue(t *testing.T) { **var** waitGroup sync.WaitGroup
    //create and post message to Kafka queue
    resp, _ := buildAndSendMessage(url, id, ***true***)
    //fail test if creating message resulted in error
    assert.Equal(t, resp.Status, **“200 OK”**)
    //setting up a timeout. Test will stop when timeout is reached
    ctx, cancel := context.WithTimeout(context.Background(),   timeout)
 **defer** cancel()
    waitGroup.Add(1)//this is our goroutine. In the code below we start separate      thread that will be constantly consuming messages from queue and checking that it is message that we expected to see **go func**(){
       result, err := consume(ctx, topic, true, topic, id)
 **if** err != nil {
          fmt.Println(**“ERROR: Consuming error”**, err)
          assert.FailNow(t, "Failed to consume message from queue***"***)
       }
       fmt.Println(**“Found event in kafka topic:”**, result)
       waitGroup.Done()
       ctx.Done()
       assert.Equal(t, result, *expected_result*)
       }()
    waitGroup.Wait()
}
```

还有另外两个特性使得 Go 非常适合测试自动化:

*   **没有例外。自己处理错误的能力对于编写自动化测试和框架来说是一个很大的优势。它给了我们工具来查明到底哪里出错了，并使失败测试用例的调试变得轻而易举。例如，我们自动化了完全模拟生产系统中数据流端到端测试。测试运行了几个小时，数据由不同的系统组件处理。当它失败时，我们有非常具体的错误消息，并能够在几秒钟内找到失败的根本原因。**
*   **多个返回值。**您可以返回错误和部分响应、测试结果以及解释错误的消息。一切。这个特性最常用的情况是返回一些对象和错误。

下面是一个在测试中使用多个响应的例子，该测试命中*/执行* API 端点并验证它返回 200 OK。如果请求失败，它会点击*google.com*并返回来自它的响应和来自请求的错误到*/执行*(请不要问我为什么有人会有这样奇怪的测试，也许我们想检查我们仍然能够连接到谷歌，这已经成为互联网连接的同义词)

```
**func httpRequestMultipleResponseValues**(url string) (*http.Response, error) {
  client := &http.Client{}
  r, err := http.NewRequest(http.MethodGet, url)
  r.Close = ***true* if** err != nil {
    **return** nil, err
  }response, err := client.Do(r)
  **if** err != nil {
    //if error is returned when we hit 'url' we will hit google  and return response from google and 'url' error
     r, _ := http.NewRequest(http.MethodGet, "**http://google.com/**")
     responseAlt, _ := client.Do(r)
     **return** responseAlt, err
  }//in case of positive scenario we return /execute response and nil as an error **return** response, nil
}**func TestMultipleReturns**(t *testing.T) {
  url := fmt.Sprint("**http://localhost:8080/execute"**)
  resp, err := httpRequestMultipleResponseValues(url)
  **if** err != nil {
     if resp.Status != http.StatusOK {
     fmt.Println("Additional requests failed")
     }
     assert.FailNow(t, "Test Failed!***"***)
  }
  assert.Equal(t, resp.Status, "**200 OK"**)
}
```

现在来说说**工装**。首先，Go 不需要 web 框架。HTTP，JSON，HTML 模板支持是在语言中内置的，这是一个巨大的优势。

有多种测试框架可以帮助组织测试，提供额外的资产，报告功能和许多其他有用的特性。其中一个是[**go convey**](https://github.com/smartystreets/goconvey)**。这是地鼠的 BDD 框架。它允许将测试描述和测试代码保存在一个地方。GoConvey 与 *go test* 配合使用，可在终端或浏览器中使用，提供出色的报告。它需要不到一分钟，包括下载时间开始使用它。可惜 GoConvey 的开发已经在 2017 年 8 月停止。**

```
**func** TestExecute_POST(t *testing.T) {Convey("**Given call to /execute EP of test API"**, t, **func**() {
   resp, err := httpRequest("**http://localhost:8080/execute",** url)
   Convey("**When successful response returned"**, **func**() {
      **if** err != nil {
        fmt.Println(**“Requests failed”**, err)
        So(***true***, ShouldEqual, ***false***)
      }
      fmt.Println(**“Response:”**, resp.Status)
      Convey("**The response HTTP status code should be 200"**, **func**() {
        So(resp.Status, ShouldContainSubstring, "200**"**)
    })
   })
})
}
```

另一种，比较流行的 BDD 式围棋测试框架是[**银杏**](http://github.com/onsi/ginkgo) **。**它最好与 [Gomega](http://github.com/onsi/gomega) matcher 库配对，但被设计成与匹配器无关。银杏比 GoConvey 有更复杂的设置，但是它有更好的测试组织，更多的特性，并且更成熟。银杏是建立在`testing`包之上的，所以它与其他测试兼容，即使他们不使用银杏。

最新版本—2018 年 8 月。如果我现在选择自动化框架，我会选择银杏。

银杏声称它支持测试异步性，但我不会说这是银杏的特性，它只是包装良好的常规 GoLang 例程。

```
It("**should post to the channel, eventually"**, **func**(done Done) {
 c := make(**chan** string, 0)**go** DoSomething(c)
 Expect(<-c).To(ContainSubstring(**“Done!”**))
 close(done)
}, 0.2)
```

这里有一个银杏测试的例子(我从银杏文档中获得)

```
**func** TestApi(t *testing.T) {
   RegisterFailHandler(Fail)
   RunSpecs(t, "**Api Suite"**)
}**var** _ = Describe("**Api**", **func**() {
    Describe("**Test Convey APIs — execute ep**", **func**() {
    resp, _ := httpRequest(**“http://localhost:8080/execute"**)
     Context("**Hit execute EP"**, **func**() {
       It("**Should get response 200 OK**", **func**() {
          Expect(resp.Status).To(Equal(**“200 OK”**))
       })
     }) 
    })
})
```

我认为 Go 最适合后端测试，但是有一个用于 Go 的通用 WebDriver 客户端— [Agouti](http://github.com/sclevine/agouti) 。我个人从未使用过它，但我相信它有未来。Agouti 提供 [Gomega](https://github.com/onsi/gomega) 匹配器，与[银杏](https://github.com/onsi/ginkgo)和 [Spec](https://github.com/sclevine/spec) 一起工作(另一个 BBD 测试组织者)。上一次发布是在 2018 年 3 月，所以它的开发非常活跃。

有多种用于负载测试的 GoLang 测试工具。其中最成熟的是 [**贝吉塔**](https://github.com/tsenart/vegeta) **，** HTTP 负载测试工具。它既可以用作命令行实用工具，也可以用作库，并且和本文中提到的所有其他工具一样是开源的。上一次发布是在 2018 年 10 月。

注意:我提到的所有发布日期都是截至 2018 年 12 月的实际日期。

在你走之前，我想强调几点。

*   测试库足以创建单元、集成和 e2e 测试
*   无论你选择什么样的框架，go test 总是运行你的测试
*   有许多测试框架，它们都是开源的
*   异步的、事件驱动的测试非常容易构建
*   一个框架—所有测试(api、负载、后端端到端)

最后一点——本文的重点不是说服每个人使用 GoLang 实现自动化，而是让测试社区对新事物更加开放。有许多工具和语言可能比大多数流行的工具和语言更适合您的自动化需求。明智地选择你的语言和工具，祝你自动化愉快！

我用来准备本文的资源:

*   [Awesome Go](https://github.com/avelino/awesome-go#testing)—Awesome Go 框架、库和软件列表。
*   [围棋基本测试模式](https://medium.com/agrea-technogies/basic-testing-patterns-in-go-d8501e360197)作者[塞巴斯蒂安·达尔格伦](https://medium.com/@sebdah)
*   [围棋之美](https://hackernoon.com/the-beauty-of-go-98057e3f0a7d)作者[卡尼什克·杜德佳](https://hackernoon.com/@kanishkdudeja)
*   [为什么围棋人气暴涨](https://opensource.com/article/17/11/why-go-grows)作者[杰夫·劳斯](https://opensource.com/users/jeffr)
*   [为什么要学围棋？](https://medium.com/exploring-code/why-should-you-learn-go-f607681fad65)作者[凯夫尔·帕特尔](https://medium.com/@kevalpatel2106)
*   [我们从 Python 转向 Go 的 5 个原因](https://hackernoon.com/5-reasons-why-we-switched-from-python-to-go-4414d5f42690)作者 [Tigran Bayburtsyan](https://hackernoon.com/@tigranbs)
*   [GopherCon 2018——从原型到生产:用 Go](https://about.sourcegraph.com/go/gophercon-2018-from-prototype-to-production-lessons-from-building-and/)[Deval Shah](https://twitter.com/devalshah)构建和扩展 Reddit 广告服务平台的经验教训
*   Mat Ryer 用#golang 编写单元测试的 5 个简单提示和技巧

[![](img/439094b9a664ef0239afbc4565c6ca49.png)](https://levelup.gitconnected.com/)[](https://gitconnected.com/learn/golang) [## 学习围棋-最佳围棋教程(2018) | gitconnected

### 23 大围棋教程。课程由开发者提交并投票，使您能够找到最好的围棋课程…

gitconnected.com](https://gitconnected.com/learn/golang)