# 一个开发者的誓言:遵守我的承诺…用 React

> 原文：<https://levelup.gitconnected.com/a-developers-oath-keep-my-promises-with-react-6926b60435c>

## React 开发人员的承诺处理建议

**注意:**这个提议可以被移植到其他工具上，不久前我已经为 lit-html 移植过了。

![](img/5598d1514b54020c8602f7c9fc87ac51.png)

# 1/3 引言

有些承诺你信守，有些则不然；挺好的。只要这两种情况都处理了😌。剧透提示:结尾有一个代码沙箱。

![](img/f13bd4a8666d8800e641b659823e8bf6.png)

那天我希望我的应用程序可以是一个誓言…一个牢不可破的誓言🧙‍♀️

在 React 项目(和其他项目)中，我有这样一种感觉，我们通过将结果保存在一个状态中来保持尽可能快地去结构化承诺——通常在组件安装时。像这样:

现在在想:
1。为什么总是重写这些`useEffect`或`componentDidMount`(尽管有些部分可以分解)？
2。解开承诺价值的冲动是什么？
如果我们直接使用模板中的承诺会怎么样🤔？(这本身就是一个因式分解🤫。并且实际上会更加函数式编程友好，尽管这是有争议的。)

# 2/3 承诺折叠和映射

![](img/84e71269a5b500990981480dc1bf04f2.png)

疯狂的折叠技巧😎

有**两个用例来*创建*承诺** :
**1。**当异步动作被触发时*立即*，如数据获取。
**2。**按需触发*异步动作时，如表单提交。*

有**两个用例来*使用*一个承诺值** :
**1。详尽地**:在一个地方处理所有状态(未询问、未决、失败&成功)；特别是确保错误得到处理😒。
2**。不完全**:例如，当你需要一个按钮只有在提交被挂起时才被禁用。为了符合人体工程学，我们的组件 API 应该处理那些 2 × 2 用例。

> 旁注:我得出一个结论，特朗普和墨西哥人之间的边界(以及其他边界)是错误处理🤐

![](img/31a12e8f4d48c90f533d87f2c5410619.png)

一个初级开发人员在路上的罕见镜头

## 1.异步动作立即触发——数据提取

出于测试目的，我们可以通过注入来测试所有的承诺状态:
-未决:`getUser: (id) => **new** Promise()`
-从不解析承诺:`getUser: (id) => Promise.resolve(…)`
-失败:`getUser: (id) => Promise.reject(**new** Error('Oops'))`

## 2.按需触发的异步动作*—表单提交*

*好的，那很好。但是如果我有更复杂的需求呢，比如组合承诺？*

## *承诺作文*

*幸运的是，`Promise`是一个很棒的数据类型，它允许您转换成功数据**和错误数据**。这意味着我们可以自由地无限期地从其他承诺中创造承诺**，而不会触发更多的请求**。为了说明这一点:*

```
***const** user = fetch(…)
**const** userName = user.then((user) => user.name)
**const** userNameWithBetterError = userName.catch((error) => {
  **if** (error.message.length > 10) throw error
  **throw new** Error('some clearer error')
})*
```

*然后(🤭)在某种程度上，我们可以准确地分解它们的值**，并且只在我们需要的地方进行分解**。*

*这里，如果`user`承诺改变，`posts`承诺立即变为*待定*。*

*在性能方面，可能会有惊人的影响:
*在*之前:整个组件每承诺重渲染 2+次。
*之后:组件为每个承诺重新呈现一次，当承诺解决或拒绝时，只有模板的子部分被重新呈现。
多牛逼啊？**

**![](img/6390bb4a00349aa28277b0cf0990fcd7.png)**

**我，唱着我的小夜曲**

**现在可能有一些我目前不知道的警告。想一想，我想知道这种方法是否有一个共同的警告:道具训练。
如果你在组件树深处传递承诺，并在向下的过程中从 props 中创建新的承诺，我不知道错误有多明显，找到最初的错误承诺有多容易。这个*可能*不能帮助跟踪 bug 和追踪问题。
另一方面，承诺堆叠痕迹挺好的，不把承诺当道具传就没事，所以……我不知道。也许一个不允许通过承诺作为道具的 ESLint 规则可以达到这个目的？
思想？**

# **3/3 反馈时间！**

**![](img/d9121493ac82c638c2652f94850cbca3.png)**

**拜托了。**

*   **你觉得这个图案怎么样？**
*   **这是一个**真**好主意还是一个**假**好主意？**
*   **你会在你的公司或项目中使用这种模式吗？**
*   **我是不是错过了一些应该阻止这个想法的东西？**
*   **最后但同样重要的是:我应该对此做一个小小的改动吗？(如果是这样，我也会添加一个独立的挂钩😏)**

> **如果您对承诺是如何在幕后表现的感兴趣，我使用了一个我在 Elm 包 [remotedata-http，](https://package.elm-lang.org/packages/ohanhi/remotedata-http/latest/)中遇到的通用数据类型`*RemoteData*`，并将其转换为 JavaScript/TypeScript:**

```
****type** RemoteData<Error, Value> =
  | { kind: 'NotAsked' }
  | { kind: 'Pending', stale: Value | undefined }
  | { kind: 'Failure', error: Error }
  | { kind: 'Success', value: Value }**
```

> **我最近还在 [fp-ts](https://gcanti.github.io/fp-ts/modules/TaskEither.ts.html) 库中发现了一个等价类型:`*TaskEither*`。**

## **结论**

**![](img/66cea42e63aa66bd31460fc35b35e747.png)**

**唷，那是一个很长的帖子，我想我现在需要打个盹**

**无论如何，如果你已经走了这么远，非常感谢*的阅读，我希望它至少能引出一些问题。我在最后添加了一些关于现有技术的注释。***

***保持安全😷，希望疫情将很快成为一个(非常)糟糕的记忆。
干杯🤗，又这么久👋！***

***顺便说一句，我从来没有提到过:
我目前住在巴黎东部(作为自由职业者)，如果你想边喝边讨论东西或项目，你可以给我留言😊。***

***因为我信守承诺🙃，承诺的沙盒:***

***沙盒***

# ***先前技术***

***我在 NPM 上发现的最接近的 react 现有技术是 [use-remote-data](https://oyvindberg.github.io/use-remote-data/intro) (它做了太多的事情)和[react-use-promise-matcher](https://www.npmjs.com/package/react-use-promise-matcher)，在这两种情况下，我都不喜欢需要一个钩子**和一个组件**的想法。IMHO 它应该是一个钩子**或**一个组件。库应该尽可能保持简单，以便有效地集成在一起。
例如，这里我只处理简单的承诺，你可以使用其他工具来处理承诺，比如 [ts-retry](https://www.npmjs.com/package/ts-retry) 来处理重试策略。我不知道你如何履行你的诺言！***

***其他现有技术——它们是很好的库，但不适合我现在使用库的方式:***

*   ***[react-async-hook](https://www.npmjs.com/package/react-async-hook) :错误的数据结构允许不正确的状态。例如，`{ loading: true, error: new Error(…), result: '…' }`是一种可能的状态，它在现实世界中没有位置🤷。***
*   ***[react-query](https://react-query.tanstack.com/overview) :同样的问题，而且它做了太多的事情，同时紧密地将它们绑定到 react。在我看来，像缓存**这样的特性实现不需要 React** ，一般来说，这些特性应该与其他工具/库分离。***
*   ***[use-remote-data](https://oyvindberg.github.io/use-remote-data/intro/) :状态映射没有穷尽性(没有错误处理)，需要处理的东西太多，反应也紧密耦合。***
*   ***[swr](https://github.com/vercel/swr) :相同。但是不一样。但还是一样。🕵️‍***