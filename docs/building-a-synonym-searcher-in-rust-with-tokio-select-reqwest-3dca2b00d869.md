# 构建同义词搜索器🔍在 Rust with tokio 中，选择并请求 west

> 原文：<https://levelup.gitconnected.com/building-a-synonym-searcher-in-rust-with-tokio-select-reqwest-3dca2b00d869>

作为开发人员，给事物命名是一项众所周知的任务，这是一项持续的斗争。我经常打开两个或更多的浏览器标签，以便找到我要找的单词。然后有一天我突然想到。

> 终端里有个同义词搜索器不是很好吗？。

没有时间可以浪费，所有其他的副业都在瞬间被放弃，我通往一个更好命名的世界的道路开始了。

![](img/007f4fca5a3b93ac41259352f313baf3.png)

照片由[托尔加·乌尔坎](https://unsplash.com/@tolga__?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

## 目标🥅

从弄清楚我们想要实现什么开始总是好的。本着努力完成这一点的精神，让我们尽量保持范围相对较小。
命令行工具不必比这更复杂:

```
$ cargo run <SOME-WORD>suggestion 1
suggestion 2
suggestion 3
...
suggestion 10
```

## 寻找合适的词语💬

那么我们在哪里可以找到同义词呢？

我通常用谷歌搜索我想找的同义词，然后点击进入一些网站。我意识到我通常会上这三个网站:[thesaurus.com](https://www.thesaurus.com/)、[yourdictionary.com](https://www.yourdictionary.com/)和[merriam-webster.com](https://www.merriam-webster.com)。

一个简单的解决方案是抓取这些站点，然后合并结果。但是为了找到我们想要抓取的页面，我们需要一种可靠的方法来查询每个站点。

经过一番调查，发现每个网站都使用 URL 中搜索到的单词作为路径参数。太完美了:)

要查找单词“汽车”的同义词，每个站点的 URL 应该是:
`[https://thesaurus.yourdictionary.com/car](https://thesaurus.yourdictionary.com/car)`
`[https://www.thesaurus.com/browse/car](https://www.thesaurus.com/browse/car)`
`[https://www.merriam-webster.com/thesaurus/car](https://www.merriam-webster.com/thesaurus/car)`

## 刮掉🛣️的路

为了从每个网页中提取数据，我们首先需要一种方法来发出获取网页的 HTTP 请求。为此，我将使用 HTTP-crate:[*req west*](https://docs.rs/reqwest/0.11.0/reqwest/)。

获取网页后，我们需要某种方法来解析和选择 HTML 中的数据。这可以用 [*选择*](https://docs.rs/select/0.5.0/select/) 板条箱来完成。

我就不赘述 [*选择*](https://docs.rs/select/0.5.0/select/) 板条箱是如何工作的了，不过我以前写过关于这个库的文章，所以如果你想要的话，可以在这里[阅读一下。](https://medium.com/swlh/digging-out-the-news-with-rust-b4975c91be74)

## 寻找关键的🗝️

下一步是找出如何在每个网站中找到重要的数据。这意味着弄清楚 HTML 的结构，这样我们就可以可靠地遍历它，每次都能找到相同的元素和数据。

下面是一些选择器语法，显示了每个网页的一种可能的解决方案。

**韦氏词典** : `.syn-list .mw-list > li > a`
**词库** : `[id="meanings"] li`
**你的词典** : `.synonym-link`

## 去拿🐶

好了，让我们开始这个敲打键盘的旅程吧！

要克服的第一个障碍是从每个网站获取内容。我们如何用 *reqwest* 来做这件事？

机箱分为两部分:异步和阻塞。尽管我们已经承诺在 *Tokio* 中用 async 来做这件事，我们将从阻塞客户端开始，稍后再引入 async。

获取网站并提取其主体的代码非常简单。
`let body = reqwest::blocking::get("http://some.url")?.text()?;`

你可以在这里阅读更多关于`get`功能[的内容。](https://docs.rs/reqwest/0.11.0/reqwest/blocking/fn.get.html)

在*中选择* crate-world，你创建一个`[Document](https://docs.rs/select/0.5.0/select/document/struct.Document.html)`，它代表你想要搜索和提取的 HTML。这是真的，与我们将要合作的网站无关。所以我们可以从创建一个获取网站的函数并将其转换成一个`[Document](https://docs.rs/select/0.5.0/select/document/struct.Document.html)`开始。

## 挑剔的⛏️

我们已经知道在每个网页的哪里寻找我们的数据，现在我们只需要记下适当的*选择*代码。让我们为每个网站创建一个单独的功能。

该函数将接受一个`&str`并返回一个`Vec<String>`。

以下是三种实现方式:

这三个函数都使用这个小助手函数从一个*选择* `[Node](https://docs.rs/select/0.5.0/select/node/struct.Node.html)`对象中提取文本。

## 链条链条⛓️

有了这三个独立的网页抓取功能，我们最终得到了三个填充了`Strings`的列表。第一步是尝试将所有列表合并成一个大列表。

通过将我们的`Vec<String>`值转换成`[Iterator](https://doc.rust-lang.org/std/iter/trait.Iterator.html)`实例，这使我们能够使用`[.chain](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.chain)` 方法。就这么简单:)

## 🎖️新秩序

如果我们使用我们的合并结果，我们会注意到同义词的排序会有点混乱。

我们正在使用的网站似乎会在列表顶部显示最相关的同义词。同样，更牵强的单词会出现在列表的底部。

我们当前的解决方案将显示从`res_1`开始的所有单词，从最相关到最不相关排序，然后*显示`res_2`的排序结果，依此类推。*

我们需要一种跟踪同义词出现顺序的方法。

因为我们在获取函数中使用迭代器，所以我们可以使用`.enumeration`方法。这将是一个小切口，并给出什么后。该方法会将我们的最终结果从`Vec<String>`更改为`Vec<(usize, String)>`。

这里有一个例子:

对于单词“hello ”,我们现在得到这样的结果:

```
(0, greetings)
(1, hi)
(2, howdy)
(3, ...)
...
```

现在我们可以对枚举值进行排序，其中最小值等于最佳匹配:`result.sort_by(|(a, _), (b, _)| a.cmp(b));`

## 复视觉🐫

我们现在可以组合我们的结果*和*排序我们的组合列表，但是另一个问题出现了…

重复的呢？最有可能发生的情况是三个不同的提取结果可能包括相同的单词。有时单词在相同的枚举位置，有时不在。

```
Source 1
 (0, Wonderful)
 (1, Perfect)Source 2
 (0, Brilliant)
 (1, Wonderful)Source 3
 (0, Wonderful)
 (1, Nice)
```

在这种情况下，单词“精彩”根据来源 1 和 3 排在第一位，但根据来源 2 排在第二位。

可能有一些聪明的解决方案来解决这个问题，但让我们保持简单:)

我想到的第一个解决方案是将每个单词分组，并对它们的枚举值求和。在本例中，“精彩”的得分为`1=(0+1+0)`。

让我们使用一个`HashMap<String, usize>` 来分组计算。

## 多任务⚙️

我们差不多完成了。是时候加入期待已久的超级英雄 Tokio 了。Tokio 是一个异步运行时，它允许我们轻松地同时表达和运行事物。

我们的代码现在的一个问题是我们一个接一个地发出 HTTP 请求。在 *Tokio* 和来自 *reqwest* 的异步 [get](https://docs.rs/reqwest/0.11.0/reqwest/fn.get.html) 函数的帮助下，这些请求可以同时运行。

好的方面是它不需要我们做太多的工作来改变它。下面是将要发生的事情的清单:

*   让我们的`main`函数与`#[tokio::main]`宏异步。
*   将我们的三个`fetch_*`函数转换为异步
*   将我们的`fetch_website`转换为异步，并使用来自 *reqwest* 的异步`get`
*   使用 *Tokio 的* `join!`宏调用每个`fetch_*`并等待全部完成

…下面是一些代码示例:

*主要功能签名:*

*取文件功能:*

*获取其中一个同义词站点:*

*使用 Tokio 的* `*join!*` *宏等待所有异步功能完成:*

## 波兰✨

组合结果的列表可能会很长，所以让我们通过选择前 10 个同义词来限制输出。最后，输出结果。

## 这是一个总结🌯

运行`cargo run improve`会给你一个同义词列表:)

在测试的时候，我意识到结果有时会让人感觉有点偏差。因此，一些改进肯定是可以实现的，但是，如果一切都这么简单，那就有点无聊了！

这里有很多未处理的错误，但是为了这篇文章不至于没完没了，我将把它留给你或以后的文章去深入研究。

以下是同步和异步示例版本的链接:

*   [同步版本](https://github.com/fabrlyn/sandbox__syno/blob/sync-version/src/main.rs)
*   [异步版本](https://github.com/fabrlyn/sandbox__syno/blob/async-version/src/main.rs)

谢谢你在我晚上闲逛的时候跟着我。

*/罗伯特*