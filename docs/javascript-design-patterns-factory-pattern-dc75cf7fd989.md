# JavaScript 设计模式—工厂模式

> 原文：<https://levelup.gitconnected.com/javascript-design-patterns-factory-pattern-dc75cf7fd989>

![](img/c32f265c26f252bcedf0c1949c18cf2d.png)

莱尼·屈尼在 Unsplash 上的照片

设计模式是任何好软件的基础。JavaScript 程序也不例外。

在本文中，我们将研究工厂和观察者模式。

# 工厂模式

工厂模式是一种设计模式，它允许我们创建新的对象，并以一种干净的方式返回它们。

例如，我们可能想要返回相似的不同种类的东西。

为了方便起见，我们可以创建一个工厂函数，让我们用一个工厂函数返回不同种类的对象。

我们可以写:

```
class Fruit {
  //...
}class Apple extends Fruit {
  //...
}class Orange extends Fruit {
  //...
}const fruitFactory = (type) => {
  if (type === 'apple') {
    return new Apple()
  } else if (type === 'orange') {
    return new Apple()
  }
}
```

我们有`fruitFactory`工厂函数，它获取一个对象的`type`作为参数，然后根据类型返回一个对象。

如果我们有`'apple'`作为`type`的值，我们返回一个`Apple`实例。

如果我们将`type`设置为`'orange'`，我们将返回一个`Orange`实例。

现在我们可以返回`Fruit`的不同子类实例，而不用显式地写出它们。

如果我们对类做了任何更改，或者添加或删除了它们，那么我们仍然可以使用相同的工厂函数，不会破坏任何东西。

JavaScript 中的一些工厂函数在标准库中。

它们包括用于创建字符串的`String`，用于从其他实体创建数字的`Number`，用于将变量转换为布尔值的`Boolean`，用于创建数组的`Array`，等等。

现在我们不必担心类结构的改变会弄乱我们的代码，因为我们使用了一个工厂函数来创建我们的`Fruit`对象。

# 观察者

观察者模式是指多个对象监听一个可观察的对象。

这样，可观察对象(也称为主体)可以用发出的数据通知订阅可观察对象的对象。

例如，我们可以写:

```
const observable = {
  observers: {},
  subscribe(obj) {
    const id = Object.keys(this.observers).length + 1;
    this.observers[id] = obj;
    return id;
  }, notify(data) {
    for (const o of Object.keys(this.observers)) {
      this.observers[o].listen(data);
    }
  }
}const observer = {
  listen(data) {
    console.log(data);
  }
}
observable.subscribe(observer);
observable.notify({
  a: 1
});
```

我们有`observable`对象，它让观察者对象(有一个`listen`方法来监听数据)订阅来自`observable`对象的通知。

当我们可以像上面那样`observable.notify`处理某些东西时，observer 对象的`listen`方法就会运行。

这样，交流完全是通过`notify`方法来完成的，而不是其他地方。

没有公开实现，因此没有紧密耦合。

只要方法做同样的事情，我们就不必担心破坏订阅`observable`的观察者对象。

我们还可以给`observable`添加一个`unsubscribe`方法，它使用从`subscribe`返回的`id`从`observers`对象中移除一个观察者对象。

例如，我们可以写:

```
const observable = {
  observers: {},
  subscribe(obj) {
    const id = Object.keys(this.observers).length + 1;
    this.observers[id] = obj;
    return id;
  }, notify(data) {
    for (const o of Object.keys(this.observers)) {
      this.observers[o].listen(data);
    }
  }, unsubscribe(id) {
    delete this.observers[id];
  }
}const observer = {
  listen(data) {
    console.log(data);
  }
}
const subscriberId = observable.subscribe(observer);
observable.notify({
  a: 1
});observable.unsubscribe(subscriberId);
```

我们添加了一个`unsubscribe`方法，这样我们就可以从`this.observers`列表中删除观察者对象。

一旦我们取消订阅，如果我们再次调用`notify`,我们将不再收到取消订阅的观察者的通知。

观察者模式的例子在很多地方都有使用。

observer 模式的另一个好处是我们不通过类进行任何交流。

松耦合应该总是优于紧耦合。

在上面的例子中，我们只通过一个通道进行通信，所以观察者模式是耦合所能达到的最松散的模式。

它们包括消息队列和 GUI 事件(如鼠标点击和按键)的事件处理程序。

![](img/8ec65949399e241e2718bac4c771a697.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以使用观察者模式来尽可能地分离对象。

我们所做的就是从一个我们想要改变的可观察物体接收事件。

可观察对象向观察对象发送通知。

工厂模式允许我们通过创建工厂函数在一个地方创建相似类型的对象。