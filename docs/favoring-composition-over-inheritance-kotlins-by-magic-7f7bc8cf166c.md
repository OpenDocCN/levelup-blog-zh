# 重创作轻继承:科特林的“by”魔术

> 原文：<https://levelup.gitconnected.com/favoring-composition-over-inheritance-kotlins-by-magic-7f7bc8cf166c>

## 关于科特林代表的详尽指南

继承之上的复合是面向对象编程的一个重要设计原则。这使得代码更具可重用性和可维护性。这就是人们常说的 OOP 原则，比如在很有影响力的书 [***设计模式:可重用面向对象软件的元素***](https://en.wikipedia.org/wiki/Design_Patterns) ***。***

让我们看看维基百科是怎么说的:

> 组合优先于继承的原则是，类应该通过它们的组合(通过包含实现所需功能的其他类的实例)而不是从基类或父类继承来实现多态行为和代码重用。—维基百科

![](img/599ad8d6fa1b0f35b555fa1b14339d38.png)

由 [Vardan Papikyan](https://unsplash.com/@varpap?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将看到如何利用 Kotlin 委托来实现合成

## 遗传有什么问题？

继承是一个强大的特性，但是它被设计用来创建一个具有“是”关系的对象层次结构。当这种关系不明确时，继承可能会有问题，应该谨慎执行。以下是继承实现的常见问题:

1.  继承在父类和它的子类之间创建了一个强关系，并产生了紧密耦合的代码。继承一个类将子类与父类的实现细节联系起来，因此当父类中的代码发生变化时，所有的子类可能都需要更新。
2.  使用一个父类来保存所有的公共代码片段违反了单一责任原则，最终导致代码混乱
3.  当类是封闭的或最终的时，实现继承来扩展类的功能是不可能的

当我们需要的只是简单的代码提取或重用时，应该谨慎使用继承；相反，我们应该更喜欢一个更轻松的选择:课堂作文。

## 实现组合

通过委托，可以使组合像继承一样对代码重用具有强大的功能。委托本质上意味着将请求转移或转发给相关的委托对象。委托下的两个对象处理一个请求:接收对象将任务委托给它的委托人，然后委托人处理请求。

*委托模式*是一种流行的设计模式，其中父对象将请求传递给子对象*委托对象*。这提供了通过继承类似地实现的代码重用，同时还实施了“单一责任原则”,该原则允许父节点保持对请求如何执行的不可知。

[委托模式](https://en.wikipedia.org/wiki/Delegation_pattern)已经被证明是实现继承的一个很好的替代方案，Kotlin 本身就支持它，不需要任何样板代码。

## 什么是科特林代表团？

Kotlin 添加了一个名为`“by”`的新关键字来支持“委托”设计模式。可用于**财产委托**或**委托**执行。

`“by”`既可以用于委托接口实现，也可以用于属性委托接口实现。我们将在下面看看这两个。

## 委托接口实现

看看下面的代码:

```
interface BaseCar {
    fun color()
    fun maxSpeed()
}

class BaseCarImpl(val color: String) : BaseCar {
    override fun color() { print(color) }
    override fun maxSpeed() { print("250") }
}

class Derived(b: BaseCar) : BaseCar by b

fun main() {
    val b = BaseCarImpl("Green")
    Derived(b).color()
}
```

在上面的例子中，类`Derived`实现了接口`BaseCar`，但是它不需要覆盖接口的任何方法。`Derived`类只是将传入的请求委托给实际的实现，在本例中是`BaseCarImpl`。如果我们看到上面代码的输出，我们会在控制台中看到“绿色”字样

我们可以通过覆盖`Derived`类中所需的方法来进一步定制行为。检查下面的代码:

```
class Derived(b: BaseCar) : BaseCar by b{
    override fun color() { print("Red") }
}

fun main() {
    val b = BaseCarImpl("Green")
    Derived(b).color()
}
```

在这种情况下，我们将“红色”作为控制台中的输出。`Derived`类不会将请求委托给委托对象，因为我们已经覆盖了`Derived`类中方法`color()`的行为。

这个委托实现在 Android 的`BaseActivity`类中特别有用，该类通常是为了保存公共代码而创建的。仅仅使用`BaseActivity`来保存公共的代码片段违反了单一责任原则，而且我们最终会暴露父类的 API，而所有的子类可能都不需要这些 API。

看看一个更现实的例子:

假设我们为我们的产品定义了两种类型的用户层:免费层**和付费层**。因此，为了在应用程序端处理这个问题，我们可以有一个名为`Tier`的接口，以及付费和免费层的相应实现:`FreeTierImpl`和`PaidTierImpl`。我们最终的代码看起来会像这样:****

```
interface Tier {
    fun getLicenseType() : Int
    fun numberOfFeatureAvailable() : Int
}

class FreeTierImpl() : Tier {
    override fun getLicenseType() : Int { return 1 }
    override fun numberOfFeatureAvailable() : Int { return 2 }
}

class PaidTierImpl() : Tier {
    override fun getLicenseType() : Int { return 2 }
    override fun numberOfFeatureAvailable() : Int { return 4 }
}

class CompositeService(val tier: Tier, val messagingServ: MessagingService) : Tier by tier
, MessagingService by messagingServ{

}

fun main() {
    val service = CompositeService(PaidTierImpl(), UserMessagingSErvice())
    service.numberOfFeatureAvailable()
}
```

**`CompositeService`仅聚合两个服务层和 MessagingService，但它不知道为计算功能数量的许可证而实施的业务逻辑。`CompositeService`类的构造函数中的参数可以通过一些 DI 逻辑注入**

**在下一节中，我们将研究**属性委托****

## **财产委托**

**对于一些常见类型的属性，即使我们可以在每次需要时手动实现它们，但更有帮助的是实现一次，将它们添加到库中，并在以后重用它们。我们可以创建自己的自定义属性委托，但为了本文的简洁，我们将研究 Kotlin 提供的一些现成的标准委托。Kotlin 标准库为几种有用的委托提供了工厂方法。**

## **1.懒惰的财产**

**`lazy()`是一个函数，接受一个 lambda 并返回一个`Lazy<T>`的实例，它可以作为实现一个 lazy 属性的委托。对`get()`的第一次调用执行传递给`lazy()`的 lambda 并记住结果。随后对`get()`的调用只是返回记忆的结果。这对于那些计算起来很昂贵并且我们可能永远都不需要的属性来说是很有用的。查看下面的示例:**

```
class UserDb(userId: String) {
    val name: String by lazy {
        queryForValue("SELECT name FROM users_table WHERE id = :id", mapOf("id" to userId)
    }
}
```

## **2.Delegates.observables()**

**`Delegates.observable()`接受两个参数:初始值和修改处理程序。**

**每当我们改变属性时(在赋值被执行后*，处理程序被调用。Lambda 有三个参数:被赋值的属性、旧值和新值。***

```
class ObservedProperty {
    var name: String by Delegates.observable("Initia value") {
        prop, old, new -> println("Old value: $old, New value: $new")
    }
}
```

## **3.在地图中存储属性**

**一个常见的用例是在地图上保存属性值。这通常发生在解析 JSON 或执行其他动态操作的应用程序中。这里，map 实例本身可以充当委托属性的委托。请看下面的代码示例:**

```
class DelegateMapExample(map: MutableMap<String, Any?>) {
    var name: String by map
    var license: Int by map
}

fun main() {
    val data = DelegateMapExample(mapOf(
        "name" to "USP",
        "license" to 4
    ))
    println(data.name)
}
```

**代码的输出将是“USP”。委派属性使用与属性名称相关联的字符串键从该映射中获取值。**

## **最后的想法**

**属性委托和委托实现是 Kotlin 提供的强大特性。我希望这篇文章已经激发了你去利用它们。**

**这就把我们带到了文章的结尾。我希望你觉得这东西有用。既然您已经阅读了这篇文章，请点击“鼓掌”按钮，继续阅读更多这样的文章**

## **参考资料:**

**[https://kotlinlang.org/docs/delegation.html](https://kotlinlang.org/docs/delegation.html)**

**https://kotlinlang.org/docs/delegated-properties.html**