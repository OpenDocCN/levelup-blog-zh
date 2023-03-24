# RxJS 反应式编程简介

> 原文：<https://levelup.gitconnected.com/introduction-to-reactive-programming-with-rxjs-114fa5e57eb1>

![](img/096f8325eb05238b1721d915bfa2935d.png)

照片由 [Yolanda Sun](https://unsplash.com/@iyolanda?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

RxJS 是一个反应式编程库，允许我们实现观察者模式。这种模式使我们能够观察程序中的变化，并相应地运行代码。可观测量是当它识别变化时发出值的实体。观察器是接收观察器发送的数据的实体。

在本文中，我们将快速了解如何创建新的可观察对象，以便向观察者发送新的值。

# RxJS 的组件

RxJS 有几个部分。它们是:

*   observables——获取变更并将其发送给观察者的实体。
*   观察者——观察从可观察对象推送的新数据的实体。
*   操作符——通过使用类似于`map`、`filter`、`concat`、`reduce`等操作来处理集合的函数。
*   主题—向多个观察者广播数据的事件发射器
*   调度程序—控制并发性的集中式调度程序。当计算发生时，它们让我们协调。

# 创造可观的

我们需要可观测的东西来向观察者发送数据。有了 RxJS，我们有了一个`Observable`构造函数，让我们可以向订阅者发出数据。

例如，我们可以写:

```
const observable = new Rx.Observable(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  setTimeout(() => {
    subscriber.next(3);
    subscriber.complete();
  }, 1000);
});
```

上面的`observable`会随着订户立即发出 1，2，1 秒后发出 3。`subscriber`是我们用来向观察者发送数据的订阅者。

`complete`阻止被观察方使用订阅方发出更多数据。

我们可以使用`observable`获得如下发射值:

```
observable.subscribe(val => console.log(val));
```

此外，我们可以将一个对象传递给`subscribe`方法，用`next`方法获取发出的值，`error`方法获取错误，用`complete` 方法在可观察对象发送完数据后运行一些东西:

```
observable.subscribe({
  next(x) {
    console.log(x);
  },
  error(err) {
    console.error(err);
  },
  complete() {
    console.log("done");
  }
});
```

# 拉与推

可观察对象是推送系统，数据从一个数据源发送到观察者。

它是多重价值的生产者。只有当观察者获得新值时，才进行评估。

# 可观测量就像函数一样

观察值与函数相似，都返回其他实体的数据。我们可以在任何我们希望的地方使用返回的数据。

例如，如果我们有以下函数:

```
const foo = () => 1
```

和一个可观察的:

```
const foo = new Observable(subscriber => {  
  subscriber.next(1);
});

foo.subscribe(x => {
  console.log(x);
});
```

那么它们都给我们值 1。除了我们之前看到的，我们还可以用可观测值得到多个值，这是函数做不到的。

同样，它们可以是同步或异步的函数。从第一个例子，我们有:

```
subscriber.next(1);
subscriber.next(2);
```

逐行运行，同时:

```
setTimeout(() => {
  subscriber.next(3);
  subscriber.complete();
}, 1000);
```

运行前等待 1 秒，这意味着它是异步的。

![](img/cc2f335d60044f1e947e3a7444aeea3b.png)

照片由[🇸🇮·扬科·菲利- @specialdaddy](https://unsplash.com/@thepootphotographer?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 可观察的部分

Observables 是用`Obseravable`构造函数创建的，我们可以用 observer 订阅它。

它向观察者发送`next`、`error`或`complete`通知。可观的东西一旦收到就会自动处理掉。

## 创造一个可观察的

`Obserable`构造函数接受一个带有`subscriber`参数的回调函数，该函数让我们的订户对象发出值。

例如，我们可以写:

```
const observable = new Observable((subscriber) => {
  const id = setInterval(() => {
    subscriber.next("foo");
  }, 1000);
});
```

每秒钟向观察者发出`'foo'`。

## 订阅 Observables

我们调用可观察对象的`subscribe`方法来订阅它推送的值。例如，我们可以写:

```
observable.subscribe(x => console.log(x));
```

获取最新的值。

同一可观察对象的多个观察者不会共享`subscribe`呼叫。每次调用 subscribe 都会创建自己的观察器来观察值。

## 执行观察值

代码:

```
const observable = new Observable((subscriber) => {
  //...
});
```

执行可观察的。它只发生在订阅的每个可观察对象上。

它可以提供以下价值:

*   下一步—向观察者发送值
*   错误—向观察者发送错误或异常
*   完成—不发送任何内容

Observables 严格遵守可观察契约，所以一旦调用了`complete`，它就不会发送更多的数据。

例如:

```
const observable = new Observable(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  subscriber.complete();
  subscriber.next(4);
});
```

4 不会被发送到 observables，因为`complete`已经被调用。

## 处理可观察的执行

我们可以调用`unsubscribe`来停止订阅可观察值。它会在被调用后停止观察更多的变化，并释放观察所需的资源。

我们可以返回一个`unsubscribe`函数来清理我们想要添加的代码。例如，我们可以写:

```
const observable = new Observable(function subscribe(subscriber) {
  const intervalId = setInterval(() => {
    subscriber.next("hi");
  }, 1000); return function unsubscribe() {
    clearInterval(intervalId);
  };
});const subscription = observable.subscribe(x => console.log(x));setTimeout(() => {
  subscription.unsubscribe();
}, 3000);
```

上面的代码有一个`unsubscribe`函数，用`setInterval`返回的 ID 调用`clearInterval`。然后我们调用`subscribe`来订阅我们的 observable，它用`unsubscribe`方法返回一个对象。该方法在`setTimeout`回调 3 秒后被调用。

使用 Rxjs，我们可以创建 Observables 来用`subscriber`对象发出值。然后我们可以订阅 observables 来观察值。

我们还可以创建一个函数，在取消订阅时进行清理，然后当我们不想再订阅一个观察对象时调用`unsubscribe`。