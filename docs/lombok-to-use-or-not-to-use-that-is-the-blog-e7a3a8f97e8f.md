# 用或不用，那是博客

> 原文：<https://levelup.gitconnected.com/lombok-to-use-or-not-to-use-that-is-the-blog-e7a3a8f97e8f>

## 如果你使用然后如何使用，那也是博客

![](img/4dcea603e9441143b553214bdbdb20a0.png)

Project Lombok 是一个 java 库，可以自动插入到您的编辑器和构建工具中，为您的 Java 增添趣味。再也不用编写另一个 getter 或 equals 方法了，有了一个注释，你的类就有了一个全功能的构建器，自动记录变量，等等。
[—龙目岛队](https://projectlombok.org/)

Project Lombok 是一个非常好的工具，可以让 Java 编码变得简单。对于那些说 Java 代码与 Python 相比非常大的开发者来说，这是一个很好的回答。Lombok 帮助开发人员专注于实现，而不用担心编写模板代码并保持最终代码更短(相对而言😅).

但是…现在大多数 ide 都支持生成锅炉板代码，那么我们为什么还需要 Lombok 呢？
是的，虽然我们仍然可以在 IDE 中生成代码，但它仍然是一个样板代码，增加了总 LOC，从而降低了代码的可读性和可维护性。

在这篇文章中，我将讨论 Lombok 的一些神话、关键特性和实现细节。

# 揭秘神话

当团队讨论在他们的应用程序中使用 Lombok 时，有一些共同的假设和理论。因此，在我们讨论实现之前，在本文中首先讨论它们也变得很重要。

1.  它增加了代码的紧密耦合性
2.  它降低了预见代码气味的能力
3.  它阻碍了良好的设计实践

是的，当我们谈论将 Lombok 项目添加到我们的应用程序中时，它们中的大多数都是有效的，但问题是影响是什么？让我们看看。

## 紧密结合

所有的类和代码都依赖于 Lombok，因此使它们相互依赖，导致紧密耦合。如果龙目岛关闭，整个项目将会陷入困境。

1.  但事实并非如此，因为 Lombok 附带了 DeLombok 工具，即使您必须从您的应用程序中删除 Lombok，您也可以轻松地从应用程序中删除它和依赖关系。
2.  因为所有对 Lombok 的依赖都是单向的，即类依赖于 Lombok，而 Lombok 不直接依赖于类，所以它没有创建真正的紧耦合。此外，编译后的代码最终是 Lombok 免费的，因此可以安全使用。

## 代码气味和对干净代码的影响

这很棘手，许多高级开发人员面临着初级开发人员不正确使用 Lombok 的问题，这使得 Lombok 更麻烦而不是更有利。但是有一些提示和技巧可以帮助正确使用 Lombok。其中一个诀窍是让你的后辈阅读这篇文章😂

有三种可能的情况，

1.  项目是一个建立良好的组织，其中代码质量是最重要的
2.  项目是一个快速面对的应用程序，其中完成是重要的，代码质量是中等的重要性
3.  项目是一个中等规模的应用，时间和质量都是至关重要的

对于案例 1，一个团队将有足够的合格成员来审查代码，从而在集成 Lombok 之后降低坏代码的总体风险，并且团队的其他成员最终会获得好的实践。

对于第二种情况，一点点糟糕的代码是可以接受的。团队最终会学会前进，逐渐提高整体素质。

对于第三种情况，它变成了对团队的召唤，这取决于团队成员以前在 Lombok 上的经验和应用程序的规模，您可以选择选择它或放弃它。

# 设置龙目岛

我会尽量让这个博客简短有趣。你可以在 Github 上找到详细的[实施项目以供参考。](https://github.com/mohammed-atif/medium-project-lombok)

## 将 Lombok 添加到项目中

1.  [将 Lombok 添加到 Maven 项目中](https://projectlombok.org/setup/maven)
2.  [将 Lombok 添加到 Gradle 项目](https://projectlombok.org/setup/gradle)

## 设置 IDE

1.  [月食](https://projectlombok.org/setup/eclipse)
2.  [IntelliJ](https://projectlombok.org/setup/intellij)
3.  [VS 代码](https://projectlombok.org/setup/vscode)

# 实现 Lombok

有一些非常好的文章详细解释了如何使用 Lombok，所以我将重点介绍如何正确使用它们。

首先是一些官方文件

*   [https://projectlombok.org/](https://projectlombok.org/)
*   [https://object computing . com/resources/publications/sett/January-2010-reducing-boilerplate-code-with-project-lombok](https://objectcomputing.com/resources/publications/sett/january-2010-reducing-boilerplate-code-with-project-lombok)

如果你也需要这篇文章来介绍详细的实现，请在评论中告诉我。

# 使用 Lombok 时的常见错误

## 不正确地使用`@Data`

`@Data`是最常用的注释，但它同样是有风险的注释。因为它也添加了`@Tostring and @EqualsAndHashCode`，所以当它和`Collections`或其他类似的项目一起使用时，会使你的代码变得危险

预期两个断言都将通过，因为我们正在创建两个不同的对象并将其添加到 set 中，但是由于`@Data`也添加了 EqualsAndHashCode，set 只插入一个值，因为对象的内容是相同的，因此第二个测试用例将失败。

许多开发人员不熟悉这个特性，最后花了几天时间来调试错误。

## 不正确地使用构造函数注释

*   在上面的同一个示例中，您可以注意到我已经添加了 AllArgsConstructor，但是没有将字段标记为 final。这个过程有两个缺陷，其他任何人都不能访问默认的构造函数，并且可以使用 setters 修改字段，这是一个糟糕的设计组合。
*   类似地，如果一个构造函数中有 5 个以上的参数，那么它就被认为是一个坏类，因为它很可能偏离了单一责任原则。当使用 AllArgsConstructor 或 RequiredArgsConstructor 时，开发人员很容易忽略这一点，代码很容易变得难以管理

# 一些良好做法

需要记住的事情不多，

*   Lombok 只是一个让你生活轻松的工具。它不为干净代码提供任何帮助或支持。
*   如果干净的代码和设计原则并不重要(相信我，在大多数情况下并没有听起来那么糟糕)，那么使用 Lombok 来简化开发过程是最好的选择。

说到这里，让我们看看一些使用 Lombok 的好的编码实践

*   仅使用绝对需要的功能
*   如有疑问，请参考文档。阅读文档只需不到 5 分钟，但识别和修复一个愚蠢的 bug 却需要 5 天

在这里找到详细的实现:[https://github.com/mohammed-atif/medium-project-lombok](https://github.com/mohammed-atif/medium-project-lombok)