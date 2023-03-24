# 关闭观察点

> 原文：<https://levelup.gitconnected.com/closing-observables-e612803517dc>

![](img/ebc0daec22e04a382561a56428c205ed.png)

蒂姆·莫斯霍尔德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在 Angular 中，我们在任何地方都可以使用 RxJs。在反应式编程范例中，我们认为数据流要么是懒惰的，要么是急切的，当不再需要时，可以订阅和必须取消订阅。否则，我们将面临不必要行为的风险，在最坏的情况下，甚至会出现内存泄漏。幸运的是，有很多方法可以退订可观的东西，但有些方法肯定比其他方法好。

## 显式描述

作为 RxJs 的初学者，最常见的关闭可观测量的方法是显式方法。当调用 subscribe 方法时，会返回一个订阅，我们可以在 ngOnDestroy 生命周期挂钩中取消订阅。我们可以只创建一个订阅对象，并将具体的订阅添加到类属性中，而不是将这些订阅对象保存在多个类属性中。

订阅对象

通过使用 subscription 对象并将订阅添加到其中，代码变得更加简洁，但是老实说，这并不是明智的终结，因为它迫使您在代码中进行订阅，实际上一点也不被动。

## 隐式退订

更好的退订方式是隐式退订，或者换句话说:被动退订。在这种情况下，我们不必保存任何订阅并明确取消订阅。我们唯一要做的，就是利用关闭管道操作符，比如:

*   直到
*   拿
*   投掷误差

被动退订

如您所见，这是真正的反应性，在使用 RxJs 时，这始终是一个可实现的目标。当主题第一次发出时，takeUntil 运算符将取消订阅。另一方面，take 运算符有一个固定的排放量，在达到该量后关闭。

## 异步管道

绝对最好和最简单的方法是在模板中使用异步管道，因为它自动订阅和取消订阅可观察对象，因此我们编写的代码要少得多。

异步管道

使用 ng-container 和 ngIf 来解包可观察对象的值，这是一个很好的实践，可以确保可观察对象已经发出了一个值，而不需要创建额外的 DOM 元素。请注意，不要在代码中订阅可观察对象，因为异步管道会自动订阅。

## 取消订阅热门观察

另一个经常被遗忘的重要案例是关于热可观测量。之前，我们研究了冷的可观察对象，但通常我们需要通过使用 share 或 shareReplay 管道操作符将冷的可观察对象变成热的，该操作符将一个主题连接到原始可观察对象，并将内部主题公开为可观察对象，这样就确实是热的。使用 share 不会有问题，但是使用 shareReplay 时如果不小心，可能会遇到问题。

热 vs 冷

我们把连接一个主体到冷可观察对象的过程叫做 **connect** ，这样我们也有一个叫做 **disconnect** 的过程，当所有对热可观察对象的订阅都被关闭时，它取消对冷可观察对象的订阅。Share 确实会为我们自动断开连接，但是 shareReplay 可能会很麻烦，因为 refCount 参数必须设置为 true，这样它就会自动断开连接。

# 结论

有许多方法可以取消订阅，而最好的方法是使用异步管道，或者当您需要在代码中订阅时使用反应式取消订阅。你应该始终确保你所有的订阅将在某一点被取消订阅，否则你会遇到内存泄漏或不必要的行为的风险。

**Pro 提示:**通过查看 DevTools 中的 Memory 选项卡并创建一个堆快照，您可以很容易地查看当前订阅了多少 observable，这样您就可以搜索 observable 并查看有多少实例是活动的。

## 推荐链接

如果你喜欢这篇文章，我很乐意分享我的**推荐链接**:

[](https://medium.com/@stefan.haas.privat/membership) [## 通过我的推荐链接-斯特凡·哈斯加入媒体

### 阅读斯特凡·哈斯的每一个故事(以及媒体上成千上万的其他作家)。您的会员费直接支持…

medium.com](https://medium.com/@stefan.haas.privat/membership) 

## **其他有趣的阅读:**

[](/refactoring-angular-applications-be18a7ee65cb) [## 重构角度应用程序

### 重构是软件工程中最重要的技术之一，因为它是项目进行的唯一方式

levelup.gitconnected.com](/refactoring-angular-applications-be18a7ee65cb) [](/angular-dont-use-functions-in-templates-f33d67db18da) [## Angular:不要在模板中使用函数

### 在这篇简短的博文中，我将向你展示 Angular 的一个核心概念，这是每个 Angular 开发者都应该知道的…

levelup.gitconnected.com](/angular-dont-use-functions-in-templates-f33d67db18da) [](/moduliths-in-angular-with-nx-b8b0076794fb) [## 以 Nx 为单位的角度模数

### 使用 DDD 和 Monorepos 创建可持续应用

levelup.gitconnected.com](/moduliths-in-angular-with-nx-b8b0076794fb) [](/your-first-angular-microfrontend-58950768a465) [## 你的第一个角形微前端

### 这是一个关于如何创建一个简单的 angular 应用程序来使用另一个应用程序的模块的分步指南…

levelup.gitconnected.com](/your-first-angular-microfrontend-58950768a465)