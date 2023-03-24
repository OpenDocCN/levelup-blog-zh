# 我在使用 Jest 和 React 测试库对 REST APIs 进行单元测试时遇到的问题。

> 原文：<https://levelup.gitconnected.com/things-i-got-stuck-with-when-unit-testing-rest-apis-using-jest-and-react-testing-library-6dbfdbe70cf9>

![](img/a764f9ccb1b220b247be126738b57e47.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 我开始写测试代码的原因

我是一个 React 爱好者，一直在使用 React 创建许多 web 应用程序。然而，我从来没有为他们写过测试用例。通常，当我学习新的东西时，我会从教程开始。然后，我根据从教程中获得的知识创建了我的应用程序。不管怎样，我都不需要写测试。当涉及到创建一些带有教程的应用程序时，测试在大多数时候超出了他们的范围。我自己创建应用程序的时间呢？
老实说，我认为只要应用程序正常工作就没问题。是的…那可能没问题，但是我可以做得更好！

尤其是生产级应用，必须安全工作。如果我在生产中造成系统故障，影响将是巨大的。开始学考就够理由了吧？这就是我开始编写测试的原因。

# 我为 like 写测试用例的项目怎么样？

我自己创建的最新项目是一个 [YouTube 克隆应用](https://react-youtube-clone-app.netlify.app/)。这是一个简单的 React 应用程序，几乎与 YouTube 一样工作。你可以通过关键词搜索你想看的视频，并在上面播放。虽然我是按照教程创建的，但是没有关于测试的说明。所以，我决定为这个应用编写测试。

这次我使用 Jest 和 React 测试库来编写单元测试。请注意，这次我将跳过对它们的详细解释。如果你想更详细地了解它们，我推荐你阅读这篇文章。

[](https://dev.to/ibrahima92/8-simple-steps-to-start-testing-react-apps-using-react-testing-library-and-jest-3922) [## 使用 React 测试库和 Jest 开始测试 React 应用的 8 个简单步骤

### 测试经常被视为单调乏味的事情。这是额外的代码，在某些情况下，老实说，这是不需要的…

开发到](https://dev.to/ibrahima92/8-simple-steps-to-start-testing-react-apps-using-react-testing-library-and-jest-3922) 

顺便说一下，你可以在这里玩这个应用程序。😀

 [## React 应用

### 使用 create-react-app 创建的网站

react-YouTube-clone-app . net lify . app](https://react-youtube-clone-app.netlify.app/) 

# 我写什么样的测试？

由于 YouTube clone 应用程序从 YouTube API 获取数据并将它们传递给每个 React 组件，所以我决定检查它是否按预期执行。

这是我的 GitHub 回购。如果你觉得我的解释遗漏了什么，可能会有帮助。

[](https://github.com/marieooq/react-youtube-clone-public) [## marieooq/react-YouTube-克隆-公共

### 这是一个 YouTube 克隆应用程序。你可以通过关键词搜索视频。当你搜索时，与关键词相关的视频…

github.com](https://github.com/marieooq/react-youtube-clone-public) 

我已经去掉了从 API 获取数据的那部分代码。当通过 GET 方法到达每个端点时，YouTube API 会根据请求返回一个响应。我将检查是否从 API(模拟 API)获取数据并在 React DOM 中正确显示。

# 原料药测试的准备工作

在开始测试之前，您必须创建一个像真正的 API 一样工作的服务器。这意味着你必须让 API 在它的端点被点击时返回数据，就像 YouTube API 那样。你会怎么做？让我们来看看这个例子。

为了创建一个服务器，我使用[模拟服务工作者。](https://mswjs.io/docs/)他们的文档组织有序，非常容易理解。我建议看一下。我就在你这次已经知道 MSW 的前提下往前走了。https://mswjs.io/docs/getting-started
T3

最核心的部分是下面的代码。当您点击端点(([https://www.googleapis.com/youtube/v3/videos'](https://www.googleapis.com/youtube/v3/videos'))时，该服务器返回 200 状态(成功状态，表示请求已经成功)和 JSON 数据，其中包含名为 **popularItems** 的 items 属性和值。

我简单解释一下其他代码。
在开始测试之前，要用 **beforeAll()** 监听服务器。

您可以通过使用 **afterEach()** 来重置您可能在测试期间添加的任何请求处理程序，这样它们就不会影响其他测试。

您可以在测试完成后使用 **afterAll()** 进行清理。

# 让我们写测试用例吧！

下面是测试用例的代码。让我们仔细看看代码。

我将解释一下这段代码中使用的关键字。

*   **描述:**说明这是一种什么样的测试。您可以在作为第二个参数传递的函数中编写测试用例。
*   **it** :描述测试本身。它将测试名和保存测试的函数作为参数。
*   **渲染**:用于渲染给定组件的方法(在本例中，< Top / >是我想要测试的目标)
*   **期望**:测试需要通过的条件。

例如，下面的代码意思是这样的…

1.  我希望文档中存在**【标题 1】**
2.  **我希望 **'title1'** 作为 **alt** 属性存在(我想检查 **img** 标签，其中 **alt ='title1'** 存在 **)****
3.  **我期望 **'title1'** 作为 **alt** 属性存在(我想检查一下 **img** 标签，其中 **alt ='title1'** 存在**)具有 src 属性'**[https://dummy image1/default . jpg](https://dummyimage1/default.jpg)**'****

# **我遇到的问题以及如何解决它们？**

## **问题 1:如何访问全局状态？**

**现在我先介绍了我的最终代码，你可能想象不到在完成这个项目之前我有多努力。但是，我在编码时遇到了几个问题，我来介绍一下。**

**我遇到的第一个问题是访问全局状态。在渲染要测试的组件时，通常你会这样写代码。**

**一开始我也是这么走的。然而，当我运行测试时，我遇到了错误。**

```
Error: Uncaught [Error: Invariant failed:
You should not use <Link> outside a <Router>
```

**好吧，那是因为我在顶部组件中使用了<link>，但是我没有用<router>包装它们。然后，我修改成这样。</router>**

**这一次，它似乎修复了错误，但它仍然没有通过测试。**

```
Unable to find an element with the text: title1\. This could be because the text is broken up by multiple elements. In this case, you can provide a function for your text matcher to make your matcher more flexible.
```

**为什么会发生这样的事情？因为 YouTube 克隆 app 使用的是 React context API 和 globalState 管理的状态。让我们看一下 App.js 和 index.js，它们是它的上层。**

**在 App.js 中，每个组件都用<router>包装，而在 index.js 中，App 组件用管理全局状态的<storeprovider>包装。它没有通过测试，因为我没有用<router>和<storeprovider>包裹顶部组件。最终，正确的代码是这样的。</storeprovider></router></storeprovider></router>**

**现在，您应该正确运行测试了！👏**

## **问题 2:如果端点需要某个查询怎么办？**

**让我们看看另一个要测试的组件。**

**它的结构与我前面提到的组件几乎相同，但是在这种情况下，您需要一个查询来从 API 获取数据。那么，你如何在测试中做同样的事情呢？**

**如果你用的是 React 路由器(我猜大部分 React 项目都在用。)，可以使用 **createMemoryHistory。****

**[https://know body . github . io/react-router-docs/API/histories . html](https://knowbody.github.io/react-router-docs/api/Histories.html)**

> **`**createMemoryHistory(options)** createMemoryHistory`创建一个不与浏览器 URL 交互的内存中的`history`对象。当您需要定制用于服务器端渲染和自动化测试的`history`时，这很有用。**

**正如这个描述，它最适合自动化测试！所以，是时候写测试了！**

**在这种情况下，它的作用就像您在带有查询“dummy”的路径“/search”中一样。**

**这就是在搜索组件中获取查询的方式。**

**以下是使用 createMemoryHistory()更多示例。
[https://testing-library.com/docs/example-react-router/](https://testing-library.com/docs/example-react-router/)**

**要多学一点历史，这篇文章可能会有帮助。
[https://medium . com/@ pshrmn/a-little-bit-of-history-f 245306 f48dd](https://medium.com/@pshrmn/a-little-bit-of-history-f245306f48dd)**

## **问题 3:由于虚拟数据的结构，没有通过测试。**

**由于虚拟数据的结构，我多次测试失败，所以请确保数据结构与真实数据相同！**

## **问题 4:没有通过测试，因为我没有用异步封装测试。**

**当您为 API 编写测试用例时，您必须使用 async，因为从它那里获取数据需要一段时间。不要忘记在你的测试用例中使用它。**

**当你第一次编写测试用例时，你可能会像我一样面临很多错误。希望这篇文章有帮助！如果您有任何问题或建议，请告诉我！非常感谢您的阅读！😀**

**我愿意讨论 web 开发中的新机会！🔥另外，我现在正在推特上写 [#100DaysOfCode](https://twitter.com/search?q=%23100DaysOfCode&src=hashtag_click) 。喜欢就去看看！推特:@marie_otaki**

**[https://twitter.com/marie_otaki](https://twitter.com/marie_otaki)**