# 您应该了解的 Java 15 的令人兴奋的新特性

> 原文：<https://levelup.gitconnected.com/new-and-exciting-features-of-java-15-you-should-know-about-d23c1c175176>

## Java 15 中新特性的指南及代码示例

![](img/c0f9055ee0b7f5d141dbd1eb73a0bcdb.png)

由[迈克·肯尼利](https://unsplash.com/@asthetik?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Java 在最近发布的 Java 15 中引入了一系列新特性。在本文中，我将讨论 Java 15 新引入的产品特性和一些我认为最有趣的预览特性。我们将研究的特性列表是文本块、记录、基于 EdDSA 的加密签名、隐藏类、instanceof 操作符的模式匹配和 Z 垃圾收集器。所以让我们开始吧。

# **文本块**

文本块是一个多行文字字符串，它不需要大多数转义序列，如换行符和引号。文本块以三个双引号开始。

```
String example = """ This is a Text Block """;
```

今天，文本块的主要用途是当开发人员想要一个包含 HTML、XML、JSON 或 SQL 片段的字符串值时。如果我们没有文本块，我们必须使用转义和连接来实现。文本块最初是作为 JDK 13 的预览功能引入的，现在在 JDK 15 中作为产品功能提供。

# 记录(第二次预览)

Java 记录是一种特殊类型的 Java 类，用于保存不可变的数据。记录在某些方面类似于 Java 枚举。所有记录的基类是`java.lang.Record`，就像`java.lang.Enum`是所有枚举的基类一样。下面是一个简单记录的例子。

下面是一个简单记录的例子。

```
record Person (String firstName, String lastName) {}
```

当在没有任何构造函数声明的情况下编译记录时，它会自动获得一个规范的构造函数，该构造函数会将所有隐式声明的私有字段分配给实例化该记录的新表达式的相应参数。这意味着当上面的记录被编译时，它将看起来像下面的代码段。

```
record Person(String firstName, String lastName) { 
    // Implicitly declared fields
    private final String firstName;
    private final String lastName;

    // Implicitly declared canonical constructor
    Person(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
}
```

Java Record 的实例对于保存从数据库查询返回的记录、从远程服务调用返回的记录、从 CSV 文件读取的记录或类似的用例非常有用。

# 基于 EdDSA 的加密签名

EdDSA 是一个现代的椭圆曲线方案，比目前 JDK 的签名方案有更多的好处。数字签名允许我们验证签名的作者、日期和时间，并验证消息的内容。与其他签名方案相比，EdDSA 具有更高的安全性和性能。

下面是一个使用 EdDSA 生成密钥对和签名的示例。

```
KeyPairGenerator kpg = KeyPairGenerator.getInstance("Ed25519");
KeyPair kp = kpg.generateKeyPair();
Signature sig = Signature.getInstance("Ed25519");
sig.initSign(kp.getPrivate());
sig.update(msg);
```

# 隐藏类

隐藏类是不能被其他类的字节码直接使用的类。它们旨在由运行时生成类的框架通过反射来使用。调用`ClassLoader::defineClass`会创建一个普通类，而调用`Lookup::defineHiddenClass`会创建一个隐藏类。JVM 将从提供的字节中生成一个隐藏类，链接该隐藏类，并返回一个查找对象，该对象提供对隐藏类的反射访问。

您可以通过在返回的查找对象上调用`Lookup::lookupClass`来获得隐藏类的类对象。除了四个限制之外，隐藏类可以从类对象实例化，其成员可以像普通类一样被访问。这四种限制如下:

*   `Class::getName`返回不是二进制名称的字符串
*   `Class::getCanonicalName`返回空值
*   隐藏类中声明的 Final 字段是不可修改的
*   该类对象不可由检测代理修改

# **(第二次预览)**实例的模式匹配

Java 15 中的`instanceof`操作符通过模式匹配得到了增强。为了理解这个特性是什么，让我们将它与操作符功能的原始实例进行比较。让我们看看下面传统的`instanceOf`操作符的示例代码。

```
if (vehicle instanceof Car) {
    Car car = (Car) vehicle;
    car.drive();
} else if (vehicle instanceof Bike) {
    Bike bike = (Bike) vehicle;
    bike.drive();
}
```

让我们看看如何为`instanceof`操作符编写相同的带有模式匹配的代码。

```
if (vehicle instanceof Car car) {
    car.drive();
} else if(vehicle instanceof Bike bike) {
    bike.drive();
}
```

从上面的例子中，您可以看到，使用模式匹配，我们可以避免样板代码，在样板代码中，我们用来检查对象的实例，并为相同的对象创建一个实例，并使用它进行进一步的操作。

# z 垃圾收集器(ZGC)

ZGC 是一个可伸缩的低延迟垃圾收集器，最初是作为 Java 11 中的一个实验性特性引入的。如今，应用程序同时为成千上万的用户提供服务并不罕见，对于这样的应用程序，需要大量的内存。然而，管理所有这些内存很容易影响应用程序的性能。ZGC 是作为这个问题的解决方案引入的，随着 Java 15 的推出，它现在已经成为一个产品特性。

本文到此为止。我希望你学到了新东西。如果你想在 Java 面试中脱颖而出，可以看看我写的关于棘手的 Java 面试问题的文章。

[](/tricky-java-interview-questions-cfc546fd03ab) [## 棘手的 Java 面试问题

### 你知道答案吗？

levelup.gitconnected.com](/tricky-java-interview-questions-cfc546fd03ab) 

感谢您的阅读和快乐编码！

# 参考

*   [打开 JDK](https://openjdk.java.net/projects/jdk/15/)
*   [JDK 15 版本说明](https://www.oracle.com/java/technologies/javase/15-relnote-issues.html)