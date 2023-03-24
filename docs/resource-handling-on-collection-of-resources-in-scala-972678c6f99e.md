# Scala 中资源集合的资源处理和 Java try-with-resources 的挑战

> 原文：<https://levelup.gitconnected.com/resource-handling-on-collection-of-resources-in-scala-972678c6f99e>

![](img/ab8bd7d39fcb83b6c239d6e86dc0c043.png)

[Tamanna Rumee](https://unsplash.com/@tamanna_rumee?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

Scala 生态系统中有很棒的资源处理库。我推荐以下内容作为快速开始。

1.  由齐奥([https://github.com/zio/zio](https://github.com/zio/zio))管理
2.  猫.效应.资源([https://github.com/typelevel/cats](https://github.com/typelevel/cats))

这些库优于其他流行语言中的许多库，尽管我发现主要是与 java `try-with-resources.`进行比较。这个博客的简单故事是，你可以利用上面提到的这些库的资源管理技术是 ***而不仅仅是 Java `try-with-resources`的闪亮替代品*** ，因为一些(嗯，许多)重要的语义在`try-with-resources,`中完全丢失了(下面将进一步详述)。因此，我发现在处理资源密集型用例时，这些库是必不可少的。这与基于纤程的并发模型一起，使我们能够并行获取数百万的资源，并针对中断和故障制定明确的释放策略。这些技术在 3-4 年前就可以在 Scala(当谈到 JVM 时)中使用了。在这个博客中讨论所有这些是不可行的，因此我将集中在一个代码片段上来给出这些事情的要点。

**注意**:谈到并发性，Project Loom 在 JVM 中随处可见，但是它并没有增加任何额外的东西，对于那些几年前就已经通过 ZIO/Cats 在 JVM 中使用基于纤程的并发模型的人来说，这并没有太大的惊喜。

## 进一步阅读的先决条件

1.Scala 语法知识
2。`cats.effect.Resource`中的知识

这个博客更多的是代码，对于那些已经通读过`cats.effect.Resource`的人来说可能有意义。

下面是博客的代码片段摘要，供那些想了解整个博客摘要的人参考。

对于那些想知道更多关于直觉和详细解释的人，请继续阅读“一个简单的例子”

```
val listOfResources: List[Resource[Connection]] = List.fill(100)(Resource.mk(acquire)(release))val resource: Resource[List[Connection]] = listOfResource.sequenceval dbResult: IO[DbResult] = resource.use(r => // use all connections or not, all connections will surely be closed)// or use it one by one, making sure conn are closed as and when they are used.val dbResults: IO[List[DbResult]] = 
  listOfResources.traverse(resource => resource.use(con => getDbResult(con))
```

## 一个简单的例子。

假设您正在从[亚马逊 S3](https://aws.amazon.com/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc&awsf.Free%20Tier%20Categories=categories%23storage&trk=ps_a134p000006gEalAAE&trkCampaign=acq_paid_search_brand&sc_channel=PS&sc_campaign=acquisition_ANZ&sc_publisher=Google&sc_category=Storage&sc_country=ANZ&sc_geo=APAC&sc_outcome=acq&sc_detail=%2Bamazon%20%2Bs3&sc_content=S3_bmm&sc_matchtype=b&sc_segment=476957268618&sc_medium=ACQ-P|PS-GO|Brand|Desktop|SU|Storage|S3|ANZ|EN|Text&s_kwcid=AL!4422!3!476957268618!b!!g!!%2Bamazon%20%2Bs3&ef_id=CjwKCAiAqJn9BRB0EiwAJ1SztQj-NWAzRMZqn-Qt0LF5ethVVWfF72GIFUxtrdyuGGYkVg4yxbeVghoC7VoQAvD_BwE:G:s&s_kwcid=AL!4422!3!476957268618!b!!g!!%2Bamazon%20%2Bs3) 目录下载文件，并且在您将这些文件下载到您的本地机器/应用程序内存中之后，您正在进行一些处理——可能会将内容加密到一个单独的临时文件中，然后将这些加密的文件(以及获取它们的元数据，如大小、校验和等)发送到另一个下游系统。

可以有多个文件，也可以只有一个文件，用户可以选择一个接一个地发送，或者一起发送，或者同时发送。除此之外，您必须确保您没有向下游发送重复的文件，这意味着您还需要管理一个状态。

这里是一个快速的起点。这只是一个小装饰，但在猫身上却是有用的装饰。

让我们创建一个可以从 S3 下载文件的`downloadAllFiles`函数。不是一个伟大的名字，但名字真的不重要。抛开代码中的细节，主要重要的是`downloadAllFiles`的签名。你能猜到它为什么返回类型`Resources`吗？

总之，`downloadAllFiles`不做任何事情——它只是返回`List[cats.effect.Resource[IO, TemporaryFile]]`(还不是资源[IO，List[TemporaryFile]]),然后被封装在`Resources`数据类型中。这在很多方面限制了用户。他们必须调用`use`(或 executeAll)来处理资源，这迫使他们在编译时考虑资源的获取和释放。

简而言之，真正有保证的资源释放。让我们看看`TemporaryFile`的实现。简而言之，它只是文件的`cats.effect.Resource` 。

这是一段冗长的代码，但大多数情况下，它们将成为应用程序代码中的黑盒，它们的使用将成为各种用例的首选。

我将浏览几个例子，这些例子清楚地揭示了用例。

## 示例 1:最明显的用法—一次发送一个文件，但保持一个状态

```
downloadAllFiles(...).use((_, a) => sendFile(a))
```

上述用法确保了以下几点:

*   所有文件将被单独下载，并将内容复制到本地重命名的文件中
*   在这个获取过程中的任何失败都将保证弹性行为——如果它同时打开了任何东西，它会捕捉并告诉用户正在关闭资源。
*   通过将这些文件传递给 sendFile(到某个目的地)来逐个使用它们。并保证单独清理与 **i** t 相关的所有资源。在某种程度上，这意味着，在执行相应的 sendFile 之前，资源不会被分配。
*   这禁止在需要传输之前下载多个文件(可能在 s3 路径中，具有适当的大小)。这在很大程度上避免了内存崩溃。此外，其中一个文件传输的失败会导致立即删除相应的资源。
*   你也可以保持一个执行状态，比如避免发送多个文件，或者累积文件的大小。由于这些文件在任何时候都不是一起存在的，所以我们需要保持一个状态。例如，下面给出的代码确保我们不会在没有将资源收集到内存中的情况下发送重复的文件，*允许即时清理*。

```
downloadFiles(...)
 .use(
    Resources.trackDuplicate(sendFile)(_.toString)(
      name =>  s"Duplicate file tracked. ${name}"
    )
```

## 示例 2:execute all—收集所有资源以获取文件列表，一起发送它们并一起释放资源。

```
downloadAllFiles(...)
  .executeAll
  .use(allFiles => sendAllFiles(allFiles))
```

*   在这种情况下，资源是累积的(并且不会启动任何进一步的清理/使用过程，直到所有资源都被获取)
*   也就是说，它允许您在以后的某个时间点将下载并重命名的文件集合**与**一起使用。`sendAllFiles`就是一个例子。
*   它保证在成功或失败执行`sendAllFiles`之后，释放与集合关联的**的所有资源**
*   一个实际的例子:对于使用 spark 的**JDBC 转移，我们可能需要整理文件的所有资源，只有在集体转移到 SQL 数据库后才关闭它们。**

## **示例 3:收集资源，但逐个发送文件(示例 2 的一个版本)**

**您也可以一起获取资源，但是单独使用每个资源(可能是一些复杂的过程),并保证在所有时间点释放所有资源。**

```
downloadAllFiles(...)
  .executeAll
  .use(allFiles => allFiles.traverse(a => sendFile(a)))
```

**整个想法相当于 Java 中的`descriptive` `try-with-resources`的`Composable`(版本)的`Collection`。但是这样的事情是不存在的。**

## **Java 中使用资源尝试的挑战**

**将上述资源处理的模块化解决方案移植回 Java try-with-resource 相当具有挑战性，但是，这是一个很好的尝试:**

*   **try-with-resources 有边缘情况(显然我听说它正在被修复),其中 try(var f = foo())可能对可能失败的资源做一些事情，并且它从未在内部包装在 try catch 中。那就是你的第一次资源收购可能会失败。在这种情况下，您可能会用另一个 try-catch(我不知道)来包装 try-with-resources**
*   **下一个挑战是，我们如何确保 try-with-resources 不会将资源的获取和释放以及它的使用混为一谈。你不能把它们分开，因为资源的“使用”应该伴随着它，这意味着我们如何在一个地方安全地获得资源，但在它们被用在其他地方后又让它们关闭。**
*   **另外，尝试并行获取资源并单独使用它们，但要确保在使用完所有资源后关闭所有资源。同时，如果任何一个使用失败，请确保释放所有其他资源。**
*   **如果你设法跳过了以上所有的障碍，那么阅读代码，看看我们是否能静态地证明所有的资源使用位置都与一个资源释放绑定在一起。**

**一旦这些都完成了，为了大家的利益，把你的结果贴在评论区吧:)**

**我希望，你能从我的写作中有所收获。如果你认为它太抽象，我会用更多的上下文和推理来进一步改进它。**

**我感谢 John De Goes 分享了他关于 Java 资源尝试的精彩想法，这启发了我写更多关于资源尝试的挑战。我建议通过以下途径浏览约翰的课程:https://patreon.com/jdegoes**

**干杯！**