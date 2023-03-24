# Java 的可选性不是那么明显

> 原文：<https://levelup.gitconnected.com/java-optional-is-not-so-obvious-263d9559dd41>

`NullPointerException`在 Java 世界里就是这么烦人的东西，`Optional`就是为了解决这个问题而被带来的。我们不能说它已经完全消失了，但是我们已经迈出了巨大的步伐。许多流行的库和框架在其生态系统中加入了 Optional。例如，`Spring Data` 储存库返回`Optional<Entity>`，而不是`null`。

我认为一些程序员对`Optional`变得如此兴奋，以至于他们开始过度使用它，并用错误的模式应用它。在这篇文章中，我列举了这个单子的一些误用，并提供了我处理这些问题的方法。

![](img/f42f243859e80ffbea573ed6074285d4.png)

# **可选决不能是** `**null**`

我想这里不需要额外的解释。将 Optional 赋给`null`打破了这个类的整体思路。您的 API 的任何客户端都不会检查可选的空值相等。你应该更喜欢用`Optional.empty().`而不是`null`

# **了解 API**

自从发布了新的 Java 版本以来，`Optional`被增强了新的特性，允许实现更少的冗长代码。

可选. isPresent

这个想法很简单。如果名称为空，则返回默认名称。我们可以用更漂亮的方式重写。

可选。否则

让我们做一些更复杂的东西。假设，我们需要返回一个人名的`Optional`。如果该名称包含在允许的名称集中，则该值应该存在，否则不存在。下面是详细的方法。

名称的可选性

使用`Optional.filter`可以让这段代码看起来好很多。

可选名称—更好的方法

这个建议不仅适用于`Optional`而且适用于整个开发过程。

> 如果这种语言有自己的解决方案，在想出自己的解决方案之前，尝试使用它。

# **更喜欢基于价值的替代方案，而不是通用方案**

有一些特殊的非泛型可选类。`OptionalInt`、`OptionalLong`和`OptionalDouble`。如果需要使用基本类型，最好使用基于值的容器。在这种情况下，没有可能影响性能的额外装箱和取消装箱过程。

# 不要忽视懒惰

`Optional.orElse`是一种返回默认值的便捷方法。但是如果默认值的计算非常昂贵，这可能会导致一些性能问题。

可选，性能不足

即使该表存在于缓存中，每次也将从远程服务器中获取。谢天谢地，这可以用`Optional.orElseGet`轻松解决。

可选，没有性能问题

`Optional.orElseGet`接受仅适用于空集装箱的λ值。

# **不要从集合中选择**

虽然我很少看到这种情况，但有时还是会发生。

可选列表

任何集合本身都是一个容器，其中的空性可以不用额外的类来确定。

空列表—更好的方法

不必要的选项使得 API 很难使用。

# 不要将可选参数作为参数传递

这里我们开始讨论文章中最有争议的部分。

为什么不应该把 Optional 作为参数传递呢？乍一看，这似乎合乎逻辑。这有助于我们避免意外的`NullPointerException`，对吗？嗯，也许是吧。但是这个问题有更深层次的原因。

可选参数

首先，API 有不必要的界限。每次调用时，用户都必须用可选的。即使是已知的常数值。

第二，`applySettings`有 4 种潜在的不同行为，这些行为基于可选方案的存在。这违反了 [SRP(单一责任原则)](https://en.wikipedia.org/wiki/Single-responsibility_principle)。

最后，我们不知道该方法如何解释可选的？它只是用缺省值替换不存在的值，还是完全改变了业务逻辑？也可能它只是抛出了`NoSuchElementException`。

如果我们看看可选的 javadoc，我们可以发现一个有趣的注意事项。

> Optional 主要用于在明确需要表示“无结果”以及使用 null 可能会导致错误的情况下作为方法返回类型。

可选字面定义*可能没有结果的*对象。在方法中传递*可能没有结果*听起来是个坏主意。这意味着 API 知道太多关于上下文的信息，并做出它不应该知道的决定。

那么，我们该如何改进这段代码呢？如果`age`和`role`必须总是存在，我们可以从参数中删除可选的，并在顶层处理它的缺失。

消除可选参数

现在，调用代码控制着参数值。如果你开发一个框架或者一个库，这变得更加重要。

相反，如果`age`或`role`可能被省略，这种方法将不起作用。在这种情况下，最好的方法是为不同的用户需求声明不同的方法。

分离的“applySettings”方法

也许这看起来有点冗长，但是现在用户有能力做他们需要做的事情，避免意外的错误。

# 不要使用可选作为类字段

关于这一点，我听到了许多意见。有些人认为在类中直接存储选项有助于极大地减少`NullPointerException`。我在一家知名创业公司工作的朋友说，这种方法被认为是他们公司的一种模式。其他人认为这打破了单子的整个概念。

虽然直接存储 Optional 听起来是个好主意，但我认为这带来的问题多于好处。

## 没有可序列化性

可选不实现`Serializable`接口。这不是一个错误，这是手动完成的，因为 Optional 被设计为仅用作返回类型。因此，具有任何可选字段的类都不能序列化。

我认为这一点是最没有说服力的。因为在分布式系统和微服务的现代世界中，基于平台的序列化不再像过去那样重要。

## 保留不必要的引用

Optional 是用户只需要几毫秒的对象。之后，它可以被垃圾收集器删除。但是如果我们把可选的作为一个类字段，它可以存储在那里直到程序停止。您可能不会注意到小项目中的任何性能问题。无论如何，如果我们在谈论一个有几十个 beans 的大型应用程序，可能会导致不同的结果。

## 与 Spring Data/Hibernate 的集成较差

假设我们正在构建一个简单的 Spring Boot 应用程序。我们需要从表中检索值。这可以通过声明实体和相应的存储库来轻松完成。

带有存储库的简单实体

这就是`personRepository.findAll()`的可能结果。

```
Person(id=1, firstName=John, lastName=Brown)
Person(id=2, firstName=Helen, lastName=Green)
Person(id=3, firstName=Michael, lastName=Blue)
```

假设`firstName`和`lastName`字段可为空。我们不想搞砸`NullPointerException`。所以，让我们用可选的替换简单的字段声明。

具有可选字段的实体

现在都碎了。

```
org.hibernate.MappingException: Could not determine type for: java.util.Optional, at table: person, for columns: [org.hibernate.mapping.Column(firstname)]
```

Hibernate 只是不能将数据库中的值映射到 Optional。

# 但是有些东西确实可以正常工作

我不得不承认，事情终究不是那么糟糕。一些框架与可选的正确集成。

## 杰克逊

让我们声明简单的端点和 DTO。

PersonDTO

简单端点

这里是`GET /person/1`的结果。

```
{
  "id": 1,
  "firstName": "John",
  "lastName": "Brown"
}
```

如你所见，我们没有配置。一切都开箱即用。让我们试着用`Optional<String>`代替`String`

具有选项的人员

为了测试多个案例，我用`Optional.empty()`替换了一个任务。

带选项的端点

令人惊讶的是，一切仍然正常工作。

```
{
  "id": 1,
  "firstName": "John",
  "lastName": null
}
```

所以，我们可以在 Spring Web 中安全地使用 Optional 作为字段值，对吗？嗯，算是吧。有一些边角案例。

## SpringDoc

SpringDoc 是一个用于 Spring Boot 应用程序的库，可以自动生成[开放 Api 模式](https://swagger.io/specification/)。

这是我们为`GET /person/{id}`端点所得到的。

```
"PersonDTO": {
  "type": "object",
  "properties": {
    "id": {
      "type": "integer",
      "format": "int64"
    },
    "firstName": {
      "type": "string"
    },
    "lastName": {
      "type": "string"
    }
  }
}
```

看起来相当果断。但是我们需要使`id`财产成为强制性的。这可以通过使用`@NotNull`或`@Schema(required = true)`来完成。再补充一些比较有意思的细节。如果我们把`@NotNull`放在一个可选的字段上呢？

具有强制 id 的人员

那将导致有趣的结果。

```
"PersonDTO": {
  "required": [
    "firstName",
    "id"
  ],
  "type": "object",
  "properties": {
    "id": {
      "type": "integer",
      "format": "int64"
    },
    "firstName": {
      "type": "string"
    },
    "lastName": {
      "type": "string"
    }
  }
}
```

正如我们所见，`id`确实是必填字段。但是`firstName`也是。棘手的部分开始了。Optional 不能是必需的，因为它的全部含义意味着该值可能被省略。无论如何，我们仅仅用了一个额外的注释就错误地指向了框架。

有什么问题？例如，如果前端部分使用任何基于开放 Api 模式的类型生成器，这将破坏数据格式，并可能导致令人不快的后果。

# 解决办法

我们能做些什么呢？答案很简单。仅对 getters 使用 Optional。

带有可选 getters 的 PersonDTO

现在，这个类可以安全地用作 DTO 或 Hibernate 实体。可选对数据没有影响。它只是包装可空值来适当地处理缺少的数据。

但是有一个缺点。这种方法无法与 [Lombok](https://projectlombok.org/) 完全整合。库不支持可选的 getters。而且也不太可能发生。至少我们可以考虑通过[Github](https://github.com/rzwitserloot/lombok/issues/1957)上的一些讨论。

我写了一篇关于 Lombok 的文章，我认为这是一个很棒的工具。并且没有与 Optional-Getter 模式集成的事实令人不快。

目前唯一的解决方法是手动定义 getters。

# 结论

关于`java.util.Optional`，我想说的就是这些。我知道这是一个有争议的话题。如果您有任何问题或建议，请留下您的评论。感谢阅读！