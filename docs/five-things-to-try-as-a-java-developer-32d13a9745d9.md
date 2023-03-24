# 作为 Java 开发人员要尝试的五件事

> 原文：<https://levelup.gitconnected.com/five-things-to-try-as-a-java-developer-32d13a9745d9>

## 你不需要放弃 Java 生态系统来扩展你的技能和学习新的东西。

![](img/df5024b2265cc54f53b874b11b70d57b.png)

凯利·西克玛在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

作为一名 Java 开发人员，专攻一种平台或编程语言并不罕见。我们专注于我们擅长的事情，并继续做得更好。不久之后，我们把自己局限在一个狭窄的技能范围内，而牺牲了其他所有的技能。

如果你已经到了这一步，是时候扩展一下，学习一些新的东西了。首先，对你的事业有好处。当然，Java 本身——尽管它已经存在了一段时间——可能在未来一段时间内仍将是一种占主导地位的语言和平台。编写相同类型的企业 Java 应用程序很容易陷入困境。但是 Java 平台提供的东西将会不断发展。有一些有用的、革命性的创新正在你身边进行:反应式编程和函数式编程；更新、更小、更快的框架；大数据处理；备选 JVM 语言。冒着你自己职业生涯的风险，保持对它们的忽视！

第二，对大脑有好处。科学清楚地表明，学习新事物有助于你的大脑成长并保持健康。当然，你可以学习弹吉他、攀岩、变戏法或表演即兴喜剧(所有这些都是需要掌握的技能)。但是如果你是一名程序员，为什么不学习与你的工作相关的新技能呢？

最后，我认为这将有助于避免职业倦怠。只坚持你所知道的，很容易对你的软件工程生涯感到厌倦和不满。但事实上，这个行业有很多令人兴奋的事情在发生！

尽管如此，作为 Java 程序员，我们并不总是想偏离自己的领域太远。因此，下面是一些需要学习的东西，它们仍然与 Java 有一些联系。

# RxJava

反应式编程最近越来越受欢迎。这是至少两个其他行业趋势同时出现的结果:

*   基于事件循环的 web 服务器框架，如 Node.js，以及本文后面提到的一些基于 Netty 的 Java 框架
*   大数据:大数据流的批处理

RxJava(代表 Java 的*反应式扩展)已经成为第一个，也是最流行的，进行反应式编程的 Java 工具包之一。值得一提的是，自从 RxJava 兴起后， [Reactive Streams](https://www.reactive-streams.org/) Java 规范作为一种标准化 Java 中异步流处理的尝试。Reactive Streams API 与 RxJava 的原始 API 有些不同；然而，RxJava 的最新版本符合新的规范。*

但是首先，对于那些不知道什么是反应式编程，或者为什么我们想要一个反应式编程的特殊工具包的人来说，这是一本入门书。

本质上，反应式编程涉及到对异步流的编程，每次数据到达流时都有效地“反应”。所以我可能会写一个程序，说:“监听这个套接字，任何时候有新的连接进来，就执行这个工作。”我们如何指定这个作品是什么？通过提供回调。

例如，上述示例的伪 Java 代码可能如下所示:

```
Socket s = getSocket();
s.connect(new SocketCallback(SocketData d) {
 log.info(d.message);
});
```

在上面编造的 API 中，我们可以连接到一个`Socket`的实例，并连接到它，提供一个`SocketCallback`的匿名实例来处理作为新连接的结果而进入的数据。一旦进行了`s.connect(…)`调用，我们的代码将继续执行，稍后当连接进来时，我们的`SocketCallback`将执行。

这是反应式编程的一个简单例子。这就是问题所在:当我们开始超越琐碎的例子时，我们的代码会变得相当难看和难以阅读。好的，当然，在 Java 8 中，我们可以用一个 lambda:

```
getSocket().connect(d -> log.info(d.message);
```

但是，如果我们需要链接额外的反应调用来处理套接字数据呢？

```
getSocket().connect(d -> {
  httpClient.sendAsyncRequest(d.message, resp -> {
    dbClient.save(resp.body, result -> {
      log.info("data saved");
    }
  }
});
```

我们必须开始嵌套我们的回调，我们进入“回调地狱”。上面描述的三个回调可能看起来仍然是可管理的，但是在现实世界中，我们经常会发现自己将更多的被动调用链接在一起。

这就是 RxJava 的用武之地。简而言之，它允许我们以一种看起来更像过程化编程的方式将调用链接在一起:

```
getSocket.connect()
.map(d -> httpClient.sendAsyncRequest(d.message)
.map(result -> log.info("data saved"));
```

如果您使用过 Java Streams API，这可能看起来有点熟悉。但是这两种 API 之间有一些重要的区别。值得注意的是，Streams API 给了我们足够的能力在本地转换数据集；例如，在单个方法的上下文中。使用 RxJava，我们可以构建完整的应用程序，而无需离开反应式上下文。例如，当我们想要利用高度可伸缩的、基于事件循环的 web 应用程序框架时，这是很重要的。

## 如何学习 RxJava

学习 RxJava 有几种方法。首先，您可以创建一个简单的 Java 项目，添加最新的 RxJava 依赖项，此时看起来如下所示:

```
<dependency>
 <groupId>io.reactivex.rxjava2</groupId>
 <artifactId>rxjava</artifactId>
 <version>2.2.19</version>
</dependency>
```

开始时，请记住 RxJava API 实际上只有几个基本部分:

*   ***可观测量*** 。它们代表了良好的、被观察的和被处理的数据。有几种不同的类型:`Observable<T>`:基本类型，代表 0..n 项；`Single<T>`:发出一个单项(或一个错误)；`Maybe<T>`:发出一个项目，不发出任何项目，或者发出一个错误；而`Completable`:要么完成而不发出任何东西，要么返回一个错误(类似于`Runnable`)。
*   ***订户*** 。它们订阅上面提到的可观察类型之一，并处理发出的项目(或错误)。
*   ***加工功能*** 。通过`filter()`和`map()`等方法，转换由可观察类型发出的数据。他们这样做并没有改变任何数据，而是通过返回修改后的副本。在下面的例子中，我们从`String` s 的`Observable`开始，将其转换为`Integer` s 的新`Observable`(通过`map()`方法)，然后再次将其转换为`Integer` s 的新`Observable`(通过`filter()`方法)。

这样，您就可以编写并运行您的第一段 RxJava 代码，如下所示:

```
package rxdemo;import io.reactivex.Observable;public class RxDemo {public static void main(String[] args) {
    Observable<String> obs = Observable.*just*(
          "foo", "bar", "baz", "monkey", "gamma", "onomatopoeia");
    obs.map(s -> new Integer(s.length()))
    .filter(i -> i > 3)
    .subscribe(len -> {
      System.*out*.println("Found a large string of length: "+ len);
    }, e -> {
      e.printStackTrace();
    });
  }
}
```

从那里找一个好的教程开始。ReactiveX 网站有一个[页面，列出了许多教程和入门指南](http://reactivex.io/tutorials.html)。请记住，ReactiveX 是独立于语言的反应式扩展伞。虽然不同语言实现的概念非常相似，但在开始学习时，您会希望选择一个特定于 RxJava 的教程。

一旦你掌握了一些编码，你就可以在你选择的框架中使用 RxJava 了。比如 Android 应用中常用的 RxJava。你也可以[轻松地把它添加到 Spring](https://medium.com/@axella.gerald/reactive-rest-api-using-spring-boot-rxjava-4efb620c69ac) 这样的框架中，并且它在 Vert.x 这样的[其他框架中也是原生支持的。](https://vertx.io/docs/vertx-rx/java2/)

## 学习 RxJava 的收获

通过学习 RxJava，您将能够理解反应式编程。如果你没有做过很多，你可能会发现一开始会有一点挑战性(好的方面)。但是你可能也会觉得有趣和好玩。随着许多 web 应用程序转向事件循环、反应式 API 模型，您将获得越来越受欢迎的技能。

您还将获得对函数式编程的一些核心概念的相对温和的介绍(尽管您可能听说过函数式编程——虽然与反应式编程相关——但不是一回事)。值得注意的是，RxJava 是围绕[函子和单子](https://www.nurkiewicz.com/2016/06/functor-and-monad-examples-in-plain-java.html)构建的。尤其是单子，众所周知很难解释，然而一旦你理解了它们，它却出奇的简单。使用 RxJava 的可观察类型将帮助您在意识到之前理解单子。这将使你处于一个很好的位置——如果你决定尝试纯函数式编程的话(这是另一种需求越来越大的技能)。

# 斯卡拉

Scala 是 JVM 的替代编程语言。它经常被描述为一种混合的函数式面向对象语言。换句话说，它提供了函数式编程所需的所有功能，但也支持面向对象编程。这使得 Java OO 程序员学习 Scala 比学习纯函数式 JVM 语言 [Clojure](https://clojure.org/) 更容易。

也就是说，学习和理解 Scala 可能会很困难，需要付出一些努力。首先，虽然面向对象和函数式编程的结合可以让 Scala 更容易学习，但也增加了语言的复杂性。此外，Scala 倾向于提供许多不同的方式来做任何事情。Scala 还引入了许多强大的构造(例如，[隐式](https://docs.scala-lang.org/tour/implicit-parameters.html))，这些构造虽然非常有用，但可能会让新手感到困惑。最后，即使是 [Scala 的文档也很难读懂](https://www.cis.upenn.edu/~matuszek/cis554-2011/Pages/scala-api.html)，直到你习惯了它。

但是 Scala 的强大和灵活性让它在许多行业得到了越来越广泛的使用。如果您是一名 Java 开发人员，您可能会遇到已经迁移到 Scala 的 Java 商店，至少对于他们的一些系统来说是这样。即使您没有也不打算这样做，熟悉 Scala 也可以帮助您完成 Java 日常工作。

此外，Scala 从一开始就被设计为在 JVM 上运行，这也很有帮助。这意味着在幕后，您不会偏离 Java 生态系统。此外，Scala 可以与 Java 互操作。这意味着您可以从 Java 代码中调用 Scala 代码，反之亦然(事实上，当我以开发 Scala 为生时，我经常这么做。)

最后，Scala 无可否认的强大。你可以用这门语言做很多事情。学习 Scala 可能需要一段时间，但是在学习的过程中，您将会学到一些有趣且有用的构造，甚至可能会想知道 Java 何时会开始实现它们。

## 如何学习 Scala

首先，不要从学习 Scala 框架开始学习 Scala。这主要是因为借助 Scala 强大的构造和灵活的语法，它的许多流行框架(例如，Play！框架)实际上是领域特定语言(DSL)。它们中的许多都需要你学习它们自己的语法，这对学习 Scala 可能有帮助，也可能没有帮助。

相反，从简单地学习语言本身开始。有一些不错的教程，但是我建议从一本书开始。[Scala for the immune](https://www.amazon.com/Scala-Impatient-Cay-S-Horstmann/dp/0321774094)是快速学习 Scala 的最佳书籍之一。这将很好地向您介绍 Scala 最常见的特性，并将涵盖面向对象和函数式方法。

一旦你掌握了基本的语言语法和结构，我建议你把重点放在 Scala 的纯函数方面。毕竟，作为一名 Java 开发人员，OO 编程对你来说应该不是什么新鲜事。我推荐阿尔文·亚历山大的[函数式编程，简化版:(Scala 版)](https://www.amazon.com/Functional-Programming-Simplified-Alvin-Alexander/dp/1979788782/ref=sr_1_1_sspa?crid=61KPOG46HK39&dchild=1&keywords=functional+programming+in+scala&qid=1587355013&s=books&sprefix=functional+p%2Cstripbooks%2C215&sr=1-1-spons&psc=1&spLa=ZW5jcnlwdGVkUXVhbGlmaWVyPUEyWjhMOUtNQllRTVJYJmVuY3J5cHRlZElkPUEwMzE3ODI1NDRDUlQwN09XNFJRJmVuY3J5cHRlZEFkSWQ9QTA1OTg4MDczMFQwNURSTVZMT1U4JndpZGdldE5hbWU9c3BfYXRmJmFjdGlvbj1jbGlja1JlZGlyZWN0JmRvTm90TG9nQ2xpY2s9dHJ1ZQ==)。

## 学习 Scala 你会得到什么

即使你在日常工作中从未使用过 Scala，学习它仍然会让你成为一名更好的程序员。

首先，您将获得函数式编程的基础经验。除了别的以外，这将帮助您更好地理解像 Java Streams API 这样的东西，包括它为什么存在，如何更好地使用它，以及它目前缺少什么。

您还将学习一些强大的构造，例如:

*   [模式匹配](https://docs.scala-lang.org/tour/pattern-matching.html)，这将让你体会到 Java 非常缺乏的功能，但这些功能将在[的](https://cr.openjdk.java.net/~briangoetz/amber/pattern-match.html)和[的](https://dzone.com/articles/a-first-look-at-records-in-java-14)中逐渐增加。
*   [*for*comprehensions](https://www.geeksforgeeks.org/scala-for-comprehensions/)，这是一个使链式函数调用更容易理解的特性。
*   [currying](https://www.geeksforgeeks.org/currying-functions-in-java-with-examples/) ，Scala 和其他函数式语言中常见的、有用的功能，在 Java 中也可以实现
*   [case 类](https://docs.scala-lang.org/tour/case-classes.html)，它们不仅不需要显式的 getters 和 setters(以及显式的`.equals()`和`.toString()`方法和构造函数)，还支持模式匹配等特性

哦，也许你会意识到关于 Java 10 中*局部变量*的争论是多么的愚蠢。

# 春天…或者，不是春天的东西

在服务器端 Java 的世界里，似乎有两种类型的程序员:

*   那些使用 Spring(或类似的依赖注入框架)的
*   他人

Spring 可以说是最流行的服务器端 Java 框架。作为依赖注入框架的核心，Spring 已经发展成为几乎所有企业 Java 工作的事实上的 Java 框架。

它是在近二十年前发布的，表面上是对更“重量级”的 Java 企业版( *J2EE* 或者后来的 *Jave EE* )的回应。但是即使在早期，Spring 也利用了部分 Enterprise Java，最著名的是 servlet 模型。

尽管如此，Spring 仍然被认为是两者中更轻量级(和开放)的，所以它赢得了市场的最大份额。的确，春天的流行是当之无愧的。它功能强大，具有一致的编程模型，简单明了，并且被证明可以适应行业变化。事实上，以至于许多 Spring 程序员已经沉浸在框架中，再也不想学习其他任何东西。

但问题是，这里的 ***是*** 还有很多其他的。无数其他的 Java 框架涌现出来，并且越来越受欢迎。虽然其中一些框架遵循 Spring 的基本模式(依赖注入、分层架构、每请求线程编程模型*)，但它们中的许多提供了完全不同的范例。

也就是说，也许你是 ***不使用***Spring 的后端 Java 开发者之一。鉴于 Spring 仍然是最流行的 Java 框架，为什么不花点时间熟悉一下呢？您可能不会最终切换到 Spring 作为您的 事实上的框架，但是它将帮助您理解它为什么如此受欢迎，以及它的缺点导致了其他框架的出现。

**历史上。Spring 5 引入了反应式 Webflux 模型，尽管今天许多 Spring 应用程序仍然遵循每请求线程模型。*

## 从学习非 Spring 框架中你会得到什么

如果您认为自己是 Spring 开发人员，那么您可能已经习惯了这样的想法，即基于配置或基于注释的依赖注入对于严肃的应用程序开发是绝对必要的。您的应用程序设计应该看起来像一个生日蛋糕(底部是美味的巧克力 DAO 层，中间是美味的香草业务/域层，顶部是美味的草莓 UI 层)。每个请求应该在自己的线程上运行。此外，您的应用程序需要几秒钟甚至几分钟才能启动，这没什么大不了的。

因此，挑战这些假设是值得的。例如，有许多较新的应用程序避免了依赖注入——至少 Spring 提供了重量级的内置依赖注入——并且比 Spring 更高效。

有些框架的核心是基于非阻塞的、反应式的模型。这通常允许单个服务器处理高得多的负载。这也是一个有趣的编程模型，需要熟悉。

而且许多这样的框架——因为它们在启动时缺少 Spring 的依赖注入连线——会在眨眼之间启动。事实上，有些甚至本身就支持热重载，避免了重新编译/重启来测试更改的需要。

您很可能会决定坚持使用 Spring 作为您事实上的框架。但是探索其他框架将会让您体验到现有的替代方案。

就其本身而言，这会拓宽你的技能。此外，如果您遇到 Spring 不是理想解决方案的用例，并且面临队友怀疑您应该使用完全不同的语言时，它可以成为救命稻草。我不止一次遇到过这样的情况，大量的预期并发请求促使我们考虑使用 Node.js。我对 Vert.x 框架的熟悉——它本身源于 Node.js 模型，提供了超越 Node 的优势——为我们提供了一个很好的选择，让我们留在了 Java 世界中。

## 学习非 Spring 框架

学习替代框架首先要做的是选择要学习的框架。外面有许多选择；我会推荐三个好的候选人。

**垂直 x 轴**

[Vert.x](https://vertx.io/) 是我用过的所有框架中最喜欢的一个。像许多新的 Java 框架一样，它是建立在 Netty 之上的；它提供了基于事件循环的编程范式和反应式 API，使它能够处理大量的请求。它还提供了许多有用的创新功能，包括:

*   *多语言编程*。编写 Vert.x 应用时，可以从众多语言中选择，比如 Python、Scala、Kotlin、Javascript，当然还有 Java。您甚至可以在同一个应用程序中混合使用多种语言。因此，即使您可能坚持使用 Java，您也可以涉猎其他语言……或者让您热爱 Python 的队友相信 Vert.x 是您下一个项目的正确选择。
*   *垂直*。Vert.x 应用可以通过使用*vertices*来组成，vertices 是独立部署的组件。垂直设备通过事件总线(接下来讨论)传递消息来相互通信。这使得垂直元素彼此分离。它还提供了一个灵活的部署模型，其中相同的垂直集可以在相同的应用程序中运行，也可以跨不同的应用程序运行。
*   *事件总线*。顾名思义，Vert.x 事件总线是一个发布和使用消息的通道。这是不同垂直领域进行沟通的主要机制。
*   *热重装*。特别是当采用垂直模型时，更改会自动重新加载。无需退出，重新编译，并重新启动您的应用程序。

学习 Vert.x 有很多方法。你会在网上找到很多教程([包括我自己的一个](https://medium.com/better-programming/build-a-movie-tracking-system-using-react-and-java-522388965c55))，但是你也可以直接从 [vertx.io](https://vertx.io/) 开始。该文档是一流的，并且该网站提供了许多操作方法和教程的链接。

**玩！**

[上场了！十年前，Typesafe(现在莫名其妙地被称为](https://www.playframework.com/) [Lightbend](https://www.lightbend.com) )创建了框架。它用 Scala 编写，很快成为最流行的 Scala web 框架之一。然而，它也提供了一流的 Java API，因此您可以继续使用您的语言。

Play 也是建立在 Netty 之上的，并具有一个反应式 API。因此，它为开发基于事件循环的 web 应用程序提供了很好的介绍。它还具有快速启动时间和热代码重载功能，允许开发人员避免重新编译-重新启动循环来测试更改。

这出戏！网站为[提供了大量的教程](https://www.playframework.com/documentation/2.8.x/Tutorials)，其中大部分专注于某个特定的功能。它还链接到各种第三方教程，其中许多是新手的良好起点。

**夸尔库斯**

作为最新的 Java 框架之一(至少在撰写本文的时候)， [Quarkus](https://quarkus.io/) 是一个有前途的框架，具有近乎革命性的特性。像大多数新的 Java 框架一样，Quarkus 是建立在 Netty 之上的。事实上，它实际上是建立在 Vert.x(如上所述)之上的，而 vert . x 本身是建立在 Netty 之上的。它还增加了自己的反应库，大大简化了反应代码的编写。

但不仅如此，Quarkus 是从零开始建造的，考虑到了 Kubernetes 和容器。快速启动、低内存开销和更小的应用程序从一开始就是目标。Quarkus 也被设计成可以在 GraalVM 上运行，并且可以很容易地编译成本地可执行文件。

Quarkus 还宣传它的命令式和反应式编程的融合。事实上，我不推荐 Quarkus 作为 Spring 开发者尝试的另一个框架的唯一原因是 Quarkus 可能有点太熟悉了。也就是说，对于那些想尝试新东西但又不想偏离舒适区太远的 Spring 开发者来说，Quarkus 可能正是他们想要的。

要开始使用 Quarkus，可能没有比前往 Quarkus 的[入门](https://quarkus.io/guides/getting-started-reactive)页面更好的方法了。

## 你将从学习 Spring 中得到什么

如果你是少数几个不知道或不使用 Spring 的 Java 程序员之一，那么你真的应该熟悉它。Spring 是事实上的依赖注入框架(在 Java 生态系统以及其他软件行业中)。理解 Spring 将有助于您理解依赖注入到底有多强大。

## 学习 Spring 框架

到目前为止，有无数的资源可以用来学习 Spring。此外，Spring 生态系统已经变得足够庞大，以至于您需要决定想要尝试哪种风格的 Spring 应用程序。我建议切入正题，用 [Spring Boot](https://github.com/spring-projects/spring-boot) 构建一个 [SpringMVC](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html) 应用程序(这是 Spring 的微服务快速开发环境)。只需前往 [Spring Boot 入门页面](https://spring.io/guides/gs/spring-boot/)。

# 阿帕奇波束

如今，大数据是大新闻。多年来，出现了许多用于处理大量数据的框架——Hadoop、Flink、Spark、Storm。对于普通程序员来说，没有一个是超级直观的。作为一名通才软件开发人员，他们总是不敢坐下来学习。

阿帕奇光束不一样。首先，Beam 是一个更高级的数据处理框架，它独立于运行程序。这意味着您可以编写自己的代码(Java 是 Beam 的一等公民，Python 也是)，并使用各种不同的运行程序运行它。从某种意义上说，Beam 就像 Java 一样，只需编写一次就可以在任何地方运行。例如，你可以编写你的数据处理代码，并轻松地在本地运行它，然后在不更改一行代码的情况下，在 [Google Cloud Dataflow](https://cloud.google.com/dataflow) 上运行它。

而且入门极其容易。一旦您创建了一个 Maven 项目并导入了 Bean 依赖项，那么在本地创建并运行一个 Beam 作业所需要的就是一个像下面这样简单的类:

```
public class WordCounter {public static void main(String[] args) {
    PipelineOptions options = PipelineOptionsFactory.create();
    Pipeline p = Pipeline.create(options);
    p.apply(TextIO.read().from("gs://apache-beam-samples/shakespeare/*"))
        .apply(
            FlatMapElements.into(TypeDescriptors.strings())
                .via((String line) -> Arrays.asList(line.split("[^\\p{L}]+"))))
        .apply(Filter.by((String word) -> !word.isEmpty()))
        .apply(Count.perElement())
        .apply(
            MapElements.into(TypeDescriptors.strings())
                .via(
                    (KV<String, Long> wc) ->
                        wc.getKey() + ": " + wc.getValue()))
        .apply(TextIO.write().to("wordcounts"));
    p.run().waitUntilFinish();
  }
}
```

并通过如下方式运行它:

```
mvn compile exec:java -Dexec.mainClass=com.me.example.WordCounter -Dexec.args=**"--output=./output/"**
```

显然，这是一个基本的例子，所有的东西都被塞进了一个 main 方法中——很难像你写一个真正的程序那样。但是它显示了从一个简单的 Beam 示例开始，然后随着您了解的越来越多，构建它是多么容易。

## 学习平衡木的收获

通过学习 Apache Beam，您将体会到如何处理大型数据集。当然，你可能不希望自己成为一名数据工程师。但是工程界正在向大数据处理发展。因此，了解如何编写数据处理代码对后端软件工程师来说变得越来越重要。

此外，通过学习 Beam，您将了解一个可以在多种环境中运行的框架。正如我们提到的，Beam 是跑步者不可知的。所以你可以轻松地在本地运行你的 Beam 应用，也可以在 Google Cloud 数据流上运行。你也可以在 Spark 上运行它们，这意味着(多亏了 [AWS EMR](https://aws.amazon.com/emr/) )你可以在 AWS 上运行它们(尽管需要更多的努力)。

## 学习阿帕奇光束

“字数统计”的例子似乎是阿帕奇梁的 *hello world* 。所以我推荐阅读 Beam 的[入门教程，开始在本地开发和运行 Beam。此外，在谷歌云平台(GCP)上运行同样的例子非常容易。事实上，GCP 基于同样的 WordCount 应用程序提供了自己的](https://beam.apache.org/get-started/wordcount-example/)[简单易懂的教程](https://cloud.google.com/dataflow/docs/quickstarts/quickstart-java-maven)。因此，要么尝试其中之一，要么两者都尝试。

# React.js

作为 Java 开发人员，我们热爱面向对象、强类型和编译代码，这是有充分理由的。因此，很难采用一种既不提供编译、不提供强类型、也不提供(直到不久前)对象的编程语言。

然而 JavaScript 和 Java 是最流行的编程语言之一。你可能会说它的流行仅仅是因为它是唯一一种可以在所有主流浏览器上运行的语言。你可能是对的。但是这并不能改变 JavaScript 在这个行业中是一种非常重要的语言的事实。

但是作为一名 Java 开发人员，您如何调和您对这种语言的厌恶和学习它的需要呢？有答案，那个答案就是 [*React.js*](https://reactjs.org/) 。

当然，你可能听说过 React。但是从外部来看，它似乎是前端世界正在谈论的一长串 JavaScript 框架中的最新一个。不是的。信不信由你，作为一名 Java 开发人员，[你可能真的喜欢 React](/even-if-you-hate-javascript-you-might-love-react-9e4134787d87) 。

这有许多原因，例如:

*   这是一个易于理解的范例。React 的核心是构建组件。然后，这些组件被组合成 UI 应用程序。当然，你需要使用一些 JavaScript，但是学习 React 并不是学习 JavaScript；是关于学习一个 Web UI 框架。
*   JavaScript 只是等式的一部分。使用 React 时，您将开发由三部分组成的组件: ***表示样式*** 以 ***级联样式表*** 或 CSS 的形式；*形式的[***JSX***](https://reactjs.org/docs/introducing-jsx.html)(这点和 HTML 很像)，以及 ***形式的*** 形式的 ***JavaScript*** 。*
*   ***JavaScript 更像胶水**。正如刚才提到的，您将不会纯粹使用 JavaScript。此外，当您编写 JavaScript 时，您通常会将它作为真正的脚本语言来使用，本质上是将应用程序的各个部分粘合在一起。基本上，这是几十年前它第一次被编写时的使用方式。*
*   *它有一个很好的编译系统，可以捕捉错误。还记得我们说过作为 Java 开发人员，我们喜欢编译器吗？React 提供了一个(实际上，更像是一个 transpiler)，而且是一个很好的工具。除了构建紧凑的生产就绪代码之外，它还会捕捉某些类型的错误。虽然不像 Java 编译器那样健壮，但它提供了良好的防护，防止运行时出现错误。*
*   *在 Java 应用程序上构建 React UIs 很容易。仅仅因为你正在使用 React、JavaScript 和 node . js(React 的构建系统运行于其上),并不意味着你不能愉快地继续用 Java 构建你所有的服务器端应用程序。事实上，构建与 Java 后端交互的 React UIs 非常容易。此外，通过将所有内容打包到一个可执行的超级 JAR 中，可以很容易地发布一个由 React 提供前端并由 Java 提供支持的自包含 web 应用程序。*
*   *如果你愿意，可以使用 TypeScript。在 React 中，JavaScript 以易于管理的小块形式出现。但是如果你真的反对 JavaScript，你总是可以使用 [TypeScript、](https://www.typescriptlang.org/)和强静态类型语言，它们可以向下转换成 JavaScript。老实说，TypeScript 是一种非常有趣的语言，有很多 Java 开发人员熟悉的特性(强类型、丰富的对象层次结构)，也有一些 Java 开发人员可能会有点嫉妒的特性(例如联合类型、内置元组)。*

## *如何学习 React.js*

*外面有很多好的 React.js 教程，[比如这个](https://reactjs.org/tutorial/tutorial.html)。大多数教程都假设您对 JavaScript 有所了解。但是，即使您从未使用 JavaScript 开发过，您也几乎可以肯定地理解它，不会有什么问题。*

*也就是说，我在学习 React 时经常遇到的一个问题是 JavaScript 的[上下文](https://scotch.io/tutorials/understanding-scope-in-javascript)。您将在 React 中编写的大部分 JavaScript 都是回调方法的形式。换句话说，您将编写一个能够响应某些事件(HTTP 请求返回、用户点击按钮等)的组件。您将在该组件中编写一个回调方法，并注册该回调来处理该事件。任何 Java 开发人员都希望能够使用`this`关键字来引用组件……毕竟，这是定义方法的地方。但是你不能…除非你在组件的构造函数中做了一件奇怪的事情:*

```
*this.myCallback = this.myCallback.bind(this);*
```

*不要问我为什么。这只是一件事。大多数 JavaScript 教程都会简要涉及到这一点；一定要记住，尤其是当你的 React 错误消息试图告诉你你的`myCallback()`方法试图调用另一个不存在的方法时。*

## *您将从 React.js 获得什么*

*通过学习 React.js，您将了解最流行的 UI 框架之一。你也将获得学习 JavaScript 的立足点。此外，您将学习一个直观的 Web UI 范例，它可以很好地与 Java 集成。*

# *是时候成长了*

*我明白了。你喜欢 Java，你对自己的职业生涯感到满意。但是过于舒适会对你的职业生涯、精神敏锐度以及(也许具有讽刺意味的)职业满足感造成损害。即使不离开 Java 生态系统，也有足够多的创新性东西可供尝试和学习。*

# *参考*

*   *[https://stack ify . com/10-of-the-most-popular-Java-frameworks-of-2020/](https://stackify.com/10-of-the-most-popular-java-frameworks-of-2020/)*
*   *[https://developers . red hat . com/blog/2019/03/07/quar kus-next-generation-kubernetes-native-Java-framework/](https://developers.redhat.com/blog/2019/03/07/quarkus-next-generation-kubernetes-native-java-framework/)*
*   *[https://www . react ive world . net/2018/04/29/rx Java-vs-Java-stream . html](https://www.reactiveworld.net/2018/04/29/RxJava-vs-Java-Stream.html)*
*   *[https://dzone . com/articles/functor-and-monad-examples-in-plain-Java](https://dzone.com/articles/functor-and-monad-examples-in-plain-java)*
*   *[https://medium . com/@ lui jar/the-observable-伪装成一个 io-monad-c89042aa8f31](https://medium.com/@luijar/the-observable-disguised-as-an-io-monad-c89042aa8f31)*
*   *[https://www . Inc . com/Brian-wong/how-learning-a-new-skill-helps-your-mind-grow-strong . html](https://www.inc.com/brian-wong/how-learning-a-new-skill-helps-your-mind-grow-stronger.html)*

*觉得这个故事有用？想多读点？只需[在这里订阅](https://dt-23597.medium.com/subscribe)就可以将我的最新故事直接发送到你的收件箱。*

*今天[成为媒体会员](https://dt-23597.medium.com/membership)，你也可以支持我和我的写作，并获得无限数量的故事。*