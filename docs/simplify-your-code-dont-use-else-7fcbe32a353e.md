# 简化你的代码:不要用“else”！

> 原文：<https://levelup.gitconnected.com/simplify-your-code-dont-use-else-7fcbe32a353e>

## 将“不要使用 else”原则应用到实际例子中

## 如何避免使用 else 关键字以使代码更易于阅读和维护的真实例子

![](img/efc0a83dba19b74184c5c472cfae0348.png)

伊利亚·巴甫洛夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

作为开发人员，我们花更多的时间审查和阅读代码，而不是编写新功能和新内容。这意味着优化我们代码的可读性和可维护性将导致生产力的整体提高。考虑到这一点，有几个指导方针可以帮助我们实现这个目标。这些指导方针被称为目标健美操，是由杰夫·贝在《思想作品选集》一书中提出的。你可以找到这本书的链接，以及一篇解释它们是什么的文章和 Nick Chapsas 关于同一主题的视频，如果你想了解更多的信息。其中描述的原则之一是不要使用 else 关键字，这也是我在本文中关注的地方。

人们偶然发现的许多例子都非常简单，与现实生活中的场景没有真正联系，这使得人们更难理解为什么这是一件好事。在本文中，我将展示一个真实的例子，以及如何应用一些与 if 语句相关的重构技术来使您的代码更容易理解

# 形势

每个需要实现的后端服务都需要某种身份验证，通过 API key 进行身份验证是最简单的方法之一，同时也非常有效。在这种情况下，API 密钥是通过一个头提供的，下面的要点描述了验证 API 密钥被接受的过程。找到以下要点:

在这段代码中，我们所关心的积极的场景是当头部带有一个值并且这个值是我们在设置中识别的值时。然而，从代码中可以看出，这种情况并不明显。让我们来看看这段代码的几个问题:

1.  有两个连续的 if，第一个包含了几乎所有的逻辑。这打破了目标健美操的另一个原则，即每种方法只能有一级缩进
2.  很难理解哪一个与哪一个“如果”相关，因为在那些“如果”中有很多逻辑

让我们一次研究一下每个问题的解决方案。

# 解决方案 1——提前退出

我们可以通过应用早期退出原则来简化这段代码。在这种情况下，因为我们不在乎处理任何没有在头中提供的 API 键的东西，我们可以在那个时候返回。请看这里的例子:

通过退出 first，我们已经解决了两个问题:

*   现在，在没有提供 API 键的情况下，我们该做什么就更清楚了
*   现在整个方法只有一级缩进

这段代码还有另一个问题，那就是 else 关键字的用法。让我们现在解决这个问题

# 解决方案 2—删除 else 关键字

因为无论它是负的还是正的，我们都不需要处理更多的逻辑，所以一旦执行了正的场景，我们就可以返回。请参见下面的重构示例:

如您所见，这个想法非常简单，只需在 if 块中添加一个 return 语句。这确保了遵循更线性的逻辑以及一级缩进。

# 结论

像对象柔软体操这样的指导方针很容易理解，但是正如你在这个例子中看到的，它们非常强大，可以使你的代码更具可读性，并确保下次你必须通读这段代码时，你会更容易理解它。

如果你想讨论，请在这里随意发表评论，或者在这些社交网络上找到我:[linktr.ee/joaonadais](https://linktr.ee/joaonadais)。

下一场见！

# 资源

思想作品选集:https://pragprog.com/search/?q =思想作品选集

目标健身操:[https://blog . avenue code . com/object-benjamins-principles-for-better-object-oriented-code](https://blog.avenuecode.com/object-calisthenics-principles-for-better-object-oriented-code)

尼克·查普萨斯实物健美操视频:https://www.youtube.com/watch?v=gyrSiY4SHxI

要点:

*   [https://gist . github . com/nada is/d 8 aa 732 C5 fdef 43448 bb 71 a7d 458 DC 15](https://gist.github.com/nadais/d8aa732c5fdef43448bb71a7d458dc15)
*   [https://gist . github . com/nada is/e 235 cbde 211 add 3d 0d 0 c 997 fef 465936](https://gist.github.com/nadais/e235cbde211add3d0d0c997fef465936)
*   [https://gist . github . com/nada is/14 c 38 f 78d 8782 AFC 1 a4 ee 279 a 34 f 7470](https://gist.github.com/nadais/14c38f78d8782afc1a4ee279a34f7470)