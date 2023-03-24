# 在 React 中实现通用导航策略

> 原文：<https://levelup.gitconnected.com/driving-towards-a-universal-navigation-strategy-in-react-a01152e6617c>

![](img/3ade3cffb42aef47acbe246fc05404ce.png)

## 当我加入 STRV 时，他们为我准备了一个具体的要求:为 iOS、Android 和 Web 开发一个前端应用程序，在所有平台之间共享组件和业务逻辑。

因为我是一个喜欢探索新领域的前端开发人员，所以我抓住了这个机会。

我最终面临着各种各样的挑战——比如缺乏与 React Native Web 相关的真实场景内容，意外缺乏流行项目的文档，以及努力构建一些特定于平台的模块。

本文重点关注这个旅程中的一个关键部分:构建导航解决方案。

但是首先…

# 一点背景知识

我之前只做过一个 React 原生应用的例子(未编译和未发布)。在这个项目的时候，老实说，我对 React Native 了解不多。

我第一次听说 [Expo](https://expo.io/) 和它的实验性网络支持，但我决定不去尝试，主要是因为我喜欢控制项目堆栈并了解正在发生的事情；我希望能够自定义安装，安装模块的自定义版本，并对项目的依赖性有更多的控制。

然后我听说了 GitHub 上的另外两个项目: [React Native for Web](https://github.com/necolas/react-native-web) 和 [ReactXP](https://github.com/microsoft/reactxp) 。两者目标相似，但方法不同。正如 ReactXP 的官方文件所述:

> ReactXP 是一个位于 React Native 和 React 之上的层，而 React Native for Web 是 React Native 的并行实现——它是 React Native for iOS 和 Android 的兄弟。

这篇文章不会着重讨论这两者之间的区别，但是，在浏览了一些技术博客文章和演讲之后，我们最终选择了 ReactNative for Web。

在深入研究了一些文章并尝试在各自的领域中实现每个环境后，我发现对我来说，最好的起点是一个伟大的模板，名为[react-native-we b-monorepo](https://github.com/brunolemos/react-native-web-monorepo)，它使用 [Yarn Workspaces](https://yarnpkg.com/lang/en/docs/workspaces/) 的一点帮助来支持通用应用程序。

但是，在开始将这种方法实现到您的项目中之前，我建议您审查您的需求，并检查这些工具是否能解决您的所有需求。

# 我们在外面有什么

React.js 生态系统上一些流行的路由解决方案并不支持 DOM 和本地环境；`<div>` s 不同于`<View>`，`<ul>` s 不同于`<FlatList>` s，并且大多数网络原语不同于移动原语——这使得很难提出一个通用的解决方案。 [@reach/router](https://reach.tech/router) 是选择不面对支持两种环境的挑战的 web 解决方案的一个例子。

不过，截至目前(2020 年 1 月)，我们已经有了一些现成的通用 web/native 公式。但是它们都没有完全满足我们的需求:

*   react-router 对于网络来说是一个很好的选择，但是在 [mobile](https://reacttraining.com/react-router/native) 上，它缺少屏幕转换、模态、导航栏、后退按钮支持和其他基本的导航原语。
*   [react-navigation](https://reactnavigation.org/) 非常适合移动设备，但是鉴于其 [web](https://reactnavigation.org/docs/en/web-support.html) 支持仍被认为是试验性的——尚未在生产中广泛使用——很可能您将面临一些与历史和查询参数相关的问题。此外，它缺少 TypeScript 类型——这让我自己编写部分定义，因为 TypeScript 是该项目的必备工具。

这就把我们带到了下一部分！

# 思考解决方案

> *这篇文章的代码可以在 GitHub 上找到:*[*ythe combinator/react-native-we B- monorepo-navigation*](https://github.com/ythecombinator/react-native-web-monorepo-navigation)

我承认，当我们开始这段旅程时，最令人困惑的事情之一是无法发现使用 React Native for Web 的流行应用程序(例如 Twitter、Uber Eats 和这里提到的所有其他)是如何进行导航的——以及它们如何面临像我之前提到的挑战。

所以，我们必须自己努力！

我们的新解决方案基于对 react-router-dom⁴和 react-navigation⁵.最新版本的抽象两者都发展了很多，现在他们两个似乎有一些共同的目标，我认为这些目标是在 React 中正确进行导航/路由的关键决策:

*   钩子-第一 API
*   实现导航的声明式方法
*   带有 TypeScript 的一级类型

鉴于此，我们提出了几个实用程序和组件，旨在实现通用导航策略:

## `[utils/navigation](https://github.com/ythecombinator/react-native-web-monorepo-navigation/blob/master/packages/components/src/utils/navigation)`

暴露两个挂钩:

*   `useNavigation`:返回一个`navigate`函数，该函数获取一条路线作为第一个参数，其他参数作为其他参数。

它可以这样使用:

```
import { useNavigation } from "../utils/navigation";// Our routes mapping – we'll be discussing this one in a minuteimport { routes } from "../utils/router";const { navigate } = useNavigation();// Using the `navigate` method from useNavigation to go to a certain routenavigate(routes.features.codeSharing.path);
```

它还为您提供了一些其他已知的路由实用程序，如`goBack`和`replace`。

*   `useRoute`:返回当前路线的一些数据(例如传递给该路线的`path`和`params`)。

这就是如何使用它来获得电流`path`:

```
import { useRoute } from "../utils/navigation";const { path } = useRoute();console.log(path);// This will log:// '/features/code-sharing' on the web// 'features_code-sharing' on mobile
```

## `[utils/router](https://github.com/ythecombinator/react-native-web-monorepo-navigation/tree/master/packages/components/src/utils/router)`

这基本上包含了一个`routes`对象——它包含了每个平台的不同路径和实现——可用于:

*   使用`useNavigation`导航
*   使用`useRoute`基于当前路径的切换逻辑
*   指定由`Router`组件呈现的每条路线的`path`和一些额外数据

## `[components/Link](https://github.com/ythecombinator/react-native-web-monorepo-navigation/tree/master/packages/components/src/Link)`

它提供了应用程序的声明性导航。它是从网上的`react-router-dom` [和移动](https://github.com/ythecombinator/react-native-web-monorepo-navigation/blob/master/packages/components/src/Link/index.web.tsx)上的`TouchableOpacity` + `useNavigation`吊钩[建立在`Link`之上。](https://github.com/ythecombinator/react-native-web-monorepo-navigation/blob/master/packages/components/src/Link/index.native.tsx)

就像`react-router-dom`里的`Link`，可以这样用:

```
import { Text } from "react-native";import { Link } from "../Link";
import { routes } from "../utils/router";<Link path={routes.features.webSupport.path}>
  <Text>Check "Web support via react-native-web"</Text>
</Link>
```

## `[components/Router](https://github.com/ythecombinator/react-native-web-monorepo-navigation/tree/master/packages/components/src/Router)`

这是路由器本身。在网络上，它基本上是一个`BrowserRouter`，使用`Switch`来选择一条路线。在手机上，它是`Stack`和`BottomTab`导航仪的组合。

综上所述，你得到的是浏览应用程序的每个[屏幕](https://github.com/ythecombinator/react-native-web-monorepo-navigation/tree/master/packages/components/src/screens)，看看`useRoute()`、`useNavigation()`和`<Link />`如何在任何平台上使用。

如果有人问我未来在这方面的工作，我会提到接下来的步骤:

1.  添加更多的实用程序——例如，一个`Redirect`组件，旨在提供更具声明性的导航 approach⁶.
2.  在两个平台上处理边缘案例。
3.  重组导航库中的大部分内容，只留下主要的`Router`组件和`utils/router`组件在应用程序端编写。

# 结论

我的感觉是，web、移动 web 和原生应用程序环境都需要特定的设计和用户 experience⁷——顺便说一下，这符合 React Native 背后提到的*“一次学习，随处编写】* [哲学。](https://reactjs.org/blog/2015/03/26/introducing-react-native.html)

虽然代码共享对于 React 和 React Native 来说是一个很大的优势，但我要说的是，共享的跨平台代码很有可能应该是:

*   业务逻辑
*   配置文件、翻译文件和大多数常量数据——那些不特定于渲染环境的数据
*   API /格式化；例如 API 调用、请求和响应数据的认证和格式化

该应用程序的其他几层，如路由，应该使用最适合该平台的库，例如，`react-router-dom`用于 web，而`react-navigation`或类似的用于 native。

也许在未来，我们可以有一个真正统一的代码库，但现在，感觉技术还没有准备好，这里分享的方法似乎是最合适的。

# 脚注

1)在今年的 Reactive Conf 大会上，Evan Bacon 在 Web 博览会上发表了一篇精彩的[演讲——如果你还没有看过，我强烈建议你去看看。](https://www.youtube.com/watch?v=k1FdrhA2sCY)

2)这是布鲁诺·莱默斯创作并使用的，他是 [DevHub](https://devhubapp.com/) 的作者，这是一个 GitHub 客户端，可以在 Android、iOS、Web 和桌面上运行，95%以上的代码在它们之间共享。如果你对他如何想出这个解决方案感兴趣，请查看[这个](https://dev.to/brunolemos/tutorial-100-code-sharing-between-ios-android--web-using-react-native-web-andmonorepo-4pej)。

3)这些问题包括:

功能范围:

*   来自 URL 的查询参数未传递(此处为)
*   推回不起作用(此处[此处](https://github.com/react-navigation/web/issues/22)和[此处](https://github.com/react-navigation/web/issues/41)
*   为了方便起见，将一些参数从一个路由推到另一个路由，并编码到 URL 中

开发者体验:

*   缺少打字稿打字([这里是](https://github.com/react-navigation/web/issues/34))——这让我自己写部分定义

4) [React Router v5](https://reacttraining.com/blog/react-router-v5/) 主要致力于引入结构改进和一些新功能。但是随后 [v5.1](https://reacttraining.com/blog/react-router-v5-1/) 带来了一些有用的钩子，允许我们为 web 实现上面提到的钩子。

5) [React Navigation v5](https://blog.expo.io/announcing-react-navigation-5-0-bd9e5d45569e) 也做了很多努力来带来一个现代的、钩子优先的 API，允许我们为移动设备实现上面提到的 API。

6)这里有一个关于用`<Redirect />` [做声明式可组合导航的极好的帖子](https://tylermcginnis.com/react-router-programmatically-navigate/)。

7)如果你对这个话题感兴趣，在[这个演讲](https://www.ythecombinator.space/talks/code-sharing-at-scale-one-codebase-for-web-mobile-and-desktop)中，我分享了在构建一个以代码共享为主要目标的应用程序时学到的一些经验——从项目设置，通过共享基础设施，一直到共享组件和样式——以及你如何才能实现同样的事情。