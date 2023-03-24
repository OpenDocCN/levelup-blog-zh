# 用 React 构建象棋战术应用程序(第 2 部分):使用象棋战术 API

> 原文：<https://levelup.gitconnected.com/build-a-chess-tactics-app-with-react-part-2-35621640afd1>

## 从头做起

![](img/089588ba4bb2e77336d1cec98bfaed37.png)

[Elia Pellegrini](https://unsplash.com/@eliapelle?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/chess?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在[上一篇文章](/build-a-chess-tactics-app-with-react-part-1-49be01fc63b5)中，我们为我们的战术应用程序创建了核心组件。在这种情况下，我们将通过将应用程序插入 API 服务并显示移动反馈来完成应用程序。

在我们开始之前，让我们再看一看在帖子的结尾我们会有什么。

![](img/d72ebb26d99c5feeb48b57173c76de52.png)

还有这里是 [app 链接](https://varunpvp.github.io/tactics-trainer/)，如果你想试试的话。

## API 服务

我们将使用[chesblunders](https://chessblunders.org/api)的`/api/blunder/get` api 端点从 api 获取随机策略。要进行 api 调用，我们需要 axios。让我们安装 axios。

```
yarn add axios
```

现在，我们已经安装了 axios。让我们写一个函数来获取策略。

我通过 [cors-anywhere](https://github.com/Rob--W/cors-anywhere) 代理 chessbulunder api 调用，以避免任何 cors 问题。并从 env 中选择 url。

加载响应后。我们将响应数据转换为`Tactic`类型。

现在，让我们换成`App.tsx`，让它与我们的 api 服务一起工作。

首先，删除我们为测试应用程序而添加的硬编码策略。

接下来，我们需要做三件事。

*   创建一个函数调用`loadTactic`，它将获取策略并将其添加到我们的`tactics`数组中。
*   A `useEffect`调用`loadTactic`从 api 加载战术。
*   在`TacticBoard`的`onSolve`回调中，将从`tactics`数组中删除第一个战术。并调用`loadTactic`加载下一个战术。

下面是修改后的代码。

如果你看到，我在`useEffect`中调用了`loadTactic`两次。这是为了确保我们已经准备好下一个策略，而用户正在解决当前的一个。

这样，我们应该通过从 api 服务获取策略来让我们的应用程序工作。

## 移动反馈

我们的应用程序正在运行。但是它没有向用户显示任何反馈。比如哪一方该上场，战术解决，正确或不正确的移动。

我们来补充一下。你看，我们的反馈用户界面有 4 种状态

*   一边玩
*   策略解决
*   错误的移动
*   正确的移动

我们需要创建一个状态变量来保存反馈 UI 的状态。

并在渲染`TacticBoard`组件之前将下面的代码添加到`App.tsx`中。

为了简单起见，我在`App.css`中添加了 css 样式。可以在 [Github repo](https://github.com/varunpvp/tactics-trainer) 中找到。

我们差不多完成了。需要做 4 件小事。

1.  在`onCorrect`回调。我们需要将提示设置为`correct`和`setTimeout`1 秒，以将其设置回`sideToPlay`。
2.  在`onIncorrect`回调。我们确实喜欢`onCorrect`，但是将提示设置为`incorrect`而不是`correct`。
3.  在`onSolve`回调中。我们将把提示设置为`solved`。
4.  在`loadTactic`中，何时完成战术的加载。我们将把提示设置为`sideToPlay`。

现在，一切就绪。这就是我们最终代码的样子。

我们已经运行了应用程序的最终版本。

我们可以添加许多其他很酷的东西。像正确或不正确的移动声音，战术计时器，跳过当前战术和改善用户界面等。

但是我就说到这里。如果你有任何想法或改进。欢迎你做出改变。这里是 [Github 回购](https://github.com/varunpvp/tactics-trainer)

[](https://github.com/varunpvp/tactics-trainer) [## varunvpp/战术训练员

### 一个应用程序来训练你的国际象棋战术技能。通过创建以下帐户，为 varunvpp/战术培训师的发展做出贡献…

github.com](https://github.com/varunpvp/tactics-trainer) 

这是[应用](https://varunpvp.github.io/tactics-trainer/)最终版本的链接。

 [## 象棋战术训练器

### 一个训练你国际象棋战术技能的应用程序

varunvpp . github . io](https://varunpvp.github.io/tactics-trainer/) 

我希望你们喜欢它，并发现它很有用。如果是的话，给它一个掌声。

感谢阅读。