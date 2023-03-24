# Swift |类型属性

> 原文：<https://levelup.gitconnected.com/swift-type-properties-7acbe44eaf5b>

## 属性第 4 部分

![](img/5bc08278045158a3c1213f254ece505b.png)

蒂埃拉·马洛卡在 [Unsplash](https://unsplash.com/s/photos/properties?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

到目前为止，我们所讨论的属性，比如存储属性、惰性属性和计算属性，都是属于特定实例的属性。但是类型属性属于类型本身。

不管是否创建了实例，都会设置 type 属性的值。类型属性对于定义特定类型的所有实例的公共值非常有用。

**存储类型属性**可以声明为常量或变量

**计算类型属性**只能声明为变量

必须设置初始值，并执行惰性操作。这是因为在初始化时，类型本身没有初始值设定项来为存储的类型属性赋值。但是，您不必指定 lazy 关键字，因为它会自动为您完成。

# 类型属性

您可以使用**静态**关键字来定义类型属性。

结构 someStructure 包含两个类型变量，storedTypeProperty 和 computedTypeProperty。正如所提到的，storedTypeProperty 是存储类型属性，computedTypeProperty 是只有一个 getter 的复合类型属性。存储类型属性有预定义的值，而计算类型属性没有。因此，计算类型属性应该始终声明为变量。

# 在类中重写计算类型属性

类中的计算属性可能会有点混乱，因为类及其属性可能会被重写。在计算属性的情况下，子类可以通过使用 class 关键字覆盖超类的实现。

让我们尝试覆盖计算类型属性。

# 类型属性示例

马上，您需要创建 myClass 的一个实例来操作存储的属性“value”。

```
var classOne = myClass()classOne.value = 100print(classOne.value) // 100 print(myClass.storedTypeValue) //110print(myClass.computedTypeValue) //110
```

要更改类型变量的值，可以执行下列操作之一

```
myClass.storedTypeValue = 200 or myClass.computedTypeValue = 200print(classOne.value) // 100print(myClass.storedTypeValue) //200print(myClass.computedTypeValue) //200
```

这就是我所有的存储属性。感谢你阅读这篇文章！如果你在 yu24c@mtholyoke.edu 有任何问题，请告诉我