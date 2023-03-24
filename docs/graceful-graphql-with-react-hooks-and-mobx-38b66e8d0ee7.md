# 优雅的 GraphQL，带有 React 钩子和 MobX

> 原文：<https://levelup.gitconnected.com/graceful-graphql-with-react-hooks-and-mobx-38b66e8d0ee7>

## GraphQL 遇到 React Hooks，结成强大的友谊。他们也应该连接 MobX！

[GraphQL](https://graphql.org/) 肯定越来越受关注[越来越受关注](https://2018.stateofjs.com/data-layer/graphql/)这是当之无愧的。如果您尝试过，并且想返回从多个端点获取数据，请举手。不用担心，我想我知道答案😎。

谈到 GraphQL，Apollo 是最常见的解决方案，我甚至敢说，如果没有它们，我们就不会像现在这样发展 GraphQL。他们建立的社区和技术值得拥有👏。

当 [React Hooks 走进光](https://reactjs.org/blog/2018/11/13/react-conf-recap.html)的时候，距离非官方项目 [react-apollo-hooks](https://github.com/trojanowski/react-apollo-hooks) 的发布也只是几天的事情。我立刻跳上了马车，并且从那时起就一直享受着它。

只是为了好玩，比较一下这三种执行突变的方法。

好了，你玩够了，现在我们继续。这篇文章是关于完全不同的东西…查询。哦，我已经答应了一些 MobX，不是吗？

![](img/76b5243ae59a292511292e42e20a1aed.png)

照片由 [AK N Cakiner](https://unsplash.com/@akin?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

运行查询对于 GraphQL 来说至关重要。没有你珍贵的数据你会怎么办？我只关注钩子方法，因为有更好的资源描述其他两种方法。

还没什么特别的。有些人可能会反对数据获取不应该在 UI 组件内部完成。当然，如果你喜欢，请随意分开它。让我们把重点放在文章更紧迫的问题上。

有一个变量通过 props 传入并传递给查询。当值改变时，查询被重新执行。到目前为止这很酷，并且当你需要更新它的时候，这绝对比使用`componentDidMount`来执行 fetch 和后续的恶作剧要好😜。

现在假设变量`tag`不容易到达。也许是在一些与显示实际图像无关的设置面板中。你很可能不想上下传递 10 层或更多层，对吗？(那叫道具钻只是供你参考。)

> 等等，但这应该是关于 MobX 的。你为什么不…？别急，我会到那里的。

让我们调用[上下文](https://reactjs.org/docs/context.html)来获得帮助。

`SettingsProvider`将被放在某个顶层，以覆盖所有可能需要它的组件。一切看起来都很棒，当我们更新设置时，我们的`RandomGiphy`组件将被重新渲染。

> 哎哟！你刚刚也意识到了吗？什么都没有？

你知道当我们更新设置时会发生什么吗？**从`SettingsProvider`到**的所有其他组件都将被重新渲染！我给你一点时间考虑一下…

> 当然，在这个愚蠢的例子中，这一点也不重要。但是在实际的应用程序中，肯定会有一些性能影响。

最后，是时候使用我心爱的 MobX 来拯救世界了！

对于`SettingsProvider`来说，拥有一些持久性是有意义的，但是为了简洁起见，我没有提到它。即使它在这里，也不会很快被重新渲染，当然，除非在树的更高处有其他的罪魁祸首。

另外，请注意，我没有将数据和状态更新程序分开。可观测状态可以直接变异。当然，这并不总是最聪明的想法，如果你愿意，我鼓励你为某种程度的抽象创造行动。或者只看一下精彩的 [mobx-state-tree](https://github.com/mobxjs/mobx-state-tree) 。

除了被 MobX 家族的`observer` HOC 所包裹之外，`RandomGiphy`几乎是一样的。当`tag`更新时，它将负责重新渲染组件(而不是其他)😍

好了，我们到此为止。祝你有愉快的一天，并很快再次见到你…

不，等等！我们可以做得更好。我们有能力定制挂钩。为什么不把查询逻辑分离成一个呢？

同样，这是一个相当愚蠢的例子，但是这种关注点的分离肯定是有用的。我们仍然需要组件的`observer`,因此对`tag`的任何更改都将重新呈现组件，从而用更新后的值重新提取查询。此外，这种分离对于集成测试也是有用的🤓。

挂钩本身可以是可观察的，并且将该部分留在组件之外。但是，这需要您手动构建一个 MobX 反应，并根据更改重新提取查询。最终，我认为这不值得争论。

> 加载和错误状态呢？

这些错误有些简单。你可以让它在树上冒泡，把它扔出去，用[错误边界](https://reactjs.org/docs/error-boundaries.html)接住。您可能想将它发送给某个报告服务，或者显示一个错误提示？或者，如果这不符合您的需要，只需从钩子返回一个错误，并在带有一些 UI 的组件中处理它。

目前加载大多只是暂时的。可以相当有把握地假设，如果从钩子返回了`null`，这意味着数据仍在加载，UI 应该显示一个微调器。后来，当[为数据获取而产生悬念](https://reactjs.org/blog/2018/11/27/react-16-roadmap.html#react-16x-mid-2019-the-one-with-suspense-for-data-fetching)得到一些爱时，我相信会有一些其他的方式，我们只能梦想❤️.

## 决赛成绩

我为你准备了一个完整的工作演示，让你看看所有的操作。这不是一些花哨的应用程序(我不是设计师)，但对于一个展示，它会做的。还有一些小的补充，这里没有提到。请随意尝试。

 [## 带 React 钩子的 graph QL & MobX-code sandbox

### 不要期待什么花哨的 app，只是展示一些基础的。

codesandbox.io](https://codesandbox.io/embed/0m4w49pqwl?expanddevtools=1) 

我希望你开始明白 MobX 是一个真正强大的优化你的应用渲染的工具。而且它非常简单，不需要用 Redux 和类似的工具来做基本的事情。如果你还在犹豫，看看我最近写的另一篇类似主题的文章。

[](/formik-with-react-hooks-and-mobx-1493b5fd607e) [## 用 React Hooks 和 MobX 创建自己的 Formik

### 贾里德·帕尔默最近展示了如何用 React 钩子实现 Formik。我决定用 MobX 代替 useReducer

levelup.gitconnected.com](/formik-with-react-hooks-and-mobx-1493b5fd607e) 

# 有问题吗？

如果你想表达你的观点或者只是问一些问题，并且你对 Medium 的方式有点恼火，让我们在 Twitter 上做[吧。丹·阿布拉莫夫在他的](http://mobile.twitter.com/search?q=https://levelup.gitconnected.com/graceful-graphql-with-react-hooks-and-mobx-38b66e8d0ee7)[overreased . io 博客](https://overreacted.io/)上使用了这个想法，这值得称赞。或者如果你更喜欢的话，继续看[光谱](https://spectrum.chat/react/general/my-take-on-graphql-with-react-hooks-and-mobx~da271d96-2d7e-425b-aae5-5039afe13c17)。

[![](img/9914c5dd23ac08b70eea6f4f9ba6fed2.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/graphql) [## 学习 GraphQL -最佳 GraphQL 教程(2019) | gitconnected

### 11 大 GraphQL 教程-免费学习 GraphQL。课程由开发人员提交和投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/graphql)