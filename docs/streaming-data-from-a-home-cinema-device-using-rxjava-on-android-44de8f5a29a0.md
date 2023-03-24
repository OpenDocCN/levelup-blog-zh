# 使用 Android 上的 RXJava 从家庭影院设备流式传输数据

> 原文：<https://levelup.gitconnected.com/streaming-data-from-a-home-cinema-device-using-rxjava-on-android-44de8f5a29a0>

![](img/66a380b50ae7fe5fc001c94444157d0a.png)

图片由 pixabay.com 提供

# 介绍

在我关于为我的 NAD 家庭影院接收器构建一个 Android 配套应用的最初博客文章中，我解释了我如何使用 Android 的 Netwerk 服务发现来发现局域网上的设备。

在这篇文章中，我将解释我如何使用 RxJava 以一种反应式的流方式连接到家庭影院接收器。同时，这也是最有趣的部分，另一方面，也是最具挑战性的部分。

如果您对使用 RxJava 的原因不感兴趣，请跳过下面的部分，直接进入实现。

# 为什么是 reactive 和 RxJava？

作为一名技术人员，当我在业余爱好项目上投入时间时，我喜欢将实用性和技术性结合起来。我这么说的意思是，我喜欢解决问题，但我也喜欢从技术上挑战自己，从教育的角度去做。

作为一个至少 8 年没有接触 Android 生态系统的人，我很高兴不知道这个生态系统变成了什么。我非常兴奋地看到，反应式编程在 Android 生态系统中也获得了很大的吸引力。为执行异步代码而创建 AsyncTasks 的日子仍然让我不寒而栗，幸运的是这种日子已经一去不复返了。

在过去的 5 年里，我大部分时间都在使用 Scala，我写的很多软件都是在牢记[反应宣言](https://www.reactivemanifesto.org/)的基础上构建的。我使用 Akka Streams 已经好几年了，并且已经习惯了[反应流计划](https://www.reactive-streams.org/)。该计划致力于为具有非阻塞背压的异步流处理提供一个标准。

连接到外部设备，如我的家庭影院接收器，并发送和接收其音量未预先确定的数据非常适合反应式应用的用例。

## 为 Android 选择实现

对于 Java，我知道[项目 Reactor](https://projectreactor.io/) ，它是一个反应流实现。然而，为了迎合尽可能多的潜在用户，我想将最低 SDK 版本设置为 Android 6.0(API 版本 23)。不幸的是，这个版本的 Android 不包括运行 Reactor 所需的 JDK 版本。

我了解到 RxJava 是 Android 上最受欢迎的选择。幸运的是，RxJava 2 还增加了对 Reactive Streams API 规范的支持。

# 深入实施

整个设置从[视图模式](https://developer.android.com/topic/libraries/architecture/viewmodel)开始。`ViewModel`是一个设计用来以生命周期意识的方式管理 UI 相关数据的类。

当`ViewModel`初始化时，我开始 2 个`Observable`流。一个用于监控 WiFi 状态，一个用于发现网络上的家庭影院接收器。

可观察的服务发现是一切开始的地方。当这个流发出时，我引导管理设备连接的可观察对象。

# 监控 WiFi 状态

对于我们的第一个流，我会保持简单。每当用户没有连接到 WiFi 网络时，我想通知他们，这实际上使应用程序变得无用。

为了监控 WiFi 状态，我使用了一个`BehaviorSubject`，它连接到安卓`ConnectivityManager.NetworkCallback`。A `BehaviorSubject`向每个订阅的观察者发送它最近观察到的项目和所有后续观察到的项目。

正如您在上面的代码片段中看到的，在类的实例化中，我创建了一个私有的`BehaviorSubject`，并将`BehaviorSubject`作为`state`属性对外公开。通过实现`ConnectivityManager.NetworkCallback`的两个方法，我在`ViewModel`中向我们的下游订阅发出正确的状态。

在`ViewModel`中，我能够调整与 UI 相关的属性，这些属性更新我的视图以向用户显示信息性消息。

## 服务发现可观测量

自从我在[博客上发布了关于在网络上发现设备的帖子](https://mrooding.me/using-androids-network-service-discovery-to-connect-to-a-device-on-the-local-network-577002324a19)之后，我发现相当多的用户拥有使 DNS-SD 不可用的网络路由设备，这导致了相当多的用户感到沮丧。

我最近发现 NAD 通过 UDP 端口 11430 发出一个辅助的、专有的服务发现协议。我已经更新了应用程序来扫描这两种实现。这意味着有两个可观察的流，当它们发现一个设备时，可能都发出一个项目。我不关心哪个协议能找到设备。我所关心的是找到一个设备，我可以开始连接尝试。

为了解决这个问题，我创建了一个`ServiceDiscoveryProtocol`接口，我将用它来实现这两个协议。除此之外，第三个实现将负责启动和停止两个协议实现，最重要的是，它将把两个可观察对象合并成一个可观察对象，供下游使用。

所有实现都公开了一个`Observable<ServiceDiscoveryState>`,这是一个指示发现状态的数据结构。

每个实现都使用与`WifiManager`所示相同的模式:通过隐藏真实可观察对象的身份来暴露私有属性。与`WifiManager`的不同之处在于，两个发现协议的实现使用了`PublishSubject`而不是`BehaviorSubject`。`BehaviorSubject`会记住最后发出的项目，即使消费者尚未订阅。`PublishSubject`只发出订阅发生后发出的值。

下面显示了我在最初的帖子中展示的网络服务发现的一个过于简化的实现。就像在`WifiManager`中一样，每当触发正确的侦听器回调时，我都会向下游发出一个项目。

最后但并非最不重要的一点是，将所有东西都联系在一起的`ServiceDiscoveryProtocol`的实现使用`Observable.merge`来合并这两个可观测量。

现在我已经有了合并两个可能的服务发现协议的`ServiceDiscoveryProtocol`实现，我可以在我的`ViewModel`中创建它的一个实例。使用该实例，我可以订阅暴露的`Observable`并处理发现的设备。

# 连接到设备

## 创建套接字

如`ViewModel`实现的最后一段代码所示，当找到一个设备时，就会调用一个`connect`方法。connect 方法最重要的部分是建立套接字连接，以便向 NAD 发送和接收数据。

为此，我创建了一个类，它将公开一个`Observable<Either<Throwable, Socket>>`以供进一步使用。`Observable`将尝试根据设备的 IP 地址和主机名创建一个`Socket`。可见链的起点是一个`PublishSubject`。建立套接字连接将以可重试的方式进行。这在`retryWhen`块中处理，该块最多重试 10 次，延迟 500 毫秒。如果超过 10 次尝试，将返回一个`Observable.error`。

然而，我不想让流失败，这就是为什么`Observable`返回一个`Either`类型。这个`Either`类型来自 [arrow](https://arrow-kt.io/) Kotlin 库，它在 Kotlin 标准库的基础上增加了很多函数类型。通过使用这个`Either`类型，当我在下游使用`Observable`时，我可以区分“软故障”(一个`Either.left<Throwable>`)和成功案例(一个`Either.right<Socket>`)。为了转换重试次数达到最大尝试次数时发生的`Observable.error`，我使用`onErrorReturn`函数将失败的`Observable`转换为`Either.left`。

这种方法的主要优点是我可以将一个不可变的`Observable`声明为类属性 val。如果我不使用`Either`类型，那么流就会失败，当重新连接时，我必须重新实例化包含`Observable`属性的类。如下一段代码所示，重新连接现在就像使用`PublishSubject`发出一个`Unit`一样简单。

最后但同样重要的是，该流使用`cache`函数来确保最新的值被缓存。这将防止我每次订阅并使用发出的值时创建新的套接字。至关重要的是，我将在下一节中定义的下游`Source`和`Sink`使用相同的`Socket`实例。如果它们不使用同一个插座，使用`Sink`发送到设备的命令将不会在`Source`上收到应答。你可能会想，等等，他是不是把`Sink`和`Source`搞混了？请记住，我是从应用程序的角度来引用这些概念的。这意味着我发送到设备的数据将使用`Sink`。我们的应用程序从设备接收的数据将通过`Source`接收。

## 构建源和汇

有了`SocketProvider`之后，我现在想在此基础上创建一个 [Okio](https://github.com/square/okio) `Source`和`Sink`。Okio 是一个 Kotlin 库，可以更容易地执行 io 操作。其中，它提供了它的流类型`Source`和`Sink`，它们本质上是`InputStream`和`OutputStream`的包装器，有几个好处。

`SourceProvider`和`SinkProvider`的实现完全相同，它们都将`Observable<Either<Throwable, Socket>>`映射到`Observable<Either<Throwable, A>>`，其中`A`要么是`BufferedSource`要么是`BufferedSink`。这个新的`Observable`再次被公开为一个公共类属性，就像我以前做的那样。

最后但同样重要的是，这两个类再次使用了`cache`函数来防止创建过多的冗余实例。

刚刚描述的`SourceProvider`的整个实现:

## 从 NAD 读取数据

控制发送命令和接收响应的逻辑位于一个`ReceiverRepository`类中。

为了读取设备发送的响应，我声明了一个名为`responses`的类属性，它将我们的`SourceProvider`返回的`Observable<Either<Throwable, BufferedSource>>`映射到一个`Observable<Either<Throwable, SocketResponse>>`。`SocketResponse`是设备支持的协议的包装器，但是它封装了设备发送的单个响应行。

阅读反应的核心发生在`createSocketResponseStream`方法中。在这里，我把`fold`放在`Either<Throwable, BufferedSource>`上。如果它是一个`Either.left`，因此是一个`Throwable`，我简单地把它传递给下游。如果它是一个`Either.right`，因此是一个有效的`Source`，我创建一个`Observable.interval`，它将尝试每 10 毫秒从`Source`中读取一行。

这样做的原因是我需要一种方法来保持从`Source`中读取行。如果`Source`没有任何新的行要读取，它将阻塞用于运行`Observable.interval`的线程。如果没有收到数据，它就不会持续触发 10 毫秒的时间间隔，而是耐心等待新的数据。

在收到新的一行时，我试图解析那一行。如果成功，我返回一个`Either.right<SocketResponse>`。如果它失败了，我再次通过使用`Either.left`构造优雅地防止流失败。

## 向 NAD 发送命令

发送命令的逻辑比读取响应要简单得多。然而，Kotlin 协程的形式增加了一点复杂性。

使用 Kotlin 协程是为了防止我在 UI 线程上运行 IO 操作。为了向家庭影院接收器发送命令，我使用`.take(1).blockingLast()`以阻塞方式获取`Observable<Either<Throwable, BufferedSink>>`的最后发射值。然后，我映射结果，在`map`中，我将命令写入并刷新到设备。

# 把它们连在一起

谜题的最后一部分是从`ReceiverRepository`订阅我们的`responses`流。我在我的`MainViewModel`订阅了信息流。通过订阅流，我能够根据连接状态做一些事情。我可以控制呈现给用户的用户界面，在第一次连接时向 NAD 发送命令，最后但同样重要的是，在`ViewModel`状态下保存收到的响应。

# 结论

虽然听起来很简单，但连接到家庭影院以及从家庭影院发送和接收信息要比想象中复杂得多。在我构建 Android 应用的整个过程中，这无疑是最复杂的部分。我使用了很多不同的 RxJava 技术，最终得到了一个高性能、容错、可维护、松散耦合且易于测试的解决方案。总的来说，还有改进的地方需要重构，但是总的来说，我对这个实现非常满意。

我确实认识到这个解决方案非常复杂，我非常好奇尝试其他方法，比如 Kotlin Flow，看看我是否能降低代码复杂性。