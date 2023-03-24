# 非阻塞系统中的并行任务

> 原文：<https://levelup.gitconnected.com/parallel-tasks-in-a-non-blocking-system-66c195aba56d>

## 利用 Project Reactor 的线程/非阻塞混合模型。

![](img/3af28d8eb2068b6ca40b814427fde643.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1391681) 的 [Erich Westendarp](https://pixabay.com/users/hpgruesen-2204343/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1391681)

一般在 Node.js 和 JavaScript 中，只有一个线程(虽然我觉得 Node.js 新版本允许线程？).这带来了如何处理异步调用的整个生态系统，从 async/await 到基本承诺和低级回调。当 Java 通过像 [Project Reactor](https://projectreactor.io/) 这样的框架加入非阻塞异步处理时，它坚持使用非阻塞 I/O 和线程的混合模型来实现并发。线程被烤成了 Java 馅饼，没有办法摆脱它们。

但是 Project Reactor 提供了一组非常丰富的方法来处理并发性，而不必处理诸如执行器或裸线程之类的低级构造。在我的上一篇文章[通过方法](/creating-a-flux-of-fluxes-with-project-reactors-group-by-method-37200bfc2a?source=your_stories_page-------------------------------------)用 Project Reactor 的组创建 Flux of Fluxes 中，我通过在不同类型上执行三个不同的处理函数，然后将它们与处理结果连接在一起，从而谈到了并发性。

但是，假设您必须并行运行三个不同的任意任务，并让它们将每个并行轨道的输出合并到一个项目中，以便进一步处理。只要所有的任务都返回一个给出每个任务结果的`Mono<T>`，其中`T`是某种类型(或者基于单个接口`T`的不同类型)，你就可以很容易地使用`Flux.combineLatest`变体之一来完成任务。

我将继续使用我在以前的文章中整理的二手车系统来演示您将如何做这件事。我将关注从消息队列接收`Vehicle.Car`类型并对其做进一步处理的服务。在第一次迭代中，它通过查询数据库，添加汽车应该存放的“lot ”,并简单地记录汽车的收据，来检查汽车是否有相应的采购订单。下面是实现这一点的代码:

```
Receiver carReceiver = RabbitFlux.createReceiver(roptions);
carReceiver
  .consumeAutoAck(CAR_QUEUE_NAME)
  .timeout(Duration.ofSeconds(10))
  .doFinally((s) -> {
          log("Car consumer in finally for signal " + s);
          carReceiver.close();
          sender.close();
  })
  .map(j -> readCarJson(new String(j.getBody())))
  .flatMap(o -> Mono.justOrEmpty(o))
  .flatMap(v -> reactiveCollection
     .get(v.getPo().getId())
     .doOnNext(j -> log("po for car " 
              + v.getPo().getId() 
              + " confirmed"))
     .map(j -> v)
     .single()
     .onErrorReturn(v))
  .map(c -> new Vehicle.Car(c.getPo(),"car lot a"))
  .subscribe(c -> log("received car " + c));
```

如果这没有意义，您可能应该回到我在本系列中的原始文章,[理解反应式 Java](/understanding-reactive-java-e8aaee9a204b?source=your_stories_page-------------------------------------) 来复习一下。

这一部分是确认汽车采购订单的部分:

```
.flatMap(v -> reactiveCollection
          .get(v.getPo().getId())
          .doOnNext(j -> log("po for car " 
              + v.getPo().getId() 
              + " confirmed"))
          .map(j -> v)
          .single()
          .onErrorReturn(v))
```

我想改变这一点，以便在将汽车对象插入另一个数据库的同时完成确认。我将假设，如果在数据库中找不到采购订单，验证不应该停止处理，而应该记录日志或做其他事情。所以我可以并行运行这两个任务。为了使代码更容易阅读，我将读取和写入数据库，并将它们放入自己的函数中。这是我想到的:

```
private static Mono<Vehicle.Car> verifyCar(Vehicle.Car car) {
  return poReactiveCollection
     .get(car.getPo().getId())
     .doOnNext(c -> log("po for car " 
         + car 
         + " confirmed"))
     .doOnError((t) -> log("error verifying car"))
     .map(j -> car)
     .single()
     .onErrorReturn(car);
    }

private static Mono<Vehicle.Car> writeCar(Vehicle.Car car) {
  return carReactiveCollection
     .upsert(car.getPo().getId(), car)
     .doOnNext(c -> log("inserted car " 
         + car 
         + " into car database"))
     .doOnError((t) -> log("error inserting car"))
     .map(j -> car)
     .single()
     .onErrorReturn(car);
}
```

写车和看订单很像，只是作用于不同的集合。为了简单起见，除了日志之外，不提供任何错误处理。在这两个函数中，返回类型都是`Mono<Vehicle.Car>`。

现在，我们如何将这些并行运行呢？我们将修改原始代码，并更改如下内容:

```
 .flatMap(v -> reactiveCollection
...
```

新代码将如下所示:

```
.flatMap(v -> Flux.combineLatest((r) -> r[0], 
                   verifyCar(v), 
                   writeCar(v)))
```

这里我们将使用版本的`Flux.combineLatest`，它采用一个合并函数和一个`Publisher`类型的变量列表，并返回一个`Flux`。由于`Publisher`接口是由`Mono`和`Flux`实现的，我们可以使用方法`verifyCar`和`writeCar`，因为它们都返回`Mono<Vehicle.Car>`。`flatMap`将移除外部的`Flux`，因此我们回到`Flux<Vehicle.Car>`作为流程项目。

merge 函数将一个项目数组作为参数，并返回一些其他项目作为结果。数组中的项目数由传入的变量`Publisher`对象数决定。在我们的例子中，数组是一个由`Vehicle.Car`对象组成的数组，它只返回数组中的第一个项目，因为所有的`Publisher`对象都返回相同的`Mono<Vehicle.Car>`。如果您看一下日志，您可以看到每个`Publisher`在一个单独的线程上运行:

```
cb-io-kv-5-3 inserted car Vehicle(po=PurchaseOrder(id=6470945cca029936, price=92071.46, type=Car, time=2021-01-23T19:44:40.885663Z), lot=car lot a) into car database
cb-io-kv-5-2 po for car Vehicle(po=PurchaseOrder(id=6470945cca029936, price=92071.46, type=Car, time=2021-01-23T19:44:40.885663Z), lot=car lot a) confirmed
```

这两个线程都源自 Couchbase。正在发生的是插入和查询操作被启动，然后链的其余部分在 Couchbase 驱动程序提供的线程上执行，当它返回一个结果时。这是因为 Couchbase 驱动程序是被动的。

但是，如果您想要执行的任务没有反应库，会发生什么呢？有许多库在没有非阻塞替代方案的情况下执行阻塞操作。答案是，您可以很容易地将阻塞操作转换成一个`Mono`，然后可以在与非阻塞任务相同的流程中对其进行处理。

因此，让我们创建一个名为“浪费时间”的任务，睡眠 50 毫秒。为了并行运行它，您将它包装在一个 Mono 中，并将其分配给一个调度程序在另一个线程上运行。这是我想到的:

```
private static Mono<Vehicle.Car> wasteTime(Vehicle.Car car) {
   return Mono.fromCallable(() -> { 
      Thread.sleep(50);
      return car;
   })
   .subscribeOn(Schedulers.boundedElastic())
   .doOnNext(c -> log("wasting time on car " + c));
}
```

现在我们可以将它添加到要并行运行的`Publisher`对象列表中:

```
.flatMap(v -> Flux.combineLatest((r) -> r[0], 
    verifyCar(v), 
    writeCar(v), 
    wasteTime(v)))
```

返回类型仍然只是第一个发布者的返回，`verifyCar`。现在，如果我们查看日志，我们可以看到浪费时间完全是在另一个线程上执行的:

```
boundedElastic-7 wasting time on car Vehicle(po=PurchaseOrder(id=36ef40d5b598c2be, price=13969.15, type=Car, time=2021-01-23T19:44:40.885632Z), lot=car lot a)
boundedElastic-7 received car Vehicle(po=PurchaseOrder(id=36ef40d5b598c2be, price=13969.15, type=Car, time=2021-01-23T19:44:40.885632Z), lot=car lot a)
```

注意，`boundedElastic-7`线程是执行最终 subscribe lambda 方法的线程。这应该让您了解调度程序是如何处理线程的；只要没有非阻塞调用介入，它就允许同一个线程继续处理链中的下一项。

所以现在，您应该能够并行运行尽可能多的任务，只要它们都在相同的时间返回(可能是一些结果类型，可能包含特定于返回它的特定任务的信息)，并且它们都可以以某种合理的方式合并(在我们的例子中，我们采用了第一个，而放弃了另外两个)。合并的结果不必与输入数组的类型相同。

下面是本文附带的代码:

[](https://github.com/rkamradt/usedvehicles/tree/v0.3) [## rkamradt/二手车辆

### 在 GitHub 上创建一个帐户，为 rkamradt/usedvehicles 的开发做出贡献。

github.com](https://github.com/rkamradt/usedvehicles/tree/v0.3) 

以下是本文提到的一些文章:

[](/understanding-reactive-java-e8aaee9a204b) [## 理解反应式 Java

### 因为你的线程阻碍了我的表现。

levelup.gitconnected.com](/understanding-reactive-java-e8aaee9a204b) [](/creating-a-flux-of-fluxes-with-project-reactors-group-by-method-37200bfc2a) [## 用 Project Reactor 的 Group By 方法创建通量通量

### 什么通量？

levelup.gitconnected.com](/creating-a-flux-of-fluxes-with-project-reactors-group-by-method-37200bfc2a)