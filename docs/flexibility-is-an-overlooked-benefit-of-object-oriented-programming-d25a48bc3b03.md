# 灵活性是面向对象编程的一个被忽视的优点

> 原文：<https://levelup.gitconnected.com/flexibility-is-an-overlooked-benefit-of-object-oriented-programming-d25a48bc3b03>

![](img/e9b498f96632618c2e271762e6c706ea.png)

[亨利&公司](https://unsplash.com/@hngstrm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

即使许多杰出的程序员表达了他们对面向对象编程(OOP)的幻灭，它仍然是占主导地位的编程范例。

这是因为，尽管有缺陷和误用，OOP 提供了强大的功能和灵活性。明智地使用对象而不是原始数据类型可以极大地简化编程任务，并提高最终程序的可重用性。

虽然在当今使用最多的 OOP 语言之一 Java 中，`String`绝对不是一个原语数据类型，而是一个引用类型，但它在几乎每个 Java 程序中的广泛使用和 Java 编译器的特殊处理使它感觉像一个原语。

出于我们在这里讨论的目的，我们不妨将`String`视为一个原语，与`int`、`boolean`或`char`几乎没有什么不同(私有数组`char`保存了组成`String`文本的字符和/或代理)。

OOP 最现实的例子之一是工资程序，其中有`Person`、`Employee`和`Manager`类。然后`Employee`伸出`Person`，并且`Manager`伸出`Employee`。这确实感觉像一个玩具例子，不是吗？请和我在一起。

一个活着的人有家庭住址。至少在一个更好的世界里，每个想要房子的人都是如此，不管他们有没有工作。

雇员有家庭住址和工作地址。也许应该在`Person`中定义家庭地址，在`Employee`中定义工作地址。

但是如何表示这些地址呢？最明显的方法似乎是定义一系列私有的`int`和`String`字段。

长话短说，让我们假设您有一些东西(比如 Lombok)可以接受`@Getter`和`@Setter`注释，并为您编写合适的 getters 和 setters。

```
public class Person { // TODO: Name fields with getters and setters // TODO: SSN field with getter and setter @Getter @Setter private int homeHouseNumber; @Getter @Setter private String homeStreetName; @Getter @Setter private int homeApartmentNumber;
                    // May be -1 if not applicable @Getter @Setter private String homeCity; @Getter @Setter private char homeStateAbbrev = new char[2]; @Getter @Setter private int homeZIPCode; // TODO: Other necessary fields with getters and setters // TODO: Constructor}
```

同样，在`Employee`中，我们将雇员的工作地址字段，以及相关的 getter 和 setter，或者 getter 和 setter 注释。

关于 1 的注释意味着公寓号不适用于该地址，这似乎是一个有用的注释，但它太容易被忽略了。你团队中的其他人可能会认为 0 表示公寓号不适用，甚至你可能会搞混或完全忘记。

这整个东西非常脆弱。假设我们正在为密歇根州的一家公司做这件事，这家公司有一些住在加拿大的加拿大雇员。至少有一个字段具有不同的验证要求。

例如，一个在底特律工作但住在温莎的雇员可能有一个邮政编码为 N8N 0A3 的家庭地址。我不知道那实际上是否对应于一个住宅区，但它不符合`homeZIPCode`字段，反正它是名不副实的。

也许我们应该把`homeZIPCode`改成`String`类型，并重新命名为`homePostalCode`。但是如果我们为邮政编码编写任何验证，对`String`的更改将会破坏这种验证。

我们必须重写邮政编码验证，并添加加拿大邮政编码验证。但是……这听起来是不是开始违反单一责任原则了?“S”来自某个著名的首字母缩写词？

在任何情况下，`Employee`类从一开始就应该只有一个职责:收集雇员的信息。邮政编码验证属于邮寄地址类，或者邮政编码类。

此外，如果我们同时在`Person`和`Employee`中编写邮政编码和加拿大邮政编码验证，我们就复制了应该只存在于一个地方的行。那是“代码复制”，不是什么好事。

最明显的答案应该是，如果我们在 Java 开发工具包(JDK)或可信的第三方库中找不到什么，我们需要编写一个单独的类来保存邮件地址。

我们可以在`Person`中这样做:

```
 private MailingAddress addressHome = null; public MailingAddress getHomeAddress() {
        return this.addressHome;
    } public void setHomeAddress(MailingAddress address) {
        this.addressHome = address;
    }
```

和`Employee`中工作地址类似的东西。对我来说，为单个字段编写 getters 和 setters 并不像是一项艰巨的任务。并且您的集成开发环境，即使没有 Lombok，也可能为您处理 getters 和 setters。

也许应该对`Person`和`Employee`中的地址进行一些验证，这是特定于那些类的，比如家庭地址与工作地址不同。

但是更一般的验证，比如确保门牌号不是负数，这类事情属于`MailingAddress`。

在我们关于`Person`和`Employee`的工作中，我们可能不需要知道`MailingAddress`的细节。

也许在测试类中，我们可能需要构造一个适当的`MailingAddress`实例，但是就编写 setter 而言，我们也许可以依靠调用者来提供一个适当构造的`MailingAddress`实例。

通过将`MailingAddress`分离到一个单独的类中，我们不仅在这个项目中，而且在我们可能需要处理邮件地址的任何其他项目中都可以重用它。

面向对象设计当然有可能被冲昏头脑，为了对象本身而创建对象。一个明显的迹象是，当一个项目有接口或抽象类，每个只有一个实现类，没有人能给出一个好的理由。

在邮寄地址的例子中，创建一个抽象类来表示邮政编码可能是有意义的。为公寓号码创建一个抽象类几乎肯定是多余的。

对相反问题的主要诊断，一个接口和抽象类太少的项目，我认为，是对原语和`String`值的过度依赖。

如果你的项目不是这样，我不会担心接口和抽象类太少。如果需要，一个设计良好的类可以很容易地被重构来实现一个接口或扩展一个抽象类。

在 JDK，一个很好的例子就是`String`级。据我所知，`String`的最初版本没有实现任何接口，是`Object`的直接子类。

但由于 Java 1.4 至少到 Java 8，`String`实现了三种不同的接口:`CharSequence`、`Comparable<String>`和`Serializable`。其中的第一个，`CharSequence`，是最近的，也是一个最好的例子，说明了一个设计良好的类是如何足够灵活地将它的一些单元推广到一个接口或抽象类。

实际上`String`设计得非常好，以至于许多程序员甚至在手头有更具体的类可用的情况下也使用它。

也许 OOP 的问题不在于它是一个有缺陷的想法，而在于它经常被误用。如果应用得好，OOP 是非常灵活的。