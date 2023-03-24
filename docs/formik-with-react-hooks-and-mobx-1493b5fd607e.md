# 用 React Hooks 和 MobX 创建自己的 Formik

> 原文：<https://levelup.gitconnected.com/formik-with-react-hooks-and-mobx-1493b5fd607e>

[useReducer](https://medium.com/u/fb7a3c353cc1#usereducer) 钩子来实现。

> 你可以观看[完整的网络研讨会](https://zoom.us/webinar/register/1415484541886/WN_ij5ODg7YTWyOdxuT2x0sFQ)或者在 [CodeSandbox](https://codesandbox.io/s/pp3k64jj1x) 中查看 Jared 的最终作品。

## MobX 是什么？

> 如果您熟悉 MobX，请随意跳过以下段落。

除非你过去几年一直住在山洞里，否则我肯定你听说过关于 MobX 的一些事情。我不会说它比 Redux 或任何其他库更好。这只是对状态管理的不同看法，起初可能会觉得有点神奇，但在交流中就不那么啰嗦了。

当使用 Redux(和类似的)时，你必须处理动作和 reducers，以基于分派的动作和先前的状态产生新的状态。您通常听说状态应该是不可变的(主要是出于优化的原因)。

另一方面，MobX 基于完全可变的状态。每一个突变都在内部被跟踪，并可以向任何可能感兴趣的人报告变化。MobX 也可以有动作，但是它们仅仅是包装了直接状态突变的功能。

> 在我看来，MobX 没有得到足够的重视，因为它实际上对于新手来说更容易学习(我只是想设置这个属性！).希望这篇文章能有助于传播更多的信息。

注意，目前(2019 年 2 月)要使用带有 React 钩子的 MobX，你必须使用一个包 [mobx-react-lite](https://github.com/mobxjs/mobx-react-lite) ( *我正在维护*)而不是普通的 mobx-react。一旦 Hooks 尘埃落定，这很可能会随着时间的推移而改变/发展。

# 发布 3..2..一

> 如果可以的话，不要试图在 LOC(代码行)问题上比较这里显示的解决方案。虽然 MobX 看起来更短更简洁，但它的黑匣子感觉明显不好，翻越那个驼峰可能会很可怕。但是请一定要试一试，它不是那么大一堆🗻。

![](img/1b43d671e9066cee6a92d11eea1f0d26.png)

由 [SpaceX](https://unsplash.com/@spacex?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 跟踪表单值

如果你看了[网上研讨会](https://zoom.us/webinar/register/1415484541886/WN_ij5ODg7YTWyOdxuT2x0sFQ)，Jared 首先展示了一个带有`name`和`email`字段的简单表单，以及如何在新 React Hooks 工具包 [useReducer](https://reactjs.org/docs/hooks-reference.html#usereducer) 的帮助下轻松跟踪这些字段的值和触摸状态。让我们用 MobX 来代替。

[](https://codesandbox.io/embed/988wjz3pxy) [## MiniFormik v2(带有 React 挂钩和 MobX) — CodeSandbox

### CodeSandbox 是一个为 web 应用程序量身定制的在线编辑器。

codesandbox.io](https://codesandbox.io/embed/988wjz3pxy) 

仔细看看代码，它只是一个带有两个挂钩的常规功能组件。定制的可重用钩子`useFormik`负责跟踪表单输入值，并将该状态保存在 [MobX 可观察对象](https://mobx.js.org/refguide/object.html)中。不需要任何讨厌的 switch 语句、字符串操作或者将一个值合并到以前的状态😵。

看起来有点天真的使用观察者 T21 钩子使得 MobX 在 React 世界中运行，当任何被跟踪的变量改变时，它会重新渲染。

你可能听说过`useCallback`和`useMemo`优化工具(贾里德在最后提到了它)。通常，你会把这些作为优化的最后一步。或者用 MobX 有一个简单的方法。代替`useObserver`，你可以使用`Observer`组件来确保只有组件 UI 会被重新渲染。不会重新执行`useFormik`钩子，也不会重新创建任何东西🙏。

## 验证并提交

让我们快进到网上研讨会，Jared 实现了处理表单提交和基本验证的逻辑。再说一次，MobX 世界中的主要区别是，您不用对笨拙命名的动作使用`dispatch`,而是简单地改变状态，让 MobX 负责 rest。

看看一个工作示例，让我们把重点放在一些不同的和棘手的部分。

[](https://codesandbox.io/embed/5y2pprm17p) [## MiniFormik v2(带 React 挂钩和 MobX)最终版— CodeSandbox

### CodeSandbox 是一个为 web 应用程序量身定制的在线编辑器。

codesandbox.io](https://codesandbox.io/embed/5y2pprm17p) 

> 注意:在名称字段中输入值“admin”以通过表单验证，在电子邮件字段中输入任何值以通过提交验证。

**副作用**

对表单值的更改执行验证听起来是个好主意。有了 MobX，你不需要明确指定你所依赖的变量；它会通过使用可观察的变量来解决这个问题。注意，如果`props.validate`被更新，那就是另一回事了，而且`useDisposable`允许你以和`useEffect.`相同的方式指定输入

**标量状态值**

还有一个即使是经验丰富的 MobX 用户也可能会不时碰到的警告/扫兴/扫兴效果。

`isSubmitting`和`submitError`必须被包裹在另一个`state`对象中。问题是 MobX 无法跟踪标量值的变化(只能跟踪[装箱值](https://mobx.js.org/refguide/boxed.html))。这是一个简单的事实和权衡。因为我们将`formik`作为一个对象传播，所以那里只有当前值，并且因为钩子函数不是在每次改变时都执行(*这是一件好事*)，所以它自己不会看到更新的值。

## 反应上下文

Jared 还展示了如何使用 React 上下文在树的更深处传递`formik`对象。我决定不在我的例子中解决这个问题，因为它与 MobX 无关。然而，我要提到一个重要的事实。

当你有不可变的状态，你会把它放在`Provider`里面。每当产生一个新状态时，`Provider`必须重新呈现整个包含的树，这在某些情况下可能相当昂贵，例如，在每次击键时呈现整个表单😮。

在具有可变状态的 MobX 世界中，这是不需要的。提供者将总是保持对同一状态对象的引用，因此它不需要运行任何呈现逻辑。所有更新将只发生在用观察者跟踪变化的组件内部。这不是很棒吗？😈

更新:在我的另一篇文章中，我简单地提到了这个主题。

[](/graceful-graphql-with-react-hooks-and-mobx-38b66e8d0ee7) [## 优雅的 GraphQL，带有 React 钩子和 MobX

### GraphQL 遇到 React Hooks，结成强大的友谊。他们也应该连接 MobX！

levelup.gitconnected.com](/graceful-graphql-with-react-hooks-and-mobx-38b66e8d0ee7) 

# 结论

您可能会想，所有这些 MobX 的魔力都必须有一个高性能的价格标签。嗯，肯定比纯功能减压器模式高。然而，考虑到它不需要在每次改变时重新渲染大的组件树，你会突然获得大量的性能！在更复杂的场景中，它可能会更快，但我没有任何基准来证明这一点。

同样值得一提的是，不可变状态的目的是能够识别出*中的某个东西*已经大量改变，这样您就可以提供公平的优化。有了 MobX，你真的不需要担心它，因为它会让你知道哪些部分发生了变化，哪些需要刷新。这又减少了一个巨大的中间步骤，让你可以专注于你需要做的事情。

仅此而已。据我所知没什么特别的。我真的无法击败贾里德和他的可怕的演示👍。至少我希望你认真考虑给 MobX 一些尝试。完全值得！

# 有问题吗？

如果你想表达你的观点或者只是问一些问题，并且你对媒体的方式有点恼火，让我们在推特上[这样做吧。丹·阿布拉莫夫在他的](https://mobile.twitter.com/search?q=https://levelup.gitconnected.com/formik-with-react-hooks-and-mobx-1493b5fd607e)[博客](https://overreacted.io/)上使用的这个想法值得称赞。或者直接用[谱](https://spectrum.chat/react/general/check-out-comparison-of-usereducer-and-mobx~ceb3f3ef-530c-474d-9cfa-a01ac4fff629)。