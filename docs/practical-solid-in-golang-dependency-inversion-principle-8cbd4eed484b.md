# Golang 中的实用实体:依赖反转原理

> 原文：<https://levelup.gitconnected.com/practical-solid-in-golang-dependency-inversion-principle-8cbd4eed484b>

## 坚实的原则

## 我们通过展示对 Go 中的单元测试影响最大的一个原则——依赖倒置原则，来继续坚实的原则之旅。

![](img/e3ac59821d9c36a32981552cd09da8d1.png)

Jonny Gios 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

学习一门新的编程语言通常是一个简单的过程。经常听到:“你一年学的第一门编程语言。一个月内的第二起。一周内第三次，然后一天内每下一次。”。

这么说有些夸张，但在某些情况下，这与事实并不太遥远。例如，跳到与前一种语言相对相似的语言，如 Java 和 C#，可能是一个简单的过程。

但是有时候，切换是很棘手的，即使我们从一种面向对象语言切换到另一种。如果一种语言有接口、抽象类或根本没有类，那么许多特性都会影响这种转换，比如强类型或弱类型。

其中一些困难是我们在转换后立即经历的，我们采用了一种新的方法。但是我们后来经历的一些问题，例如在[单元测试](https://betterprogramming.pub/5-and-a-half-techniques-for-effectively-writing-unit-tests-in-go-1b87b94abd21)期间。然后，我们学习为什么依赖倒置原则是必要的，特别是在围棋中。

```
Other articles from the SOLID series:**1\.** [**Practical SOLID in Golang: Single Responsability Principle**](/practical-solid-in-golang-single-responsibility-principle-20afb8643483)**2\.** [**Practical SOLID in Golang: Open/Closed Principle**](/practical-solid-in-golang-open-closed-principle-1dd361565452)**3\.** [**Practical SOLID in Golang: Liskov Substitution Principle**](/practical-solid-in-golang-liskov-substitution-principle-e0d2eb9dd39)**4\.** [**Practical SOLID in Golang: Interface Segregation Principle**](/practical-solid-in-golang-interface-segregation-principle-f272c2a9a270)Some articles from the DDD series:**1.** [**Practical DDD in Golang: Value Object**](/practical-ddd-in-golang-value-object-4fc97bcad70)**2\.** [**Practical DDD in Golang: Entity**](/practical-ddd-in-golang-entity-40d32bdad2a3)**3\.** [**Practical DDD in Golang: Aggregate**](/practical-ddd-in-golang-aggregate-de13f561e629)**4\.** [**Practical DDD in Golang: Repository**](/practical-ddd-in-golang-repository-d308c9d79ba7)**5\. ...**
```

[](https://blog.ompluscator.com/membership) [## 通过我的推荐链接加入媒体——马尔科·米洛耶维奇

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

blog.ompluscator.com](https://blog.ompluscator.com/membership) 

# 当我们不尊重依赖性倒置时

> 高层模块不应该依赖低层模块。两者都应该依赖于抽象。抽象不应该依赖于细节。细节应该依赖于抽象。

我们可以从上面看到倾角的定义。鲍勃叔叔在他的论文中提出了它。他的[博客](https://blog.cleancoder.com/uncle-bob/2016/01/04/ALittleArchitecture.html)里面也有更多的细节。

那么，如何理解这一点，尤其是在围棋语境中？首先，我们应该接受*抽象*作为 OOP [概念](https://eng.libretexts.org/Courses/Delta_College/C_-_Data_Structures/06%3A_Abstraction_Encapsulation/1.01%3A_Difference_between_Abstraction_and_Encapsulation)。我们使用这样一个概念来暴露本质行为，隐藏它们实现的细节。

二、什么是高低级模块？Go 环境中的高级模块是应用程序顶层使用的软件组件，就像用于表示的代码一样。

它也可以是接近顶层的代码，比如业务逻辑或一些用例组件的代码。有必要将它理解为一个为我们的应用程序提供真正商业价值的层。

另一方面，低级别的软件组件大多是支持高级别的小代码片段。它们隐藏了不同基础设施集成的技术细节。

例如，它可以是一个结构，用于保存从数据库检索数据、发送 SQS 消息、从 Redis 获取值或向外部 API 发送 HTTP 请求的逻辑。

那么，当我们打破依赖倒置原则，我们的高级组件依赖于一个低级组件时，会是什么样子呢？让我们看看下面的例子:

在上面的代码片段中，我们定义了一个高级组件`EmailService`。这个 struct 属于应用层，负责给刚注册的客户发邮件。

这个想法是要有一个方法，`SendRegistrationEmail`，它需要一个`User`的 ID。在后台，它从`UserRepository`中检索一个`User`，然后(可能)将它交付给某个`EmailSender`服务来执行电子邮件交付。

带有`EmailSender`的部分现在不在我们的关注范围内。相反，让我们专注于`UserRepository`。该结构表示与数据库通信的存储库，因此它属于基础设施层。

因此，看来我们的高层组件`EmailService`依赖于低层组件`UserRepository`。实际上，如果没有定义到数据库的连接，我们就不能启动用例结构。

这种反模式会直接影响我们在 Go 中的单元测试。假设我们想要测试 EmailService，如下面的代码片段所示:

与一些语言不同，如 [PHP](https://phpunit.readthedocs.io/en/9.5/test-doubles.html) ，我们不能在 Go 中随意模仿。Go 中的模仿依赖于接口的使用，为此我们可以定义一个模仿的实现，但我们不能对结构做同样的事情。

因此，我们不能模仿 UserRepository，因为它是一个结构体。在这种情况下，我们需要在较低的级别上进行模拟，在这种情况下，在 [Gorm](https://gorm.io/index.html) 连接对象上，我们可以用 [SQLMock](https://github.com/DATA-DOG/go-sqlmock) 包来完成。

但是即使有了它，它也不是一个可靠的、有效的测试方法。我们需要模拟太多的 SQL 查询，了解太多的数据库模式。数据库内部的任何变化，我们都需要调整单元测试。

单元测试放在一边，现在我们有一个更大的问题。如果我们决定把存储换成其他东西，比如[卡珊德拉](https://cassandra.apache.org/_/index.html)，会发生什么？主要是我们未来计划为客户提供的分布式存储？

如果出现这种情况，并且我们使用 UserRepository 的这种实现，许多重构将随之而来。

现在，我们看到高层组件依赖于低层组件的含义是什么。但是依赖细节的抽象又如何呢？让我们检查下面的代码:

要解决高级和低级组件的第一个问题，我们应该从定义一些接口开始。在这种情况下，我们可以将`UserRepository`定义为域层上的一个接口。

因此，它提供了一个从数据库中解耦`EmailService`的机会，但仍然不是完全解耦。看看`User`结构。它仍然提出了映射到数据库的定义。

而且，即使这样的结构在域层内部，它仍然拥有基础设施的细节。我们的新接口`UserRepository`(抽象)依赖于数据库模式的`User`结构(细节)，我们仍然打破了 DIP。

改变数据库模式不可避免地会改变我们的界面。该接口仍然可以使用相同的用户结构，但是它将保存来自低级别层的更改。

最后，通过这种重构，我们一无所获。我们仍然处于错误的位置。带来许多后果:

1.  我们不能正确地测试我们的业务或应用程序逻辑。
2.  对数据库引擎或表结构的任何更改都会影响我们的最高级别。
3.  我们不能轻易切换到不同类型的存储。
4.  我们的模型与存储紧密相关。
5.  …

所以，让我们再一次重构这段代码。

# 我们如何尊重依赖倒置

> 高层模块不应该依赖低层模块。两者都应该依赖于抽象概念。抽象不应该依赖于细节。细节应该依赖于抽象。

让我们回到依赖倒置的原始指令，检查加粗的句子。他们已经给出了重构的一些方向。

我们应该定义一些抽象(一个接口)，我们的组件`EmailService`和`UserRepository`都将依赖于这些抽象。另外，这样的抽象不应该依赖于任何技术细节(像 Gorm 对象)。

让我们从下面检查代码:

在新的代码结构中，我们可以看到`UserRepository`接口是一个依赖于`User`结构的组件，它们都在域层内部。

`User`结构不再反映数据库模式，但是我们使用了`UserGorm`结构。该结构位于基础结构层。它提供了一个方法`ToUser`，将它映射到实际的`User`结构。

在这个场景中，我们可以使用`UserGorm`作为`UserDatabaseRepository`内部使用的细节的一部分，作为`UserRepository`的实际实现。

在域和应用层内部，我们只依赖于来自域的`UserRepository`接口和`User`实体。

在基础设施层内部，我们可以根据需要为`UserRepository`定义尽可能多的实现。例如，可以是`UserFileRepository`或`UserCassandraRepository`。

高层组件(`EmailService`)依赖于抽象——它包含一个类型为`UserRepository`的字段。尽管如此，低层组件如何依赖抽象？

在 Go 中，structs 隐式地实现接口。这意味着我们不需要在`UserDatabaseRepository`显式实现`UserRepository`的地方添加代码，但是我们可以添加一个带有空白标识符的检查。

使用这种方法，我们可以更容易地控制我们的依赖性。我们的结构依赖于接口，当我们想要改变我们的整体依赖时，我们可以定义不同的实现并注入它们。

这种技术在任何框架中都很常见，我们用[依赖注入模式](https://en.wikipedia.org/wiki/Dependency_injection)来解决它。在围棋中，有很多 DI 库，比如来自[脸书](https://github.com/facebookarchive/inject)、[钢丝](https://github.com/google/wire)或者[野狗](https://github.com/i-love-flamingo/dingo)的库。

我们的单元测试情况如何？让我们检查一下。

通过这种重构，我们可以提供一个简单的模仿对象`GetByIDFunc`，作为一个新的类型，它从`UserRepository`中定义了一个我们想要模仿的函数。下面是 Go 中定义一个函数类型，并给它分配一个方法来实现一个接口的常用方法。

现在，我们的测试更加优雅和高效。我们可以为`UserRepository`注入不同的实现，用于任何用例，并控制测试的结果。

# 更多的例子

我们可以在其他组件中体验 breaking DIP，而不仅仅是结构。例如，可以具有纯的、独立的功能:

所以，我们想为一个`User`读取数据。为此，我们可以使用文件和 JSON 格式。方法`GetUser`从文件中读取并将文件内容转换成实际的`User`。

方法本身依赖于文件的存在，如果我们想要正确地测试它，我们需要依赖于这样的文件。因此，为这个方法编写测试是不方便的，例如，测试验证规则，如果我们稍后将它们添加到`GetUser`方法中。

同样，我们的代码依赖于太多的细节，做一些抽象会很好:

有了新的实现，我们让方法`GetUser`依赖于`Reader`接口的一个实例。它是来自 Go 核心包 [IO](https://pkg.go.dev/io) 的一个接口。

在这里，我们可以定义许多不同的方式来提供`Reader`接口的实现，比如`GetUserFile`、`GetUserHTTP`、`GetDummyUser`(我们可以用它们来测试方法`GetUser`)。

这种方法我们可以在许多不同的情况下使用。每当我们在进行适当的单元测试时遇到困难，或者甚至在 Go 中遇到依赖循环时，我们应该通过提供一个接口和尽可能多的实现来尝试去耦合它。

# 结论

依存倒置原则是最后一个立体原则，它代表单词*立体*中的字母 *D* 。它声称高级组件不应该依赖于低级组件。

相反，我们所有的组件都应该依赖于抽象，或者更好地说，依赖于接口。这样的抽象允许我们更灵活地使用我们的代码，并正确地测试它。

```
Other articles from the SOLID series:**1\.** [**Practical SOLID in Golang: Single Responsability Principle**](/practical-solid-in-golang-single-responsibility-principle-20afb8643483)**2\.** [**Practical SOLID in Golang: Open/Closed Principle**](/practical-solid-in-golang-open-closed-principle-1dd361565452)**3\.** [**Practical SOLID in Golang: Liskov Substitution Principle**](/practical-solid-in-golang-liskov-substitution-principle-e0d2eb9dd39)**4\.** [**Practical SOLID in Golang: Interface Segregation Principle**](/practical-solid-in-golang-interface-segregation-principle-f272c2a9a270)Some articles from the DDD series:**1.** [**Practical DDD in Golang: Value Object**](/practical-ddd-in-golang-value-object-4fc97bcad70)**2\.** [**Practical DDD in Golang: Entity**](/practical-ddd-in-golang-entity-40d32bdad2a3)**3\.** [**Practical DDD in Golang: Aggregate**](/practical-ddd-in-golang-aggregate-de13f561e629)**4\.** [**Practical DDD in Golang: Repository**](/practical-ddd-in-golang-repository-d308c9d79ba7)**5\. ...**
```