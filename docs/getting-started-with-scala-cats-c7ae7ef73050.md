# Scala Cats 入门

> 原文：<https://levelup.gitconnected.com/getting-started-with-scala-cats-c7ae7ef73050>

![](img/882298f105f68977b3e5a8fa4484072c.png)

# 介绍

这篇文章是关于 Scala 猫的。它是一个为 Scala 编程语言中的函数式编程提供抽象的库。这个名字是单词*类别*的有趣缩写。

Cats 是一个轻量级、模块化、可扩展的函数式编程库。

Cats 包含各种各样的函数式编程工具，并允许开发人员选择所需的工具。这些工具中的大多数都是以类型类的形式交付的，我们可以将其应用于现有的 Scala 类型。

# 什么是类型类？

类型类是起源于 Haskell 的一种编程模式。它们允许我们用新的功能扩展现有的库，而不使用传统的继承，也不改变原始的库源代码。

类型类模式有三个重要的组成部分:类型类本身、特定类型的实例和使用类型类的方法。

# 什么是类型类？

类型类是一个接口或 API，表示我们想要实现的一些功能。在 Scala 中，一个类型类由至少有一个类型参数的 trait 来表示。

例如，我们可以如下表示一般的“序列化为 JSON”行为:

```
// Define a very simple JSON AST
sealed trait Json
final case class JsObject(get: Map[String, Json]) extends Json
final case class JsString(get: String) extends Json
final case class JsNumber(get: Double) extends Json
final case object JsNull extends Json// The "serialize to JSON" behaviour is encoded in this trait
trait JsonWriter[A] {
  def write(value: A): Json
}
```

# 什么是类型类实例？

type 类的实例为我们关心的特定类型提供了 type 类的实现，这些类型可以包括来自 Scala 标准库的类型和来自我们的域模型的类型。

在 Scala 中，我们通过创建类型类的具体实现来定义实例，并用*隐式*关键字来标记它们:

```
final case class Person(name: String, email: String)object JsonWriterInstances {
  implicit val stringWriter: JsonWriter[String] = new JsonWriter[String] {
    def write(value: String): Json = JsString(value)
  }
  implicit val personWriter: JsonWriter[Person] = new JsonWriter[Person] {
    def write(value: Person): Json =
      JsObject(Map(
        "name" -> JsString(value.name),
        "email" -> JsString(value.email)
      ))
  }
}
```

# 如何使用类型类？

类型类用途是需要类型类实例才能工作的任何功能。在 Scala 中，这意味着任何接受类型类实例作为隐式参数的方法。

Cats 提供了使类型类更容易使用的工具，你有时会在其他库中看到这些模式。

有两种方法:接口对象和接口语法。

# 什么是接口对象？

创建使用类型类的接口的最简单方法是将方法放在 singleton 对象中:

```
object Json {
  def toJson[A](value: A)(implicit w: JsonWriter[A]): Json = w.write(value)
}
```

为了使用这个对象，我们导入我们关心的任何类型类实例，并调用相关的方法:

```
import JsonWriterInstances._
Json.toJson(Person("Dave", "dave@example.com"))
```

编译器发现我们调用的 *toJson* 方法没有提供隐式参数。它试图通过搜索相关类型的类型类实例并将其插入调用位置来解决这一问题:

```
Json.toJson(Person("Dave", "dave@example.com"))(personWriter)
```

# 什么是接口语法？

我们也可以使用扩展方法，用接口方法扩展现有的类型。Cats 将此称为类型类的“语法”:

```
object JsonSyntax {
  implicit class JsonWriterOps[A](value: A) {
    def toJson(implicit w: JsonWriter[A]): Json = w.write(value)
  }
}
```

我们通过将接口语法与所需类型的实例一起导入来使用接口语法:

```
import JsonWriterInstances._
import JsonSyntax._
Person("Dave", "dave@example.com").toJson
```

同样，编译器搜索隐式参数的候选项，并为我们填充它们:

```
Person("Dave", "dave@example.com").toJson(personWriter)
```

# 什么是隐含？

在 Scala 中使用类型类意味着使用隐式值和隐式参数。为了有效地做到这一点，我们需要知道一些规则。

Scala 中任何标记为隐式的定义都必须放在对象或特征内部，而不是放在顶层。

在上面的例子中，我们将类型类实例打包在一个名为 *JsonWriterInstances* 的对象中。我们同样可以将它们放在 *JsonWriter* 的一个伴随对象中。在 Scala 中，将实例放在 type 类的伙伴对象中有着特殊的意义，因为它被称为隐式作用域。

# 什么是隐式范围？

正如我们在上面看到的，编译器根据类型搜索候选类型类实例。

例如，在下面的表达式中，它将查找类型为*JSON writer[字符串]* 的实例:

```
Json.toJson("A string!")
```

编译器搜索候选实例的地方称为隐式范围。隐式作用域适用于调用点，也就是我们调用带有隐式参数的方法的地方。

# 摘要

在本文中，我们首先看了一下类型类。

我们看到了组成类型类的组件:

*   一个特征，也就是类型类。
*   类型类实例，它们是隐式值。
*   使用隐式参数的类型类用法。

# 类似文章-

你也可以看看我在 *Scala Cats* 系列上的其他文章

*   [潜入标量猫—半群](/diving-into-scala-cats-semigroups-732ef2432042)
*   [潜入 Scala 猫——幺半群](/diving-into-scala-cats-monoids-82e744b9e518)
*   [深入 Scala 猫——函子](/diving-into-scala-cats-functors-c957285d7009)
*   [函数编程中的函子](/functors-in-functional-programming-dfaba4cfb2ed)