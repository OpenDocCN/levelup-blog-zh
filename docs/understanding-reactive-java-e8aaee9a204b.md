# 理解反应式 Java

> 原文：<https://levelup.gitconnected.com/understanding-reactive-java-e8aaee9a204b>

## 因为你的线程阻碍了我的表现。

![](img/ecf74f6fda5d07f2d92b9cad4f5dd624.png)

图片来自[皮克斯拜](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5248183)

非阻塞 I/O 已经存在一段时间了。不同的语言实现方式不同，但都提供了一种减少线程数量的方法，同时似乎允许完全并发。JavaScript 从一开始就一直在做；如果只有一个线程，你最好不要成为将阻塞调用推向生产的编码者。

虽然反应式 Java 正在获得一些支持，但我认识的大多数 Java 程序员仍然生活在多线程的思维框架中。为什么？线程是一个相对容易理解的概念。反应式 Java 要求我们重新思考我们是如何学习编码的。试图解释为什么非阻塞 I/O 是更好的选择，就像试图向一直认为地球是平面的人解释地球是球形的一样。

我学习的方式是玩、实验、制作玩具系统，在需要的时候可以作为一个更大系统的内核。我将在这里展示一个使用[项目反应器](https://projectreactor.io/)演示反应式 Java 基础的小系统。因为它很小(九个文件不到一千行)，所以应该很容易使用。

这是一个伪微服务系统，这意味着虽然它只是一个单一的可执行文件，但每个“服务”都在一个不包含任何状态的单一类中，并且这些类只能使用消息队列和数据库相互通信。一个主类启动它们，它们都被编程为在一段时间后关闭。

该系统是在一个二手车辆进气系统上建模的，它从一个购买订单开始，在我们的例子中，这个订单是随机生成的，并被添加到一个消息队列中。另一个服务使用该队列，将购买订单添加到数据库中，然后根据车辆是轿车、卡车还是摩托车，将车辆类型发送到三个消息队列中的一个。最后，有三个服务读取三个队列，检查数据库中是否存在购买订单，并为要运送的车辆分配一个批次。

`main`函数更像是一个测试函数，因为在一个真正的微服务系统中，每个服务都有自己的主函数，或者是 Spring 之类的框架的一部分。但是我们感兴趣的是看到所有的东西在一个 JVM 中一起工作。对这个系统的真正测试是将它们都作为独立的服务来工作，但是我将假设这是可行的，或者只需要很小的改动就可以工作。下面是主要的核心功能:

```
PurchaseOrderConsumer.consume();
CarConsumer.consume();
TruckConsumer.consume();
MotorcycleConsumer.consume();
PurchaseOrderGenerator.start();
```

我们启动所有的消费者，然后我们启动购买订单生成器，它创建购买订单并在十秒钟内重复地将它们插入队列。因为没有一个类有任何状态，所以所有的方法都可以是静态的。

因为`PurchaseOrderGenerator`是所有动作开始的地方，所以让我们先来看看。下面是代码的主要部分:

```
Sender sender = RabbitFlux.createSender(soptions);
sender.sendWithPublishConfirms(
  Flux.generate((sink) -> sink.next(createRandomPurchaseOrder()))
    .cast(PurchaseOrder.class)
    .doOnNext((o) -> log("produced: " + o))
    .delayElements(Duration.ofMillis(100))
    .take(Duration.ofSeconds(10))
    .map(i -> new OutboundMessage("", 
         QUEUE_NAME, 
        writeJson(i).orElse("").getBytes()))
    .doFinally((s) -> {
       log("Generator in finally for signal " + s);
       sender.close();
     })
)
.subscribe();
```

我们使用的是 [RabbitMQ](https://www.rabbitmq.com/) 消息队列，它有一个反应式 Java 库。它有一个`Sender.sendWithPublishConfirms`，它接受一个`Flux<OutboundMessage>`，并返回一个`Flux<OutboundMessageResult>`。通量是可以一个接一个作用的一系列项目。它的长度可以是无限的，一次只能有一个物品在游戏中，尽管你可以有更多的物品，这取决于许多因素。

Flux 是项目反应堆非阻塞 i/o 库的关键组成部分。它允许您将方法链接在一起，以便在项目流过系统时对其进行操作，还可以操作流本身。操作这些项目的方法通常将 lambda 函数作为参数，以便在项目流过系统时可以对每个项目调用它们。你将看到的最常见的两个是`map`和`filter`。`map`方法旨在将流中的项目从一种类型转换为另一种类型。`filter`旨在删除不符合特定标准的项目。

我们希望创建一个不间断的采购订单列表，并将其作为`Flux<OutboundMessage>`呈现给`Sender.sendWithPublishConfirms`方法。我们从`Flux.generate((sink) -> sink.next(createRandomPurchaseOrder()))`行开始这个过程，它创建了一个潜在的无限的随机购买订单列表。因为`Flux.generate`返回一个`Flux<Object>`，我们需要`cast`方法让系统知道我们正在处理的是`PurchaseOrder`对象。`Flux`的`doOnNext`方法是引入安全副作用的简便方法。我在这里使用它主要是为了记录。

接下来，`delayElements`方法在每个项目之间添加一个延迟。我这样做是为了模拟随机采购订单以合理的速度引入。您可以安全地删除它，但是它不是让系统运行 10 秒钟就关闭，而是用购买订单淹没消息队列，并且需要两分钟以上来处理所有订单。`take`方法将允许通量处理十秒钟，然后停止。我这样做是因为这个生成器仅用于测试目的。在真实的系统中，采购订单可能来自一个 API，该 API 会将它们添加到消息队列中。

为了让 RabbitMQ 处理这些项目，首先，需要将它们转换成一个`OutboundMessage`，作为表示订单的 JSON 序列化的字节数组。我们使用 map 方法和一个 lambda 表达式来创建所需的 OutboundMessage。如果在序列化购买订单时有任何错误，我们将发送一个空数组，该数组将在另一端被过滤掉。我们还可以在这里过滤它，以避免消息队列传递无效消息。但是无论如何，我们需要在另一边验证，因为这只是一个测试仪器，我们也可以把它们一起送过去。

`doFinally`方法只调用它的 lambda 表达式一次，在这种情况下，我们用它来记录流的结束，并关闭发送方。在这个链的末尾，我们有一个适合传递给`Sender.sendWithPublishConfirms`方法的`Flux<OutboundMessage>`。我们得到的是一个`Flux<OutboundMessageResult>`,我们可以检查它以确保消息被正确地放入队列。在这个简单的例子中，我们不检查。

在`Flux<OutboundMessageResult>`上调用的`subscribe`方法启动滚球。值得注意的是，在调用`subscribe`方法之前，我们所做的只是构建活动链。一旦调用了 subscribe 方法，我们就不能再对数据流做任何事情，它只返回一个`Disposable`对象，可以用来停止订阅。我们甚至没有保留`Disposable`对象，我们的流将在十秒钟后停止，正如我们在事件链中编码的那样。我们用`generate`方法创建的初始流并不以对`subscribe`的调用结束，而是期望我们传递给它的`Sender`会订阅它以启动事件链。

在我们继续讨论系统中真正的服务之前，让我们看一看幕后是什么让这一切发生的。有一种魔力在大多数情况下是看不到的，那就是`Scheduler`。看看`generate`创造的`Flux`。链中的前两个方法`cast`和`doOnNex`具有不会阻塞的 lambda 函数，并且会在很短的时间后返回。但是这个链中的下一个方法`delayElements`，必须以某种方式阻止在流程中引入延迟。在一个非阻塞的世界里，怎样才能有阻塞功能？答案是调度器。调度程序可以查看该链以及流经该链的项目，并决定在该链的哪个点上哪个项目适合处理。

你的头爆炸了吗？这里有一种看待它的方式。在一个多处理操作系统中，有些东西会查看等待的进程并决定下一个谁获得 CPU 时间。调度器的工作方式非常类似，只是它不用担心会中断任何事情。调度程序必须处理的所有任务都是非常小的代码块。延迟元素等“阻塞”任务分为两部分。第一部分从流中取出一个项目，设置一个计时器，并将控制权返回给调度程序。现在，调度程序可以自由运行其他任务了。一旦 delayElements 方法的计时器到了，就会调用第二部分，保存的项就有资格移动到链中的下一个方法。

我把它想象成两条首尾相连的传送带。第一个向第二个移动物品，但是第二个移动得慢得多。因此，当第二条传送带慢慢将物品拉走时，物品会回到第一条传送带上。当物品堆积时，这被称为背压，系统知道基本上关闭第一条传送带，直到第二条传送带能赶上一点。

让我们来看看系统中的第二个服务，PurchaseOrderConsumer。它的工作是提取采购订单队列中的项目，将它们记录到数据库中，处理它们，然后将细化的项目添加到另一个队列中。核心代码如下:

```
Sender sender = RabbitFlux.createSender(soptions);
Receiver receiver = RabbitFlux.createReceiver(roptions);
sender.sendWithPublishConfirms(receiver
  .consumeAutoAck(PO_QUEUE_NAME)
  .map(d -> readJson(new String(d.getBody())))
  .filter(PurchaseOrder::isValid)
  .doOnNext(po -> log("recevied po " + po))
  .doOnDiscard(PurchaseOrder.class, 
               po -> log("Discarded invalid PO " + po))
  .flatMap(po -> writePoJson(po).map(j -> reactiveCollection
      .upsert(po.getId(), j)
      .map(result -> po))
    .orElse(Mono.just(po)))
  .map(po -> factory.build(po.getType(), po))
  .timeout(Duration.ofSeconds(10))
  .doFinally((s) -> {
    log("Consumer in finally for signal " + s);
    receiver.close();
    sender.close();
    cluster.disconnect();
  })
  .map(v -> new OutboundMessage("", 
                outQueue.get(v.getPo().getType()), 
                vehicleWriter
                  .get(v.getPo().getType())
                  .apply(v)
                  .orElse("")
                  .getBytes()))
)
.subscribe();
```

这看起来很像第一个服务，有一个外部包装器将细化的对象发送到不同的消息队列，并调用`subscribe`。不同之处在于，我们不是用`generate`方法得到一个`Flux`来发送，而是从`Receiver.consumeAutoAck`方法得到一个`Flux`。这将创建一个来自采购订单消息队列的项目流，然后我们可以在其上构建一个处理链。这个链主要由常用的方法组成，`map`将 JSON 字符串转换成一个条目，`filter`删除无效的条目，`doOnNext`和`doOnDiscard`记录正在处理的采购订单或由于过滤器而被丢弃的订单。

然后我们得到了一个新的方法`flatMap`。这是一个值得注意的特殊方法。从 Java Streams 了解`flatMap`的人会知道，如果一个转换返回一个流(或者一个可以很容易转换成流的列表)，`flatMap`会改变外层流，使之包含内层流中的所有项。换句话说，它将把一个流转换成一个包含所有项目的流。在通量世界中，它会做同样的事情，把一个通量转换成一个包含所有项目的通量。

在我们看到的例子中，转换是对`ReactiveCollection.upsert`方法的，该方法将采购订单向上插入到 [Couchbase](https://www.couchbase.com/) 中。返回类型为`Mono<MutationResult>`。我们还没有谈到`Mono`，它是一种特殊类型的`Flux`。一个`Mono`很像一个`Flux`，但是它只能包含一个项目，或者为空。同样和`Flux`一样，里面的物品不会随着`Mono`出现，而是需要等待。同样，我们需要暂停处理包含的项目，但不要阻塞。

这就是`flatMap`来救援的地方。在这种情况下，因为内部构造是一个`Mono`，所以在外部`Flux`中仍然存在一一对应。事实上，我们正在丢弃结果，并简单地返回原始的采购订单以供进一步处理(由`map(result -> po)`映射执行)。因此，flatMap 的工作方式类似于我们之前讨论的 delayElements 方法，它被分成两个部分，所有的数据库 i/o 都在它们之间进行。但是调度器知道把一个项目交给`flatMap`去处理另一个项目。并且它知道在准备好进一步处理时获取数据库返回项。再次，`flatMap`将我们想象的传送带一分为二。

还有最后一个链式方法要讲，其余都和前面的用法一样。如果这是一个真正的服务，那么`timeout`方法甚至不应该出现在流程中。只有在某个时间段内没有收到某个项目时，才会取消流程。这允许我们的小玩具系统关闭一切，并最终终止。你可能不会在真正的服役中拥有它。唯一的例外是，如果您的服务期望每秒一定数量的项目，而您在许多分钟内都没有收到项目。您可能怀疑到队列的连接悄悄地丢失了，并希望像 Kubernetes 这样的编排服务重新启动您的服务。

另一个可能使用超时的地方是测试代码。如果您的测试需要消息队列中的一项，您可以适当地设置超时，这样您的测试就不会无限期地等待。

我在这个系统中包含了最后一组服务，一组读取消息队列的服务，`PurchaseOrderConsumer`将`Vehicle`类型输出到这些队列。它所做的唯一处理是从`Vehicle`类型中读取采购订单，确认采购订单存在于数据库中，根据`Vehicle`类型将其分配给一个批次，并记录最终对象。在一个真实的系统中，您可能希望将它添加到车辆库存数据库中，或者以其他方式跟踪它。

我创建了三个服务类，每种类型一个；汽车、卡车或摩托车。使用当前代码，我可能已经创建了一个带有一些参数的类，并启动了 consume 方法三次，每种类型一次。但是我将它们分开，因为我预计每种类型最终可能会有不同的逻辑，类型的基数很低，基本代码不到一百行。如果这些服务有很多共同的逻辑，我可能会花一些额外的时间来整合它们。但是我的实用主义有时会战胜我的完美主义，我喜欢这样。

为了测试它，我必须启动 Couchbase 和 RabbitMQ。在这两种情况下，我只是创建了一个 Docker 实例:

```
docker run -d --hostname my-rabbit -p 5672:5673 rabbitmq:3
docker run -d -p 8091-8094:8091-8094 -p 11210:11210 couchbase
```

对于 Couchbase，我必须打开位于[的管理控制台 http://localhost:8091](http://localhost:8091) ，点击用户协议，并设置“po”桶。一旦准备就绪，我就可以运行服务并观察日志:

```
...
pool-10-thread-20 recevied po PurchaseOrder(id=e186589fd87279f3, price=78023.46, type=Motorcycle, time=2021-01-05T15:43:10.704386Z)
cb-io-kv-17-2 po for motorcycle e186589fd87279f3 confirmed
cb-io-kv-17-2 received motorcycle Vehicle(po=PurchaseOrder(id=e186589fd87279f3, price=78023.46, type=Motorcycle, time=2021-01-05T15:43:10.704386Z), lot=motorcycle lot a)
parallel-8 Generator in finally for signal onComplete
parallel-13 Car consumer in finally for signal cancel
parallel-14 Truck consumer in finally for signal cancel
parallel-7 Motorcycle consumer in finally for signal cancel
parallel-4 Consumer in finally for signal onError
------------------------------------------------------------------------
BUILD SUCCESS
------------------------------------------------------------------------
Total time:  25.775 s
Finished at: 2021-01-05T07:43:23-08:00
------------------------------------------------------------------------
```

打造成功！如果您构建并运行系统，请密切注意所有打印出当前线程的日志。你很快就会看到我之前提到的小传送带的模式(有些是平行运行的)。

我希望这有助于您理解 Project Reactor 的非阻塞 i/o 解决方案。使用方法链接可能是一个很难理解的障碍，但是我相信一旦你掌握了它，阅读起来会容易得多。它采用普通的声明式编程风格，读起来更像命令式风格。你可以在这里找到我的小玩具系统的所有代码:

[](https://github.com/rkamradt/usedvehicles/tree/v0.1) [## rkamradt/二手车辆

### 在 GitHub 上创建一个帐户，为 rkamradt/usedvehicles 的开发做出贡献。

github.com](https://github.com/rkamradt/usedvehicles/tree/v0.1)