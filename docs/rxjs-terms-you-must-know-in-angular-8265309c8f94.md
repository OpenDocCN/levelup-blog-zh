# 你必须知道的角度术语

> 原文：<https://levelup.gitconnected.com/rxjs-terms-you-must-know-in-angular-8265309c8f94>

## 有角的

## 理解 RxJS 术语:observables、operators、observer、subscription、subject 和 scheduler，在 Angular 应用程序中使用简单的解释。

![](img/c14f7260f88857318659e86db3913772.png)

[刘汉宁内巴霍](https://unsplash.com/@hannynaibaho?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/coffee?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在反应式编程中，有 6 个流行术语是:

*   可观察量
*   经营者
*   观察者
*   签署
*   科目
*   调度程序

在这个故事中，我将介绍这些术语作为新手开始学习反应式编程的基础知识。

*更多类似内容，请查看*[*https://betterfullstack.com*](https://betterfullstack.com)

# **可观察的**

*可观察的是可以随时间到达的数据流或数据源。*

在 Angular 应用程序中，您可以从两种主要情况创建数据源:

HTTP 请求:

```
import { ajax } from "rxjs/ajax";const URL = "https://jsonplaceholder.typicode.com/posts";
const posts$ = ajax.getJSON(URL);
```

用户界面事件:

```
import { fromEvent } from 'rxjs';const searchBtn = document.getElementById('search-user');
const searchUser$ = fromEvent(searchBtn, 'click');
```

> 在[反应式编程的组件](/components-in-reactive-programming-365c7bd9d271)中阅读更多关于一般概念的内容。

*An****RxJS Subject****是一种特殊类型的可观察对象，允许将值多播给许多观察者。*

你可以把一个主题想象成类似于 EventEmitter 的 Angular。查看更多关于我的另一个故事 [RxJS 主题的详细信息，并举例说明](/rxjs-subjects-explained-with-examples-78ae7b9edfc)。

注意:可观测量是单播的。这意味着每个订阅的观察者拥有可观察对象的独立执行。

# **订阅**

*订阅是代表可支配资源的对象，通常是可观察的执行。*

每当你观察到一个物体，它就会被创造出来。你提供的功能是一个**观察者**，在那里我们决定如何对发生的每一个事件做出反应。

一个**观察器**有三个主要功能:`next()`、`error()`和`complete()`。

```
import { fromEvent } from 'rxjs';const searchBtn = document.getElementById('search-user');
const searchUser$ = fromEvent(searchBtn, 'click');const subscription = searchUser$.subscribe(event => console.log(event));
```

**重要提示:**一旦调用`subscribe`，它将创建一个订阅，在组件生命周期结束时，您需要删除它以避免内存泄漏。

**观察器**在反应式编程中使用默认的**调度器**，反应式编程在观察器开始发出任何通知之前执行。

您可以定制自己的调度程序，并将其应用于您的可观察对象。你可以认为**调度器**是控制并发性的集中式调度器，允许我们在计算发生在`setTimeout`或`requestAnimationFrame`或其他上时进行协调。这将使您的 web 应用程序具有更好的性能。

更简单地说，**调度器**是一种“安排”一个动作在未来发生的机制。

# **操作员**

*运算符提供了一种处理来自源的值的方法，返回转换值的可观察值。*

简单来说，你可以认为运算符是**函数**。

有两种运算符:

*   可管道化操作符
    *可管道化操作符是一个函数，它将一个可观察对象作为其输入，并返回另一个可观察对象。这是一个纯粹的操作:之前的可观察值保持不变。* 你可以认为这类运算符是我们可以应用函数式编程的地方，就像管道函数或者 compose 函数一样。
*   创建操作符
    *创建操作符是一种可以作为独立函数调用来创建新的可观察对象的操作符。* 你可以认为这种运算符会帮助你创建一个新的可观察对象。

```
import { of } from 'rxjs';
import { map } from 'rxjs/operators';const dataSource = of(1, 2, 3, 4, 5); // of(...) is creation operators which create new observableconst subscription = dataSource
.pipe(map(value => value + 1))
.subscribe(value => console.log(value)); // map(...) is Pipeable operator
```

# 摘要

这个故事帮助你理解反应式编程中的 6 个流行术语。当你理解了基本原理，RxJS 是非常容易实现的。

我希望这篇文章对你有用！可以跟着我上[媒](https://medium.com/@transonhoang?source=post_page---------------------------)。我也在推特上。欢迎在下面的评论中留下任何问题。我很乐意帮忙！

# 资源/参考资料

[1]:反应文件[https://rxjs-dev.firebaseapp.com/guide/subject](https://rxjs-dev.firebaseapp.com/guide/overview)

你可以在这里阅读更多关于 rxjs 的高级故事:

[](/approach-reactive-programming-in-modern-web-application-b20f59b7699d) [## 现代 Web 应用程序中的反应式编程

### 了解如何在现代 web 应用中处理异步代码，以及我们如何管理它——从回调和承诺到 RxJS

levelup.gitconnected.com](/approach-reactive-programming-in-modern-web-application-b20f59b7699d) [](/angular-and-rxjs-patterns-use-reactive-programming-to-compose-and-manage-data-in-angular-apps-2e0c4ce7a39c) [## Angular 和 RxJS 模式—使用反应式编程在 Angular 应用程序中编写和管理数据

### 通过 rxjs 创建数据流、创建可组合流、创建动作和进行缓存的指南。

levelup.gitconnected.com](/angular-and-rxjs-patterns-use-reactive-programming-to-compose-and-manage-data-in-angular-apps-2e0c4ce7a39c) [](/handle-multiple-api-requests-in-angular-using-mergemap-and-forkjoin-to-avoid-nested-subscriptions-a20fb5040d0c) [## 使用 mergeMap 和 forkJoin 在 Angular 中处理多个 API 请求，以避免嵌套订阅

### 指导如何在调用多个 API 时使用 mergeMap 和 forkJoin 避免嵌套订阅。

levelup.gitconnected.com](/handle-multiple-api-requests-in-angular-using-mergemap-and-forkjoin-to-avoid-nested-subscriptions-a20fb5040d0c)