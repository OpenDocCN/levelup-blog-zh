# 写干净提交历史的 4 个技巧

> 原文：<https://levelup.gitconnected.com/4-tips-to-write-clean-commit-history-51155f7f23b9>

## 饭桶

## 即使你是初学者，你也可以很容易地学习和理解。

![](img/9cd7d6fa8ed0856eaea9e292409e8903.png)

照片由[扬西·敏](https://unsplash.com/@yancymin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

A 你是否正在使用 git 并努力在干净提交中组织你的工作？当你开始处理你的任务时，你可以一次提交所有的变更。

我知道你的感受，因为我刚开始的时候就是这样工作的。随着时间的推移，我学会了写干净的提交历史，我很高兴与你分享。

# 动机—为什么要清除提交历史？

在我们深入这个话题之前，让我解释一下它的意义。以下是我学到的关于写干净的提交历史的东西。

1.  它在审核我们的拉取请求时为审核者提供了便利。当您很好地组织提交时，它会让您对主题有更好的理解。
2.  将代码组织成小的提交不会给评审人员带来压力。
3.  它有助于审阅者捕捉给定拉请求中的错误或错误。
4.  将代码组织和结构化为有意义的提交反映了您对特定问题的理解以及您试图解决该问题的方式。

以下是我的四点建议。每当我从事一项工作时，我通常会运用这些策略。请注意，没有硬性规定。

# 不要将重构与业务逻辑调整混为一谈

在同一个提交中混合重构和调整业务逻辑来支持新特性是一个常见的错误。请避免那样做。这使得评论者很难同时理解这两者。

我建议提交两次。第一次提交应该只包括代码重构，而不修改现有的业务逻辑。重构可能包括提高代码可读性或应用代码样式等。在第二次提交时添加或调整业务逻辑。

# 使配置更改可见

配置更改至关重要，需要仔细审查。通过在单独的提交中提交这些更改，使审阅者可以看到这些更改。

我已经在这里更详细地介绍了这个话题[。](https://medium.com/@anasanjaria/learned-the-concept-of-snapshot-as-a-code-contributor-663dd3f0ef2e)

# 从存储库中删除任何内容

我在这篇文章中读到

> 有经验的软件工程师都知道，最好的工程师在解决问题的时候往往是删除代码，而不是添加代码。

因此，当您删除任何代码时，请确保在单独的提交中完成。此外，确保在提交描述中记录并解释删除的原因。

通过这种方式，您不仅为将来记录了它，还将上下文传达给了审阅者。

# 实现后的框架

如果您正在处理一个具有复杂业务逻辑的特性，我建议将您的代码组织成较小的提交或多个拉请求。

这方面的一个建议是添加一个只包含业务逻辑而不涉及细节的 commit。这就是我所说的先有框架再有实现的意思。

考虑下面这个用 Scala 编写的例子。所以，这将是你的第一次承诺。

```
def process(email: String): Unit = {
  val user = getUser(email)
  val score = calculateScore(user.id)
  storeScore(score)
}def getUser(email: String): User = ???def calculateScore(userId: Int): Int = ???def storeScore(score: BigDecimal): Unit = ???
```

即将到来的提交将会用它们各自的单元测试实现单独的方法。

这只是一个例子，但是你可以把它想象成实现 DAO、服务和控制器层。如果你从上到下，也就是从控制器层到 DAO，上面的方法是有用的。

通常，我更喜欢从下到上，即从 DAO 到控制器层。

生活并不是一帆风顺的。一开始你可能会发现组织工作很有挑战性，但是熟能生巧。这并不像看起来那么有挑战性。

这里是我在工作中唯一使用的命令——我的 git cheatsheet。

```
- git check -b BRANCH-NAME
- git commit -m "TASK-ID your message here"
- git commit --amend
- git cherry-pick commit-id
- git rebase master // when I would like to edit or amend any specific commit then
- git rebase -i master
```

如果你有任何想讨论的问题或场景，请在评论区提问。我很乐意回答你的问题。

感谢阅读。

```
**Want to connect?** [Facebook](https://www.facebook.com/anas.anjaria.kh) | [LinkedIn](https://www.linkedin.com/in/anasanjaria/) | [Twitter](https://twitter.com/anasanjaria)**Subscribe to get my work directly into your inbox.** [https://medium.com/subscribe/@anasanjaria](https://medium.com/subscribe/@anasanjaria)
```

# 分级编码

感谢您成为我们社区的一员！更多内容见[级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在改造理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)