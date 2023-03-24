# 如何在 SwiftUI 中保存内容复杂的 ReferenceFileDocument

> 原文：<https://levelup.gitconnected.com/how-to-get-a-referencefiledocument-with-a-complex-content-to-being-saved-in-swiftui-1376cccca88f>

![](img/c062007cd036f7e79501a282ccfdb5ee.png)

约翰·洛克伍德在 [Unsplash](https://unsplash.com/s/photos/road?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Xcode 提供了创建基于文档的应用程序的可能性。在这种情况下，您将得到一个项目设置，其中一切都运行良好。

然而，如果您不想要基于文档的应用程序，但是仍然要使用文档——那么该怎么办呢？根据我的经验，这有点困难，我想和你分享一些结果。

从“普通”应用程序项目开始时，Xcode 创建的 SwiftUI 框架如下所示:

`WindowGroup("") { ContentView() }`

对于基于文档的项目，会有如下代码:

`DocumentGroup(newDocument: SomeDocument()) { file in
ContentView(document: file.$document) }`

在这种情况下，Xcode 会创建一个默认的`FileDocument`类型的文档并使用它。到目前为止，一切顺利。

现在，我想要两件事:(1)不要只使用一个`FileDocument`，而是一个`ReferenceFileDocument`。有什么区别？让我们先看看`ReferenceFileDocument`的文档:

> ///一个文档模型定义，用于将引用类型文档序列化到
> ///以及从文件内容。
> ///符合“ReferenceFileDocument”应该是线程安全的，并且
> ///反序列化和序列化将在后台线程上完成。

现在`FileDocument`:

> ///一个文档模型定义，用于将文档序列化为文件
> ///内容。
> ///符合“FileDocument”要求值语义和线程安全。
> ///序列化和反序列化发生在后台线程上。

对于我的用例来说，`ReferenceFileDocument`更适合，因为它将包含一个根结构，下面有一些依赖的东西。这整个树应该简单地存储为一个带有一些文本 JSON 内容的定制文件(目前)。此外，正如你在谷歌上找到的一些文章中读到的，它提供了自动撤销/重做机制。这对我的用例非常有用。

所以，我创建了一个自定义`class ProfileDocument: ReferenceFileDocument, Equatable {...}.`

我添加了一个`@Published var content: ContentType`，添加了所需的方法，通过 SwiftUI `.environment`扩展将文档添加到视图树，并开始修改内容，例如，通过 SwiftUI `Form` s

并且我把一些`debugPrint`语句放到了在修改后应该编写文档时我期望被调用的地方。文档是如何讲述的？

你猜怎么着？什么都没发生。文件的内容变了，但什么也没写，一行也没写。它只是没有被触发。

表单中提供了撤消和重做功能，但没有发生写操作。

假设问题总是出现在键盘前，我试图了解更多细节。而且我发现问题的关键是`UndoManager`。这在文档的代码中没有出现。

但它就在那里。隐含地，在背景中。如果应用程序是作为基于文档的应用程序创建的。但事实并非如此。

在我的情况下，我必须触发亲爱的经理。所以我为文档创建了一个扩展，在那里我添加了一个`undoablyPerform`操作:

现在，当改变内容时，可以很容易地触发`UndoManager`:

(我使用的是可组合架构，但是我希望这里发生的事情的核心应该是清楚的。)

因此，我的期望是:一旦注册了一个更改(或者至少在一段有限的时间之后)，现在将触发文档的保存。

没有。磁盘上没有文件。不存在的文件中没有内容。没什么。空虚，我的目光落在那里。

在这一点上，我有点失望。首先，因为我没有充分研究这个问题。第二，因为苹果的文档在这方面真的很差。第三，因为我找不到任何其他真正能够帮助我的资源。第四，因为我发现的解决方案不太适合我的应用程序的架构，并且在大多数情况下采用失败。

所以我决定用`Combine`。在文档中，我们有一个存储内容的变量，这个变量就是`@Published`。所以，我们很容易观察到它。因此，我创建了一个小的`observe()`函数，观察内容，并在一段时间后调用一个显式的`save()`函数进行去抖动:

现在，在每次使用撤销注册的更改之后，文档被正确地写入磁盘。

我的经验是，根节点下的内容树的变化没有被识别，所以文档看起来没有被修改。通过执行显式撤销注册，这似乎改变了行为，但还不够。如果没有撤销注册，但有观察，则不会触发保存。有了撤销注册，但没有观察，就不会触发保存。只有两种措施结合起来才是成功的。

然而，由于这显然不是最好的，也可能不是想要的方式，我真的很想知道一些提示。所以请随意评论这种方法。

感谢您的阅读，希望有些信息对您有用。