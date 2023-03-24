# RxJS 科目举例说明

> 原文：<https://levelup.gitconnected.com/rxjs-subjects-explained-with-examples-78ae7b9edfc>

## RxJS 中的主题说明和 4 种不同类型的示例

![](img/952cab785bed09057baed9bb41bfd976.png)

诺兰·伊萨克在 [Unsplash](https://unsplash.com/search/photos/coffee?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在这篇文章中，我将通过例子解释 RxJS 中的 4 种主题类型以及何时使用每种主题。

1.  `Subject`
2.  `BehaviorSubject`
3.  `RelaySubject`
4.  `AsyncSubject`

更多类似的内容，请查看 https://betterfullstack.com 的

# 科目

RxJS `Subject`是一种特殊类型的`Observable`，它允许将值多播给多个`Observers`。

> *一个* `*Subject*` *就像一个* `*Observable*` *，但是可以多播到多个* `*Observers*` *。* `*Subjects*` *就像* `*EventEmitters*` *:它们维护着许多侦听器的注册表。*

每个`Subject`都是一个`Observable`。

每个`Subject`都是一个`Observer`。

A `Subject`向订阅的观察者发送数据。任何以前发出的数据都不会发送给新的观察者。

# 行为主体

`BehaviorSubject`存储向其消费者发出的最新值，每当新的`Observer`订阅时，它将立即收到“当前值”。

> `BehaviorSubjects`用于表示“随时间变化的值”。例如，生日事件流是一个`Subject`，但是一个人的年龄流将是一个`BehaviorSubject`。

将最后的数据发送给新的观察者。

# 中继主题

一个`ReplaySubject`类似于一个`BehaviorSubject`,它可以发送旧值给新订户，但是它也可以*记录*执行的一部分`Observable`。

> *一个* `*ReplaySubject*` *记录来自* `*Observable*` *执行的多个值，并将它们重放给新订户。*

先前发送的数据可以“重放”给新的观察者。

# async 主语

当序列完成时，A `AsyncSubject`将向观察者发出最后一个值。

# 摘要

RxJS `Subject`是一种特殊类型的`Observable`，它允许将值多播给多个`Observers`。

此外，我们还有 3 个特殊科目，分别是`**BehaviorSubject**`、`**RelaySubject**`和`**AsyncSubject**`。它们在观察者接收数据的方式上表现不同。

我希望这篇文章对你有用！你可以在[媒体](https://medium.com/@transonhoang?source=post_page---------------------------)上跟随我。我也在推特上。欢迎在下面的评论中留下任何问题。我很乐意帮忙！

# 资源/参考资料

[1]:主题[https://rxjs-dev.firebaseapp.com/guide/subject](https://rxjs-dev.firebaseapp.com/guide/subject)

[](https://gitconnected.com/learn/angular) [## 学习角度-最佳角度教程(2019) | gitconnected

### 50 大角度教程-免费学习角度。课程由开发人员提交和投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/angular) [](https://betterfullstack.com/stories/) [## 故事-更好的全栈

### 关于 JavaScript、Python 和 Wordpress 的有用文章，有助于开发人员减少开发时间并提高…

betterfullstack.com](https://betterfullstack.com/stories/)