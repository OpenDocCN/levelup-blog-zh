# 什么是 RxJS 订阅？

> 原文：<https://levelup.gitconnected.com/what-are-rxjs-subscriptions-65f50394f190>

![](img/7e1e760c8bd3bc7249fcdbf060883d87.png)

米哈伊尔·瓦西里耶夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

RxJS 中的订阅是一种可任意使用的资源，通常表示可观察对象的执行。它有一个`unsubscribe`方法，让我们在完成后处理订阅所拥有的资源。

在 RxJS 的早期版本中，它也被称为“一次性的”。

# 基本用法

在下面的代码中可以看到一个订阅的基本示例:

```
import { of } from "rxjs";const observable = of(1, 2, 3);
const subscription = observable.subscribe(val => console.log(val));
subscription.unsubscribe();
```

在上面的代码中，当我们对一个可观察对象调用`subscribe`时，返回的`subscription`是一个`subscription`。它有`unsubscribe`方法，我们在最后一行调用该方法，以便在可观察对象被取消订阅时进行清理。

# 合并订阅

我们可以将订阅与订阅对象附带的`add`方法结合起来。

例如，如果我们有两个可观测量:

```
import { of } from "rxjs";const observable1 = of(1, 2, 3);
const observable2 = of(4, 5, 6);const subscription1 = observable1.subscribe(val => console.log(val));const subscription2 = observable2.subscribe(val => console.log(val));subscription1.add(subscription2);
```

在上面的代码中，我们有两个订阅，`subscription1`和`subscription2`，我们用`subscription1`的`add`方法将它们连接在一起。

`subscription1`是`subscription2`的父级。

我们应该

```
1
2
3
4
5
6
```

作为输出。

当我们一起加入订阅时，我们可以通过在第一个调用`add`方法的订阅上调用`unsubscribe`来取消订阅所有加入的订阅。

例如，如果我们有:

```
import { interval } from "rxjs";const observable1 = interval(400);
const observable2 = interval(300);const subscription = observable1.subscribe(x => console.log(x));
const childSubscription = observable2.subscribe(x => console.log(x));subscription.add(childSubscription);
```

然后在`subscription`上调用`unsubscribe`将取消订阅所有已加入的订阅。

```
setTimeout(() => {
  subscription.unsubscribe();
}, 1000);
```

![](img/d8471f4b6617d2b24340a0d706d8decd.png)

[米 PHAM](https://unsplash.com/@phammi?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 撤消子订阅

我们可以通过使用订阅的`remove`方法撤销子订阅。它将子订阅作为参数。

要使用它，我们可以编写类似下面的代码:

```
import { interval } from "rxjs";const observable1 = interval(400);
const observable2 = interval(300);const subscription = observable1.subscribe(x => console.log(x));
const childSubscription = observable2.subscribe(x => console.log(x));subscription.add(childSubscription);(async () => {
  await new Promise(resolve => {
    setTimeout(() => {
      subscription.remove(childSubscription);
      resolve();
    }, 600);
  }); await new Promise(resolve => {
    setTimeout(() => {
      subscription.unsubscribe();
      childSubscription.unsubscribe();
      resolve();
    }, 1200);
  });
})();
```

一旦`childSubscription`被删除，它就不再是`subscription`订阅的子订阅。

因此，我们必须对两个订阅分别调用`unsubscribe`，这样一旦完成，我们就可以清除两个订阅。

订阅让我们获得从可观察对象发出的值。我们可以用`add`方法将多个订阅连接在一起，该方法将一个子订阅作为其参数。

当它们合并在一起时，我们可以一起取消订阅。

我们可以用`remove`方法删除作为另一个订阅的子订阅。