# 承诺和可观察到的区别

> 原文：<https://levelup.gitconnected.com/differences-between-a-promise-and-an-observable-22448f116430>

![](img/ac52af4aabc6227158cb202629d6c6c5.png)

[杰森·登特](https://unsplash.com/@jdent?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/comparison?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在这篇文章中，我概述了我所学到的一些关于可观察事物的基本概念，以及它与承诺的不同之处。

在讨论可观察到的和承诺的区别之前，我们先来谈谈它们的共同点。可观测量和承诺都是生产和消费数据的框架。它们遵循推送协议，这意味着生产者确定何时将数据发送给消费者。相比之下，在拉协议中，生产者只在消费者要求时才产生数据。

在承诺中，使用者是在构造承诺时传递给构造函数的解析回调，生产者是异步调用回调来传递数据的承诺本身。例如，在下面的代码片段中，一旦承诺(生产者)在一秒钟后完成，它就调用回调(消费者)并传递字符串“工作在承诺上完成”(数据)。

```
const executor = (resolve, reject) => {
  // perform some work
  console.log("Work started on Promise");
  setTimeout(() => resolve("Work is done on Promise."), 1000);
};

const aPromise = new Promise(executor);
aPromise.then(value => console.log(value));
```

在可观察对象中，生产者是被观察对象，观察者是消费者。

```
// Producer
const anObservable = new Observable(subscriber => {
  console.log("Work started on Observable");
  setTimeout(() => {
    subscriber.next("Work is done on Observable");
    subscriber.complete();
  }, 1000);
});
// Consumer
anObservable.subscribe(
  value => console.log(value)
);
```

虽然你可以使用一个可观察的或一个承诺来产生和接收数据，但是一个可观察的和一个承诺在设计和功能上是相当不同的，我们将在后面的章节中讨论。

# 多用途与单用途

可观察对象和承诺之间的第一个根本区别是*可观察对象可以发出多个值，而承诺只能发出一个值*。

在下面的代码片段中，观察者发出两个值，然后完成。

```
const anObservable = new Observable(subscriber => {
  console.log("Observable started");
  subscriber.next(1);
  subscriber.next(2);
  subscriber.complete();
});

anObservable.subscribe(
  value => console.log(value),
  err => console.log(err),
  () => console.log("Observable completed")
);
```

下面是控制台中的输出:

```
Observable started
1
2
Observable completed
```

与可观察值不同，承诺只能发出一个值，如下面的示例片段所示。

```
const aPromise = new Promise((resolve, reject) => {
  console.log("Promise started");
  resolve(1);
  resolve(2);
});
aPromise.then(value => console.log(value));
```

当 promise 第一次调用 resolve 回调时，消费者通过`then()`子句接收第一个值。但是，第二次调用没有效果，因为您可以看到输出中只显示了第一个值。

```
Promise started 
1
```

# 渴望 vs 懒惰

可观察的人和承诺之间的另一个区别是，可观察的人懒惰，而承诺的人渴望。

一个承诺是热切的，因为执行是立即开始的，不需要等待消费者。例如，在下面的代码片段中，在实例化承诺时，尽管没有消费者对承诺采取行动(通过调用`then()`子句), console.log()还是会执行。

```
const aPromise = new Promise((resolve, reject) => {
  // The line below gets executed right away
  console.log("Promise started");
  // The resolve() only gets call when the consumer wants the data (via the then() clause). 
  resolve(1);
});
```

下面是控制台的输出:`Promise started`

另一方面，一个可观察对象是懒惰的，因为直到第一个观察者订阅它，它才开始工作。

```
const anObservable = new Observable(subscriber => {
  // The line below does not get called until a subscriber subscribes to the observable. 
  console.log("Observable started");
  subscriber.next(1);
  subscriber.complete();
});

// anObservable.subscribe(value => console.log(value));
```

要查看输出，取消最后一行代码的注释以订阅可观察对象。

以下是输出:

```
Observable started 
1
```

# 可取消与不可取消。

下一个区别是*一个可观察的是可取消的，而一个承诺不是*。

因为一个承诺是急切的，而且只用于一次，所以它不支持取消，至少本机不支持。为了进行演示，请考虑下面的代码片段:

```
const aPromise = new Promise((resolve, reject) => {
  console.log("Promise started");
  var counter = 1;
  const intervalId = setInterval(() => {
    console.log(counter++);
    if (counter > 10) {
      clearInterval(intervalId);
      resolve("Promise finished");
    }
  }, 1000);
});
```

上面的代码模拟了一个需要 10 秒钟才能完成的长时间运行的操作，它每秒钟输出一次计数器变量的值。当执行正在运行时，如果调用者决定取消，调用者没有办法取消。承诺会一直执行，直到完成。你需要自己构建取消或者使用第三方库。

以下是输出:

```
Promise started
1
2
3
4
5
```

相反，可观察对象本身支持取消。让我们修改上面的例子来使用 Observables，看看我们如何在操作完成之前取消它。

```
import { interval, Observable, Subscription } from "rxjs";

const anObservable = new Observable(subscriber => {
  console.log("Observable started");
  var counter = 1;
  const intervalId = setInterval(() => {
    if (subscriber.closed) {
      console.log("Received cancellation");
      clearInterval(intervalId);
      subscriber.complete();
    }
    subscriber.next(counter++);
    if (counter > 10) {
      subscriber.complete();
    }
  }, 1000);
});
// subscribe to receive values from the observable.
const subscription: Subscription = anObservable.subscribe(value =>
  console.log(value)
);
// cancel the subscription after 2 seconds
setInterval(() => subscription.unsubscribe(), 2000);
```

在上面的代码片段中，消费者通过调用订阅的 unsubscribe()方法向生产者发出取消信号。如果您不熟悉订阅，它基本上公开了管理和清理与订阅 observables 相关的资源的方法。对于生产者，它可以通过检查变量`closed`来检查取消请求。以下是输出:

```
Observable started
1
2
Received cancellation
```

# 多播与单播

承诺和可观察对象之间的另一个区别是*可观察对象可以向多个观察者多播数据，而承诺只运行一次，并将数据返回给其唯一的消费者。*

为了进行演示，请考虑下面的代码片段:

```
const aPromise = new Promise((resolve, reject) => {
  console.log("Promise started");
  setTimeout(() => resolve(1), 1000);
  setTimeout(() => resolve(2), 2000);
});
aPromise.then(value => console.log(value));
aPromise.then(value => console.log(value));
```

第二次调用`then()`方法不会使承诺再次执行，因为承诺已经完成。事实上，一旦 promise 完成，它就会缓存结果并为所有后续的`then()`方法调用返回缓存的值，如下面的输出所示。

```
Promise started
1
1
```

现在让我们看一个例子，一个可观察对象是如何组播的，这意味着它们可以向多个用户广播数据。

```
const anObservable = new Observable(subscriber => {
  console.log('Observable started.');
  setTimeout(() => subscriber.next(1), 1000);
  setTimeout(() => subscriber.next(2), 2000);
});
// each call to subscribe() results in a new Subscription
const subscription: Subscription = anObservable.subscribe(value => console.log(value));
const anotherSubscription: Subscription = anObservable.subscribe(value => console.log(value));
```

在上面的代码片段中，每个 subscribe()调用返回一个新的订阅，我们可以从输出中看到 observables 运行两次，每个订阅一次，同时返回两个订阅者的数据。

```
Observable started.
Observable started.
1
1
2
2
```

# 同步和异步

最后，虽然承诺在本质上是异步的，但是可观察的可以是同步的也可以是异步的。

承诺是异步的，这意味着在实例化承诺时，执行不会立即发生，而是在下一个事件循环中发生。另一方面，一旦实例化并订阅了一个可观察对象，该可观察对象就会立即运行。这样，一个可观察的可以提供更好的性能。

正如下面的代码片段和输出所示，即使我们在可观察的结果之前声明了承诺，可观察的结果也在承诺的结果之前显示。

```
import { interval, Observable, Subscription } from "rxjs";

const aPromise = new Promise((resolve, reject) => {
  resolve('Hi there. I\'m a Promise ');
});
aPromise.then(value => console.log(value));
const anObservable = new Observable((subscriber) => {
  subscriber.next('Hi there. I\'m an Observable');
})
anObservable.subscribe(value => console.log(value));
```

以下是输出:

```
Hi there. I'm an Observable
Hi there. I'm a Promise
```

可观察对象也可以是异步的。例如，我们可以包装。next()调用 setTimeout()之类的异步函数，让 observables 异步发出值。

```
import { interval, Observable, Subscription } from "rxjs";

const aPromise = new Promise((resolve, reject) => {
  setTimeout(() => resolve('Hi there. I\'m a Promise '), 500);
});
aPromise.then(value => console.log(value));
const anObservable = new Observable((subscriber) => {
  setTimeout(() => subscriber.next('Hi there. I\'m an Observable'), 500);
})
anObservable.subscribe(value => console.log(value));
```

以下是输出:

```
Hi there. I'm a Promise 
Hi there. I'm an Observable
```

因为承诺和可观察对象都异步运行，并且因为我们首先声明承诺，所以来自承诺的输出在控制台中出现在来自可观察对象的输出之前，这是意料之中的。

# 结论

在这篇文章中，我展示了承诺和可观察之间的一些区别。两者都是生产和消费数据的伟大框架。然而，根据我的理解，一个可观察的人可以做承诺所能做的事情，甚至更多。此外，可观察的提供了比承诺更大的灵活性，因为它是多播和可取消的；默认情况下，这两个特征在承诺中都是缺失的。因此，你应该尽可能地用可观察的来代替承诺。

# 参考

[可观察的](https://rxjs-dev.firebaseapp.com/guide/observable)

[取消 JavaScript 中的承诺](https://javascript.plainenglish.io/canceling-promises-in-javascript-31f4b8524dcd)

[研讨会:RxJS 第 1 天的反应基础](https://www.pluralsight.com/courses/ng-conf-2020-session-36)

*原载于 2021 年 4 月 25 日 https://www.taithienbo.com**[*。*](https://www.taithienbo.com/differences-between-a-promise-and-an-observable/)*