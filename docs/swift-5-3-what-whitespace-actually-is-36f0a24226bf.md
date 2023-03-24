# Swift 5.3:空白到底是什么

> 原文：<https://levelup.gitconnected.com/swift-5-3-what-whitespace-actually-is-36f0a24226bf>

## 深入探究该语言最基本的构件。

在这个故事中，我想谈论一些从来没有人谈论过的事情，每个教程只是简单地跳过，基本上每个人都认为理所当然，没有给它第二个想法。

![](img/a81b64f8564d1890095fd2873cc1650a.png)

杰斯·贝利在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

然而，我确实认为，要想在 Swift 编程方面做得更好，在某些时候你需要从最基本的想法开始，并从那里继续前进。您可以在开始时打破这种模式，以尽可能快的速度开始编写代码，但是到了某个时候，您会希望进入更专业的层次并回到基础。

在这个平衡点上，你不仅开始考虑如何*使用*语言来创建软件，你也开始考虑**语言本身如何工作**，当你写东西时它意味着什么，解析器和编译器如何处理明文源文件以将它们转换成可执行代码，以及**利用这些知识来编写更好的代码**。

让我们从空白开始。

> **您创建的每个 Swift 文件中都存在空白。**没有空格，就没有有效的 Swift 代码。解析器将无法理解我们的任何文件。

空格分隔源代码中的其他标记。的确，空白通常会被忽略，因为多个空白标记被视为只有一个空白标记。所以不管你是按了一次、两次还是 10 次空格键，对于 Swift 来说，它只是一个空白令牌。这当然不适用于字符串文字，因为它不是空白的一部分。

[Swift 语言参考是这样定义空白的](https://docs.swift.org/swift-book/ReferenceManual/LexicalStructure.html#ID411):

```
*whitespace* → whitespace-item whitespace(*opt*)
```

您可以这样理解: *whitespace 由任何 whitespace-item 元素组成，可选地后跟更多的 whitespace* 。

你注意到什么了吗？**对，是递归定义！**

通过检查什么是`whitespace-item`标记，我们现在可以更深入地了解什么是空白。我们可以:

*   `line-break` — U+000A ( `\n` —旧说法“向下前进”)或 U+000D ( `\r` —旧说法:“返回当前行不向下移动”)或 U+000D 后接 U+000A ( `\r\n`)。
*   `inline-space` — U+0009 或 U+0020 —这只是您的普通旧空间，多个空间被视为一个`inline-space`项目。
*   `comment`—`// comment`被 Swift 解析器视为空白。
*   `multiline-comment`—`/* comment */`也被 Swift 解析器视为空白。
*   此外，U+0000(例如在 C 风格的字符串中使用空终止符来标记它们的结尾)、U+000B ( `\t`)和 U+000C ( `\f` —换页符——过去这意味着“前进到下一页”,现在不使用了)也被视为空白。

所有这些都意味着，要在 Swift 中分隔其他令牌，我们可以使用上述任意组合，只有一个限制:必须有至少一个**`**whitespace-item**`。不管你使用了空格、`\n`、注释还是以上的组合，只要你至少有一个。**

**![](img/1d05e870181828f0d41fb3b65493d22d.png)**

**劳拉·戴维森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片**

**在这个过程中我发现了一些东西，是我意想不到的。例如，这是将编译和运行的有效 Swift 代码:**

```
**var** str/* multiline comment is treated as whitespace */= “Hello, world”
```

**对于 Swift 解析器来说，这个`/* multiline comment is treated as whitespace */`和`var`和`str`之间的空格是一回事。**

**希望你喜欢这个故事！下一次我将谈论评论——那里有许多令人兴奋的东西可以发现。**