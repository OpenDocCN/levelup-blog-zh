# 使用 JavaScript 让你的浏览器说话

> 原文：<https://levelup.gitconnected.com/use-javascript-to-make-your-browser-speak-cf29483efeb7>

## 让我们来看看大多数浏览器都有的内置 API，让它们说话！

![](img/a15eea352f0fe8c1450fe4288153989c.png)

Ashim D'Silva 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

最近在开发一个个人项目时，我有了尝试让我的浏览器说出一个句子的想法。不知道如何着手做这件事，我开始上网。我在 MDN 网络文档上找到了我的答案。让我们看看如何使用普通的 JavaScript 将一些东西组合在一起，让我们的浏览器说话！

# 最简单的例子

让我们创建一个基本的函数，它可以接受我们想要说出的一个句子或一个单词，然后让我们的浏览器实际说出这个单词。我们将使用所有现代浏览器上都有的本地 API。

创建使用语音合成 API 的函数的基本说明

信不信由你，以上就是让大多数网络浏览器说出一个句子所需要的全部内容！让我们讨论一下这里发生了什么。

我们正在创建一个`speak()`函数，它将我们的句子作为参数。我们创建了一个`utterance`对象，它本质上是一个语音请求对象，包含了必须说什么以及如何说的所有相关数据。这个对象被传递给`speechSynthesis`对象上的`speak()`函数，它将产生声音！

# 自定义速率和音高

让我们举一个稍微复杂一点的例子，试着改变单词的语速和音高——因为出于某种原因，这是我们需要的。

在上面的例子中，我们通过在参数签名中添加`pitch`和`rate`参数构建了前面的代码片段。在我们创建了我们的`utterance`对象之后，我们可以直接在对象上设置这些属性。不幸的是，目前还没有通过构造函数或 setter 方法设置这些属性的方法。

`pitch`是一个介于 0 和 2 之间的浮点值——尽管这可能会受到所使用的引擎或语音的限制。音高默认为 1。

`rate`是一个介于 0.1 和 10 之间的浮点值，默认为 1。

我们的浏览器现在应该以正常的速度和音调说出下面的句子，如果我们用一个简单的句子调用上面的片段，比如下面的

```
speak('Hello there - General Kenobe', 1, 1)
```

![](img/79632a974e4efcc1befb15c44970be2b.png)

由[迈克·刘易斯 HeadSmart 媒体](https://unsplash.com/@mikeanywhere?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 音量呢？

我们也可以像调节音高和节奏一样调节音量。让我们快速看一下这个实现—

修改前面的片段，我们现在传递我们想要用于演讲的音量。音量是一个介于 0 和 1 之间的浮点值，如果没有指定值，默认为 0.5。

不幸的是，目前还没有办法通过构造函数或任何 setter 方法来设置卷。

当我们现在用一个简单的句子和 0 到 1 之间的音量调用我们的`speak()`函数时，我们将听到我们的结果！

# 需要注意的功能

在`speechSynthesis`对象上有一些函数可以派上用场。我们能够暂停、恢复甚至取消这个过程。让我们快速看一下—

上面的片段将开始说我们的句子，然后演讲将暂停，恢复，最后取消。您可以通过实例化话语并调用`speak()`，然后在浏览器的开发工具中暂停、恢复和取消话语，来自己测试一下。

我们还可以通过直接检查`speechSynthesis`对象的`paused`属性来检查演讲是否被暂停。这将返回一个布尔值，表明讲话是否已经暂停。

```
window.speechSynthesis.paused // Boolean
```

如果有一大堆句子需要说呢？还有一个检查来确定是否有任何未决的话语等待被说出。这也将返回一个布尔值，指示是否有未决的话语。

上面的代码片段实际上将这两个话语排队，并按顺序播放它们。当访问`pending`属性时，返回的值将是`true`,因为还有第二个话语等待开始。

注意——如果你只有一句话，那么 pending 的值将保持为`false`,因为没有其他东西排队等待播放。

# 听事件

使用这个 API 时，我们可以监听一些有用的事件。让我们看一看——

上面的代码片段展示了一些在`utterance`对象上可用的有用事件。

为了确定`speechSynthesis`对象的变化，需要检查属性，就像我们在前面的段落中所做的那样。

![](img/c6136e7370b10c76f5952caf1778b213.png)

泰勒·利奥波德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 最后一个结论！

这应该给你足够的信息，开始在你的浏览器中玩语音游戏。您现在可以开始、停止和暂停讲话，以及调整讲话的速度、音调和音量。

感谢您的阅读。我希望你喜欢这篇文章，并学到了一些东西。如果你碰巧有任何反馈、批评或贡献，请随意写在下面的评论区。

再见了。

# 参考

如果您希望了解更多或者开始在自己的项目中实现语音合成，这里有一些可能会引起您兴趣的参考资料！

[MDN 语音合成](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisUtterance)
[MDN 语音合成](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis)