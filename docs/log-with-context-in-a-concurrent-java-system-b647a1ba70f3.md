# 并发 Java 系统中的上下文日志

> 原文：<https://levelup.gitconnected.com/log-with-context-in-a-concurrent-java-system-b647a1ba70f3>

确保多线程之间的一致日志记录。

![](img/38a724c72dfcfa1834df3a32312478c3.png)

照片由[托马斯·佩汉姆](https://unsplash.com/@tompeham?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/log?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

有很多 Java 日志库和外观，我相信每个人都有自己喜欢的。许多人都有一个共同点，那就是 MDC 工具提供了上下文日志记录。因此，如果您的服务有某种想要添加到日志中的事件 id，您可以使用`MDC.put(“eventId”, eventId)`来确保处理该事件的所有日志都将打印出该事件 id。

出现这个问题是因为它假设一个事件的处理都发生在一个线程上，尽管可能有多个线程同时处理多个事件。如果单个事件在不同的线程上处理，这种假设会导致系统陷入混乱。在像 [Project Reactor](https://projectreactor.io/) 这样的非阻塞系统中，线程来来去去，完全不可能预测在任何时间点哪个线程正在进行处理。

我的前几篇文章(见下面的文章列表)一直在一个示例二手车处理系统中工作，日志记录是用`System.out.println`完成的，只是为了保持简单。我们只记录了线程和一条消息，因此我们可以观察线程是如何处理的。这是他们的样子:

```
parallel-11 produced: PurchaseOrder(id=d0fb7857041cc7a0,
    price=65585.10, type=Car, time=2021-01-27T18:40:05.247210Z)
pool-29-thread-4 recevied po PurchaseOrder(id=d0fb7857041cc7a0,
    price=65585.10, type=Car, time=2021-01-27T18:40:05.247210Z)
parallel-14 produced: PurchaseOrder(id=34c0ef03c996cfce,
    price=29356.60, type=Car, time=2021-01-27T18:40:05.252178Z)
pool-29-thread-6 recevied po PurchaseOrder(id=34c0ef03c996cfce,
    price=29356.60, type=Car, time=2021-01-27T18:40:05.252178Z)
rabbitmq-sender-connection-subscription-6 sending car
   Vehicle(po=PurchaseOrder(id=d0fb7857041cc7a0, price=65585.10,
   type=Car, time=2021-01-27T18:40:05.247210Z), lot=)
```

我们可以看到所有东西运行的线程，但是我们只能知道哪个日志行与哪个事件相关，因为它们都显示购买订单。情况可能并不总是这样，所以我们应该发送一个事件 id，它在事件最终流经的所有服务中都是一致的。

我仍然打算用`System.out.println`来记录日志，这样我所展示的内容将适用于你碰巧使用的任何一个记录系统。我将创建一个上下文类型和一个静态方法，用于使用对所有服务都可用的上下文进行日志记录。这将会是这样的:

```
[@Data](http://twitter.com/Data)
@Builder
[@NoArgsConstructor](http://twitter.com/NoArgsConstructor)
[@AllArgsConstructor](http://twitter.com/AllArgsConstructor)
public class ContextLogging {
    private String eventId;
    private String serviceName;
    public static void log(ContextLogging context, 
            String msg) {
        Instant instant = Instant.now();
        System.out.println(instant.toString() 
                + " ["
                + Thread.currentThread().getName()
                + "] service: "
                + context.getServiceName()
                + " event id: "
                + context.getEventId()
                + " msg: "
                + msg);
    }
    public static void log(String msg) {
        Instant instant = Instant.now();
        System.out.println(instant.toString() 
                + " ["
                + Thread.currentThread().getName()
                + "] msg: "
                + msg);
    }
}
```

我还创建了一个没有上下文的 log 方法，用于记录与事件无关的事情，比如启动或关闭例程。

为了让处理事件的所有方法都可以使用上下文，我将创建一个`Tuple`，首先包含上下文，然后包含事件数据。我以前做过这样的事情来处理事件状态在系统中的构建。

随身携带所有这些上下文似乎很麻烦，但是如果您想生活在一个没有全局或线程本地状态的系统中，您几乎别无选择。首先，我们可以将上下文添加到`PurchaseOrderGenerator`类中，它更像是一个测试类，我在测试系统时用它来驱动系统。下面是新代码，它生成一个新的事件 id 和一个随机购买订单，并将其发送到消息队列:

```
final Sender sender = RabbitFlux.createSender(soptions);
sender.declareQueue(QueueSpecification.queue(QUEUE_NAME));        
sender.sendWithPublishConfirms(
  Flux.generate((sink) -> sink.next(createRandomPurchaseOrder()))
    .cast(PurchaseOrder.class)
    .map(i -> Tuples.of(ContextLogging.builder()
      .serviceName("PurchaseOrderGenerator")
      .eventId(UUID.randomUUID().toString())
      .build(), i))
    .delayElements(Duration.ofMillis(100))
    .take(Duration.ofMillis(1000))
    .doOnNext(t -> ContextLogging.log(t.getT1(), "produced: "
            + t.getT2()))
    .map(t -> new OutboundMessage("", 
            QUEUE_NAME, 
            writeJson(t.getT1(), t.getT2()).orElse("").getBytes()))
    .doFinally((s) -> {
         ContextLogging.log(
            "PurchaseOrderGenerator in finally for signal " + s); 
         sender.close();
    })
)
.subscribe();
```

创建随机采购订单后，我做的第一件事是将它映射到一个包含新上下文和采购订单的`Tuple`。那么之后链接的所有方法都有这两个项目。此外，我将把上下文传递给`writeJson`方法，因为我们希望将事件 id 与采购订单一起发送，这样我们就可以将下游服务中的项目关联起来。最后要注意的是，`doFinally`方法中的`log`调用没有上下文，因为它是关机例程的一部分，与事件无关。

正如我们刚才提到的，事件 id 需要和事件一起发送到消息队列。因此，我们创建了一个名为`Payload`的类型，它包含事件 id 和采购订单，然后我们对其进行序列化。我可以将`Payload`移动到它自己的文件中，但是我不会这么做。我们试图模拟不同的服务，这些服务可能来自不同的团队，而这些团队没有能力共享代码。它不是很枯燥，但有时你必须分离事物，这通常意味着复制事物。下面是序列化事件 id 和采购订单的代码:

```
public static class Payload {
  public Payload() {}
  public Payload(String eventId, PurchaseOrder po) {
    this.eventId = eventId;
    this.po = po;
  }
  public String eventId;
  public PurchaseOrder po;
}

private static Optional<String> writeJson(ContextLogging context,
                  PurchaseOrder po) {
  try {
    return Optional.of(writer.writeValueAsString(new
               Payload(context.getEventId(),po)));
  } catch (JsonProcessingException ex) {
    ContextLogging.log(context, "unable to serialize po");
    ex.printStackTrace(System.out);
    return Optional.empty();
  }
}
```

完成后，我们将必须修改消费服务以读入事件 id 和采购订单，并从中创建一个新的上下文。在`PurchaseOrderConsumer`类中，我更改了从消息队列中解析数据的方法:

```
public static class Payload {
  public Payload() {}
  public Payload(String eventId, PurchaseOrder po) {
    this.eventId = eventId;
    this.po = po;
  }
  public String eventId;
  public PurchaseOrder po;
}

private static Payload readJson(String po) {
  try {
    return reader.readValue(po);
  } catch (JsonProcessingException ex) {
    ContextLogging.log("unable to serialize po");
    ex.printStackTrace(System.out);
    Payload empty = new Payload();
    empty.eventId = "no event id";
    empty.po = PurchaseOrder.builder().build();
    return empty;
  } catch (IOException ex) {
     ContextLogging.log("unable to serialize po");
     ex.printStackTrace(System.out);
     Payload empty = new Payload();
     empty.eventId = "no event id";
     empty.po = PurchaseOrder.builder().build();
     return empty;
  }
}
```

然后，我们可以设置采购订单的处理链:

```
Map<String, Function<GroupedFlux<String, 
            Tuple2<ContextLogging, PurchaseOrder>>, 
                Publisher<OutboundMessageResult>>> 
  publisherMap = Map.of(
    "Car", (o) -> getCarPublisher(sender, o),
    "Truck", (o) -> getTruckPublisher(sender, o),
    "Motorcycle", (o) -> getMotorcyclePublisher(sender, o));
receiver
  .consumeAutoAck(PO_QUEUE_NAME)
  .timeout(Duration.ofSeconds(10))
  .doFinally((s) -> {
     ContextLogging
       .log("Purchase Order Consumer in finally for signal " + s);
    receiver.close();
    sender.close();
    cluster.disconnect();
  })
  .map(p -> readJson(new String(p.getBody())))
  .map(p -> Tuples.of(ContextLogging.builder()
     .serviceName("PurchaseOrderConsumer")
     .eventId(p.eventId)
     .build(), p.po))
  .filter(t -> t.getT2().isValid())
  .doOnNext(t -> ContextLogging.log(t.getT1(), 
         "recevied po " + t.getT2()))
  .doOnDiscard(Tuple2.class, t ->
       ContextLogging.log((ContextLogging)t.getT1(), 
         "Discarded invalid PO " + t.getT2()))
  .flatMap(t -> writePoJson(t.getT1(),t.getT2())
    .map(j -> reactiveCollection
      .upsert(t.getT2().getId(), j)
      .map(result -> t))
    .orElse(Mono.just(t)))
  .groupBy(t -> t.getT2().getType())
  .flatMap(v -> publisherMap.get(v.key()).apply(v))
  .subscribe();
```

一旦我们在`readJson`中解析了来自消息队列的输入，我们就创建一个新的`Tuple`，它具有用原始事件 id 和购买订单构建的新上下文。

现在，当我们查看日志时，我们可以看到时间、线程名称、服务名称和事件 id:

```
2021-01-27T21:20:47.519385Z [parallel-11] service: PurchaseOrderGenerator event id: 3f7650b7-c304-4537-a07f-810a909d7479 msg: produced: PurchaseOrder(id=34f6525f239b5bce, price=8042.14, type=Truck, time=2021-01-27T21:20:47.356310Z)
2021-01-27T21:20:47.756497Z [pool-29-thread-4] service: PurchaseOrderConsumer event id: 3f7650b7-c304-4537-a07f-810a909d7479 msg: recevied po PurchaseOrder(id=34f6525f239b5bce, price=8042.14, type=Truck, time=2021-01-27T21:20:47.356310Z)
2021-01-27T21:20:47.827465Z [parallel-14] service: PurchaseOrderGenerator event id: 525962b9-b760-46cc-99d8-87eb83de84b5 msg: produced: PurchaseOrder(id=2ac5eb8193157cc2, price=77006.06, type=Car, time=2021-01-27T21:20:47.361935Z)
2021-01-27T21:20:47.832642Z [pool-29-thread-5] service: PurchaseOrderConsumer event id: 525962b9-b760-46cc-99d8-87eb83de84b5 msg: recevied po PurchaseOrder(id=2ac5eb8193157cc2, price=77006.06, type=Car, time=2021-01-27T21:20:47.361935Z)
2021-01-27T21:20:47.897228Z [rabbitmq-sender-connection-subscription-6] service: PurchaseOrderConsumer event id: 3f7650b7-c304-4537-a07f-810a909d7479 msg: sending truck Vehicle(po=PurchaseOrder(id=34f6525f239b5bce, price=8042.14, type=Truck, time=2021-01-27T21:20:47.356310Z), lot=)
2021-01-27T21:20:47.905285Z [rabbitmq-sender-connection-subscription-6] service: PurchaseOrderConsumer event id: 525962b9-b760-46cc-99d8-87eb83de84b5 msg: sending car Vehicle(po=PurchaseOrder(id=2ac5eb8193157cc2, price=77006.06, type=Car, time=2021-01-27T21:20:47.361935Z), lot=)
```

您可以看到，即使我们跨服务，具有相同采购订单编号的事件也具有相同的事件 id，因此我们不再依赖于日志中的采购订单编号。在处理的下一步，我们甚至可能没有购买订单号，但是我们应该总是有事件 id 来关联服务与服务之间的日志。

这是一个很好的例子，说明了我们如何构建必须在整个流程中跟踪事件的事件状态。通过使用`Tuple`，我们可以在处理过程中根据需要从事件状态中添加或删除更多的项目，而不必改变核心数据结构。唯一的缺点是，如果你在`Tuple`中有很多项目，事情会变得很混乱。但是应该清楚的是，如果当您省略了一个“>”时，编译器错误变得不可理解，那么您需要重新构建代码。

本文附带的代码:

[](https://github.com/rkamradt/usedvehicles/tree/v0.4) [## rkamradt/二手车辆

### 在 GitHub 上创建一个帐户，为 rkamradt/usedvehicles 的开发做出贡献。

github.com](https://github.com/rkamradt/usedvehicles/tree/v0.4) 

本系列的其他文章:

[](/understanding-reactive-java-e8aaee9a204b) [## 理解反应式 Java

### 因为你的线程阻碍了我的表现。

levelup.gitconnected.com](/understanding-reactive-java-e8aaee9a204b) [](/creating-a-flux-of-fluxes-with-project-reactors-group-by-method-37200bfc2a) [## 用 Project Reactor 的 Group By 方法创建通量通量

### 什么通量？

levelup.gitconnected.com](/creating-a-flux-of-fluxes-with-project-reactors-group-by-method-37200bfc2a) [](/parallel-tasks-in-a-non-blocking-system-66c195aba56d) [## 非阻塞系统中的并行任务

### 利用 Project Reactor 的线程/非阻塞混合模型。

levelup.gitconnected.com](/parallel-tasks-in-a-non-blocking-system-66c195aba56d)