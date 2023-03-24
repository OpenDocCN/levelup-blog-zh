# 结合使用领域

> 原文：<https://levelup.gitconnected.com/using-realm-with-combine-288afa199b33>

![](img/24e217ce50da462490e82828e43290c1.png)

照片由[克里斯·莱佩尔特](https://unsplash.com/@cleipelt?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Combine 是苹果的反应式编程框架。它很强大，我想在现实世界中尝试一下，与 Realm 集成。从[版本 5.0.0](https://github.com/realm/realm-cocoa/releases/tag/v5.0.0) 开始，Realm 增加了对 Combine 和 SwiftUI 的支持，大家试一试吧！🎆

# 模型

我们将使用我之前关于领域的[文章的相同模型:](https://blog.usejournal.com/realm-tips-of-an-ios-developer-26ca1654adaf)

# 使用

我们的目标是创建一个名为`writeObject`的发布者适配器，并声明性地使用它:

1.  有`Output == Any`的发行商。它可以是提供 JSON 的任何东西，比如 [URLSession。数据任务发布者](https://developer.apple.com/documentation/foundation/urlsession/datataskpublisher)
2.  我们的目标是定义领域对象的类型和接收线程
3.  结果，看看已经打出来的有多甜😍

# 发布者适配器

这里的目标是在幕后处理多线程。为了实现这一点，我们扩展了[发布者](https://developer.apple.com/documentation/combine/publisher)协议:

1.  首先，看看返回类型`AnyPublisher<T, Error>`。`T`是结果发布者的`Output`类型，`Error`是它的`Failure`类型。另外，请注意，我们只扩展了那些`Output`和`Failure`分别匹配`Any`和`Error`的发布者。关于下面第 6 个项目符号中的`AnyPublisher`类型的更多信息。
2.  我们将在领域发布者下创建的发布者
3.  来自[苹果](https://developer.apple.com/documentation/combine/anypublisher/subscribe(on:options:)) : `subscribe(on:) changes the execution context of upstream messages`。我们告诉`AddObject`发布者在领域队列中运行
4.  由 Realm 团队创建，它`enables passing thread-confined objects to a different dispatch queue`。如果你删除这一行，应用程序将崩溃。
5.  由 Realm 团队重载，它指定了返回线程(已经考虑了我们的`ThreadSafeReference`)
6.  清理我们的返回类型，否则，它将:`RealmSwift.Publishers.DeferredHandover<Combine.Publishers.SubscribeOn<Combine.Publishers.FlatMap<RealmSwift.Publishers.AddObject<T>, Self>, DispatchQueue>, DispatchQueue>`👀

# 出版者

我们将在名为 AddObject 的领域发布者下创建一个自定义发布者。我们有两项任务:

*   定义关联的类型`Output`和`Failure`
*   为订阅者订阅订阅

1.  将我们的输出设置为通用对象类型
2.  设置我们的失败为`Error`
3.  协议一致性，限制我们的一般[订户](https://developer.apple.com/documentation/combine/subscriber)与我们的失败和输出类型兼容。
4.  我们将要创建的[订阅](https://developer.apple.com/documentation/combine/subscription)
5.  发布者唯一应该做的事情是:为订阅者订阅订阅

# 签署

这是奇迹发生的地方。订阅的职责是响应订阅者的需求请求。

1.  同样，限制我们的通用[订阅者](https://developer.apple.com/documentation/combine/subscriber)与我们的失败和输出类型兼容。
2.  协议一致性，[该功能](https://developer.apple.com/documentation/combine/subscription/request(_:))负责控制用户的[需求](https://developer.apple.com/documentation/combine/subscribers/demand)并提供正确数量的响应。我没有计算请求的数量，而是创建了一个缓存变量`result`，并在每个请求中发送响应。不知道这是不是最好的方法，我会很感激你在这里的评论😄
3.  符合[可取消](https://developer.apple.com/documentation/combine/cancellable)协议
4.  将成功的结果传递给订户。
5.  通知订阅者发布者已完成其工作
6.  将失败结果传递给订户

# 最终想法

这是我第一次接触反应式编程和 Combine 框架，我真的很兴奋！联合是强大的，我期待在 [Tellus](https://www.tellusapp.com/) 使用这些策略。我希望你和我一样喜欢这次旅行🚀

跟随我阅读指数技术和软件开发😄

# 参考

 [## Apple 开发者文档

developer.apple.com](https://developer.apple.com/documentation/combine) [](https://github.com/realm/realm-cocoa/pull/6488) [## 通过 tgoyne Pull 请求#6488 realm/realm-cocoa 添加对 Combine/SwiftUI 的支持

### 这是目前缺少的 API 文档，我计划在完成发布的第一稿后编写它…

github.com](https://github.com/realm/realm-cocoa/pull/6488) [](https://medium.com/flawless-app-stories/swift-combine-custom-publisher-6d1cc3dc248f) [## 如何在 Combine 中创建自定义发布者

### 了解如何在 Swift 中创建自己的联合出版商

medium.com](https://medium.com/flawless-app-stories/swift-combine-custom-publisher-6d1cc3dc248f) [](https://thoughtbot.com/blog/lets-build-a-custom-publisher-in-combine) [## 让我们在 Combine 中构建一个自定义发布器

### 在开始使用 Combine，打了几个网络电话，也许还试用了 Timer publisher 或 KVO 之后…

thoughtbot.com](https://thoughtbot.com/blog/lets-build-a-custom-publisher-in-combine)