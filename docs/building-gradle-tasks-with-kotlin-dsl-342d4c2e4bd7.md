# 用 Kotlin DSL 构建梯度任务

> 原文：<https://levelup.gitconnected.com/building-gradle-tasks-with-kotlin-dsl-342d4c2e4bd7>

## 理解 gradle 和任务生命周期的好处。

![](img/66d6c3ca8c2497c7ebe697946eed44c9.png)

来自[gradle.org](https://gradle.org/)

你知道是什么让软件工程成为现代世界最酷的职业之一吗？**我们造东西**，而且我们造东西**快**——或者至少**那是我们想要的**。不幸的是，软件工程师经常被日常任务耽误，比如配置、构建和运行他们的应用程序。Shell 脚本可能变得特定于团队，如果使用不当，构建自动化工具可能会变得很麻烦。

首先，我们将强调用 Kotlin 领域特定语言(DSL)实现 Gradle 任务的基本方法。在深入 Gradle 任务系统之前，我们将介绍 Gradle 对以前的自动化工具(如 Ant)所做的改进。

> *这篇文章可以带你从没有梯度知识到能够建立你自己的功能任务。*

**注意:**本教程不包括安装 gradle 或初始化项目等基本步骤。你可以在 [Gradle 安装文档中找到更多信息。](https://gradle.org/install/)

# 格拉德 vs 蚂蚁

Gradle 将自己定位为一个非常灵活的构建自动化工具，具有可重用的任务结构，用于高效的构建过程。

不相信我？在实现 Gradle 任务之前，让我们来比较一下它的语法和它曾经的前身——Ant。一个简单的**Ant 构建文件可能是这样的:**

都是 XML 格式的，恶心！正如您所看到的，标准的 Ant 构建文件极其冗长，这为可读性付出了巨大的代价。让我们看一个简单的 Gradle 构建文件:

即使你从未见过 Gradle，你也可以从这个例子中得到大概的意思。`plugins`块包括附加功能的包，`dependencies`包括你的项目需求，`repositories`告诉你从哪里得到`dependencies`，而`task`是一些要执行的动作。很干净，是吧？

# 用 Kotlin 构建 Gradle 任务

Gradle 使用两种语言作为构建脚本，Groovy 和 Kotlin。 **Groovy** (上面的前一个例子)已经成为**标准**很多年了，网上有很多例子。Kotlin 是一个较新的附加功能，作为一种**类型语言**有额外的好处。这允许对您的任务对齐进行更多的控制，以及对更高效开发的 IDE 支持。

用于 Gradle 的 Kotlin DSL 与一些 Groovy 语法成对工作。Gradle 保留了 Groovy 的一些关键字，同时允许完整的 Kotlin 编程能力。让我们看看代码。

## 运行简单的 Gradle 任务

这里有一个用 Kotlin 写的简单的 Gradle 任务。如果您在 IntelliJ 中启动了一个新的 Gradle 项目，您可以删除根目录中的`build.gradle`并用`build.gradle.kt`替换它——其中扩展名代表 Kotlin 脚本。现在，您可以用不到 5 行代码创建一个基本的 Gradle 任务:

这里，我们使用`tasks.register()`向 Gradle 注册一个任务，同时给该任务命名为`“hello”`。然后我们调用`doLast{}`，这是 **Gradle lifecycle** 的一部分，我们将在接下来展开。除此之外，我们称之为简单的打印语句。

我们可以通过在项目的根目录下打开一个终端并运行`gradle hello`来执行上面的任务。

```
$ gradle hello> Task :hello
Hello GradleBUILD SUCCESSFUL in 510ms
1 actionable task: 1 executed
```

您还可以运行命令`gradle tasks`,它将列出项目中所有可用的任务及其基本描述。

## 运行梯度构建

如果你有一个标准构建的 Java 或 Kotlin 项目，你可以运行`gradle build`来运行一组预定义的任务，这些任务内置于 Gradle 中，以便编译、构建和测试你的项目。这里有一个用 Kotlin 编写的简单 hello world 示例:

如果您第一次运行 gradle build，您将得到类似如下的输出:

```
$ gradle buildBUILD SUCCESSFUL in 645ms
3 actionable tasks: 3 up-to-date
```

我们可以运行带有`-i`标志的命令来获得更多信息。这将向我们显示所有正在运行的任务，但是这里我们只检查`compileKotlin task`。如果您已经运行了`gradle build`，您会看到任务被缓存了。

```
$ gradle -i build. . . . . .> Task :compileKotlin UP-TO-DATE
Build cache key for task ':compileKotlin' is f8831c991f078512392cf3ac3fc0c63e
Skipping task ':compileKotlin' as it is up-to-date.
```

如果我们将 Kotlin `main()`方法改为打印 Hello World，那么再次运行`gradle -i build`将显示文件已经更改，我们将运行 compileKotlin 任务。

```
$ gradle -i build. . . . . .> Task :compileKotlin
Build cache key for task ':compileKotlin' is 374fd90fedd6a3acea0979619cc2adb4
Task ':compileKotlin' is not up-to-date because:
  Input property 'source' file /Users/israelmiles/Sandbox/gradle-kotlin-demo/src/main/kotlin/hello.kt has changed.
```

> 这个缓存特性是 Gradle 如此受欢迎的部分原因。 *如果一个任务已经被证明是成功的，我们就不需要再运行它了。Gradle 将存储任何生成的 jar 文件或依赖项，以便在以后的任务中使用。这使得* ***保持构建时间最小的高效过程成为可能。***

## 这是生命周期的循环

Gradle build 文件生命周期有三个阶段— **初始化、配置和执行**。初始化阶段可以在`settings.gradle.kts`进行。配置阶段在任务内部或外部的构建脚本中。执行阶段发生在`doFirst`或`doLast`块的最后。

您可以将关键字`doFirst`和`doLast`视为添加到 Gradle 构建序列的命令的**队列** **的**前端**或**后端**。让我们来看看实际的生命周期。首先向`settings.gradle.kts`添加一条打印语句(如果文件不存在，只需将其添加到项目根文件夹中):**

```
rootProject.name = "gradle-kotlin-demo"
println("This is executed during the initialization phase.")
```

现在我们可以在`build.gradle.kts`中调用配置和执行阶段。可以立即调用配置阶段。每次您调用`doFirst`时，您都将该命令添加到队列的最前面。如果您调用`doLast`，我们会将该命令添加到队列的末尾。

运行 gradle 倒计时任务将向我们展示我们的生命周期是有序的:

```
$ gradle countdown
This is executed during the initialization phase.
This is the configuration phase> Task :countdown
This is the execution phase
4
3
2
1BUILD SUCCESSFUL in 1s
1 actionable task: 1 executed
```

## 建立任务相关性

随着您的项目变得越来越复杂，您可能会遇到这样的情况:在调用后续任务之前，您**需要完成某些任务。这可以通过使用`dependsOn()` Groovy 关键字告诉 Gradle 哪些任务需要在当前任务之前执行来轻松实现。这里有一个例子:**

因此，我们可以单独运行独立任务——但是如果我们执行依赖任务，我们将首先调用独立任务。

```
$ gradle dependent
This is executed during the initialization phase.> Task :independent
This is the independent task> Task :dependent
This is the dependent taskBUILD SUCCESSFUL in 1s
2 actionable tasks: 2 executed
```

> *这些只是*****的语法******的渐变任务。*** *进一步的配置可以让您的任务* ***扩展您的项目的功能*** *除了* ***自动化项目构建流程*** *。在这里，我们已经开始了解使用 Kotlin DSL 的 Gradle 任务的基础，这是将 Gradle 作为您自己项目的一部分的第一步！******

**这只是 Gradle 生态系统的一小部分。Gradle **plugins** 也可以用来将预构建的任务功能添加到您的项目中。你也可以创建你自己的**定制插件**来进一步重用。不仅如此，Gradle 还有很棒的**依赖管理**、**多项目构建配置**以及管理你的项目**测试过程**。**

**如果你想阅读更多关于这些话题的内容，请在下面留下评论！如果你喜欢这篇文章的任何部分，或者想知道更多的细节，我也非常希望听到你的想法。:-)感谢阅读！**