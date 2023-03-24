# Swift 中的只读属性

> 原文：<https://levelup.gitconnected.com/read-only-properties-in-swift-c90faa6f999e>

什么是只读属性？如何用 Swift 编程语言创建一个？

![](img/e157f22a40eef6455d9ef96fae171b0f.png)

> 只读意味着我们可以访问属性的值，但不能给它赋值。

当我们向开发人员询问定义时，他们经常会混淆只读的**和**常量**！**

# 对只读属性的期望是什么？

让我们想象一个叫做加法的类，如下图所示

```
*class Addition {
  var lhs: Int = 0
  var rhs: Int = 0
  var sum: Int = 0
}*
```

当您试图从该类的实例中打印属性“sum”时，控制台应该总是打印“lhs”和“rhs”的值的总和。打印 sum 一次后，如果我们更改“lhs”或“rhs”或两者的值，并再次打印“sum”的值，它应该会打印“lhs”和“rhs”的更新值。

稍后，如果我们试图给“sum”赋值，比如

```
*var obj = Addition()
obj.lhs = 10
obj.rhs = 20
print(obj.sum)**obj.sum = 50 // compiler error here
print(obj.sum)*
```

这将导致编译器错误。

# 创建只读属性

只读属性**在 Swift 中也称为*计算属性*** 。包括 Swift 在内的大多数编程语言都有“Getters & Setters”的概念。

默认情况下，您在 Swift 中创建的任何普通变量都有一个 getter & setter。因为这是内部发生的，所以我们不必担心。有时，开发人员会想要亲自动手，为他们的变量定义一个定制的 getter & setter。

```
*var myValue: Int {
  get {
    return 10
  }
  set {
    save(newValue)
  }
}*
```

> ***在 swift 中，我们可以通过只为一个变量定义一个 getter 来创建一个只读属性。意思是没有二传手！***

```
v*ar sum: Int {
  get {
    return lhs + rhs
  }
}* 
```

由于变量只有一个 getter，所以当我们试图给“sum”赋值时，编译器会抛出一个错误。

借助 Swift 的新更新，您甚至可以减少像这样输入的行数:

```
*var sum: Int {
  lhs + rhs
}*
```

# 提问时间！

尝试在 Objective C 中创建一个**只读属性。如果你有答案，请回复！**

## 奖金！

# 什么是常数？

> **常量是程序在正常执行过程中不应改变的值。**

在 swift 中，我们可以通过使用“ **let** 关键字来创建一个常量值。例如:

```
*let aConstant = 10*
```

# 关于我

> **查:【https://about.me/chandrachudhk】*[](https://about.me/chandrachudhk)***

## **这是给你的甜甜圈！**

**![](img/ac51f8a13e63a8e24ae985c4f4543a7a.png)**