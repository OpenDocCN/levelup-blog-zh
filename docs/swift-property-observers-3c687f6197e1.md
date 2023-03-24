# Swift|物业观察员

> 原文：<https://levelup.gitconnected.com/swift-property-observers-3c687f6197e1>

## 属性第 3 部分

![](img/a127763bb1493fa6d2653980b7650bc0.png)

弗雷德里克·马歇尔在 [Unsplash](https://unsplash.com/s/photos/observe?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

属性观察者观察属性值的变化并对其做出响应。可以添加属性观察器来定义除惰性存储属性之外的存储属性。

我们可以为属性定义以下观察者:

*   willSet =在保存值之前调用
*   didSet =保存新值后立即调用

## 威尔塞特

如果设置了 willSet 观察器，新属性的值将作为常量参数传递。作为 willSet 实现的一部分，您可以为此常量参数指定一个名称。如果不选择设置参数名，可以使用默认参数 **newValue** 。

## didSet

类似地，如果您是 didSet 观察者，则传递一个包含先前属性值的常量参数。可以给参数命名，也可以使用默认的参数名 **oldValue** 。当您在自己设置的观察器中为属性赋值时，您分配的新值将被您刚刚设置的值替换。

# **例子**

当试图只通过描述来理解属性观察者的概念时，可能会有点混乱，所以这里有一个例子。

让我们初始化，看看会发生什么

```
var person = Person(name: "Elizabeth",age: 20)
```

什么都没发生。首次初始化实例时，不会调用属性观察器。

现在，让我们将年龄更改为一个更大的值，并调用 willSet 和 didSet

```
person.age = 120//age will be set to 120
//100 years has been added
```

如前所述，willSet 在我们设置一个值之前被调用。所以它看起来像下面这样

```
willSet → person.age = 20 → didSet 
```

现在，如果我们将 age 改为一个更小的值，didSet 将不会被调用，因为它不满足条件

```
person.age = 0
//age will be set to 0
```

# 新值和旧值关键字

newValue 用作 willSet 的参数，oldValue 用作 didSet 的参数。如果你不选择写参数，你可以用这些关键字来指示它们

这就是本节所观察到的全部性质。谢谢你阅读我的博客。如果你有任何问题，请让我知道。