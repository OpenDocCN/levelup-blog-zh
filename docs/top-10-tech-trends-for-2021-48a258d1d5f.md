# 2021 年十大技术趋势

> 原文：<https://levelup.gitconnected.com/top-10-tech-trends-for-2021-48a258d1d5f>

今年，我将我的“预测”集中在更广泛的技术趋势上，如果你是前端、后端或全栈的 Javascript 开发人员，我强烈建议你关注这些趋势。所以我们开始吧！

![](img/abaddc8678c67b4d811fedcd0897f596.png)

2021 年横幅图像的顶级技术趋势

# **打字稿**

如果你现在正在编写 Javascript，并且你还没有学习过 Typescript，那么现在正是时候(甚至可能已经过了一会儿。)如果你在等待 Typescript、Flow、JSDoc 之争的赢家，那么 Typescript 获胜的证据就在那里。现在，大型开源项目是用 Typescript 编写的，根据我的经验，大型商业项目已经在使用它或者正在向它转移。Angular 是现成的打字稿。

依我看，进入 Typescript 的最佳方式是使用一个您已经熟悉的现有 Javascript 项目，可能是您正在做的一个小项目，或者是一个工作中的概念验证，然后将其移植到 Typescript。这将使您熟悉语法和工具，而不必同时解决新的代码问题。这是我如何在[上转换成打字稿系列](https://www.youtube.com/playlist?list=PLNqp92_EXZBIKO8lqN3089jgZUi-FUFXX)的。我们采用一些非常简单的 Javascript 代码，并一步一步地将其移植到 Typescript。

我的大多数蓝领程序员视频将在 2021 年被打印出来，我会练习，练习，练习一整年。

说真的，你们所有人，如果你们从阅读这本书中拿走一样东西，那就是这个；学习打字稿。2021 年将打字稿列为“最好拥有”的工作将在 2022 年需要它。

# 视频版本

如果你想看视频，你也可以这样做！

# **微观国家管理者**

甚至在 React hooks 出现之前，Redux 就已经失去了市场份额。太多样板文件。Redux Sagas 最终使代码变得复杂和难以理解。然后在 2018 年，我们得到了 hooks，global state 从敌人变成了失散多年的朋友。如果故事到此结束就好了，但现实是`useState`和`useContext`虽然很棒，但在大型反应树中存在效率问题，因为从`Provider`开始的所有东西都需要根据值的变化进行更新。

这意味着，如果您的状态在 React 树的不同部分共享，那么您将需要一些管理解决方案。那就复仇吧。几乎没有。有很多新的伟大的微状态管理器选项，可以给你全局状态，而没有 Redux 的麻烦。一些例子包括[瓦尔蒂奥](https://github.com/pmndrs/valtio)、[约泰](https://github.com/pmndrs/jotai)、[反冲](https://recoiljs.org/)和[祖斯坦德](https://zustand.surge.sh/)(也有最好的登陆页面 evaaahhh)。

我已经在我的频道上报道了很多。您应该在自己的项目中尝试一下，或者尝试我的一个例子，感受一下这些微观状态管理器有多简单，并为您下次需要全局状态管理时提供一些架构思路。

额外奖励: [XState](https://github.com/davidkpiano/xstate) 是一个非常有趣的基于状态机的可选状态管理系统。如果你的项目有非常具体的 UI 状态，或者服务器状态，这真的很有意思，它在那里也能工作。它还有一个超级甜蜜的状态可视化器，它本身就值门票的价格。

# **图表 QL**

GraphQL 刚满五岁！你能相信吗？现在你可能会说 GraphQL 现在应该已经接管了。但是进入 REST 的“架构风格”已经五年了(它不是一个标准)，我们仍然使用 XML 作为数据交换格式，而不是 JSON。

GraphQL 生态系统在 2020 年取得了辉煌的一年，并有望在 2021 年取得更好的成绩。一些 hilights 包括 [Hasura](https://hasura.io/) 服务器，它可以在任何 RDMS 上放置一个 GraphQL API，只需点击一个按钮。今年推出的 [graphql/nexus](https://nexusjs.org/) 库使得构建代码变得更加容易，并输出 Typescript 的类型绑定(这也是学习 Typescript 的另一个原因)。还有一个 [GraphQL 代码生成器](https://graphql-code-generator.com/)，它可以为你不拥有的服务器创建类型脚本接口。

如果你现在想亲自试用 GraphQL】，你可以[从这个列表](https://github.com/APIs-guru/graphql-apis)中选择一个 API，试一试。这里有一个[有趣的天气 API](https://graphql-weather-api.herokuapp.com/) 你可以免费运行查询，没有密钥，没有代码，只要试一试。

蓝领编码器频道将从明年年初开始推出完整的 GraphQL 首尾相连系列，将带您了解什么是 GraphQL 以及它如何融入一切，通过进行第一次查询，一直到构建自己的服务器和使用订阅等高级功能。

# **实用程序第一个 CSS**

我认为对于每个选择器都有一个类的 CSS 库的方法是否有意义有一个很好的辩论，但是有一点是肯定的，实用工具优先[顺风 CSS 库](https://tailwindcss.com/)非常受欢迎并且会一直存在。

为了开始使用 Tailwind，我推荐一个我刚刚发布的关于使用 Twin 的视频。宏,这是配置 CRA(创建 React 应用程序)以有效使用 Tailwind 的一种非常简单的方法。它将为你继续探索这种迷人的风格和布局方法提供一个很好的起点。

如果你正在寻找类似顺风的东西，但那更以反应为中心，试试 [Chakra-UI](https://chakra-ui.com/) 。要在 Chakra 中给一个框的顶部添加边距，你需要做的就是`<Box mt={3}>...</Box>`，它会给顶部添加一个漂亮的边距，所以调整你的布局就像添加属性一样简单。

# Ecmascript 模块

Ecmascript 模块越来越受欢迎，因为人们更广泛地讨厌`node_modules`和`npm`。我明白了。`node_modules`目录可以是 gynormous。

> 顺便说一句，如果你发现你的硬盘在你的主目录下运行了`npx npkill`，它会找到所有的`node_modules`目录，并有选择地删除它们。

Ecmascript 模块实际上是浏览器自带的，并且已经有一段时间了。如果将`type="module"`添加到脚本标签中，就可以在代码中使用`import` [语句来引入 Ecmascript 模块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules)。同样的事情也适用于 [Deno](https://deno.land/) 并且也支持流行的 [Vite 系统](https://github.com/vitejs/vite)来引导 Vue 应用。

这可能是我们在开发中看到的比较多，在生产中看到的比较少的东西，因为关于将代码导入页面的 HTTP 请求的数量存在效率问题。但不管怎样，这是你应该在 2021 年至少尝试一次的事情。给德诺一个机会。这真的很简单，它利用了 99%您已经熟悉的节点。

# **微前端**

我认为 2021 年微前端主要有两种使用情形:

*   **在微站点应用程序之间重用-** 如果你已经将你的单片应用程序分解成一堆微站点，并且你对 npm 共享感到沮丧，微前端是一个很好的解决方案。
*   **小部件** -微前端基本上是 2000 年代的小部件。如果你想要一些打包的代码，让你的客户可以在他们的页面上用你的数据嵌入一些用户界面，微前端是实现这一点的方法。

制作微前端最简单的方法是使用[模块联合](https://webpack.js.org/concepts/module-federation/)，这是 Webpack 5 中的新特性。通过模块联合，您可以从应用程序中公开代码(及其依赖项),而无需将其从应用程序中移除，也无需创建任何类型的包装代码。您只需从一个应用程序中公开它，然后使用`import`语句在另一个应用程序中消费它。就这么简单。

更好的是，当公开组件的应用程序更新时，该代码的任何客户端也会立即更新。这意味着在你的站点上，或者在你和你的客户之间，实时共享代码。

Zack Jackson 和我写了一本书，[Practical Module Federation](https://module-federation.myshopify.com/products/practical-module-federation)，您可以阅读并了解如何在您的应用程序中使用这个新的 Webpack 特性。蓝领编码器频道有一个专用于模块联盟的[完整播放列表](https://www.youtube.com/playlist?list=PLNqp92_EXZBLr7p7hn6IYa1YPNs4yJ1t1)。

# **网页表现**

2020 年对电子商务来说是重要的一年，即使隔离解除(手指交叉),这一年也可能会保持下去。任何有电子商务经验的人都知道，额外的秒数意味着更低的转化率，而更低的转化率意味着更少的销售额。因此，在支持相同功能集的同时，让页面更快是一件大事。

你可以让自己成为电子商务或内容领域未来雇主的无价之宝，至少可以通过学习[如何拆分捆绑包和延迟加载](https://reactjs.org/docs/code-splitting.html)，以及学习你想要跟踪的[绩效关键指标](https://web.dev/vitals)。

# **WebAssembly**

WebAssembly 已经从一个玩笑的想法(至少对我来说)变成了一个非常真实的世界，特别是随着[微软的 Blazor 框架](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor)的发布，它使得使用 C#以类似 Vue 的风格开发 web 应用程序变得容易，但是编译后的输出是 WebAssembly 代码而不是 Javascript。这是一个令人惊叹的框架，我已经在[蓝领编码器频道](https://youtu.be/S6Tu8oXyoRk)上报道过。

但不仅仅是微软，它也是开源的。现在，您可以利用您的 Typescript 技能(是的，这也是学习 Typescript 的另一个原因)并将它们与 [AssemblyScript](https://www.assemblyscript.org/) 一起使用，使用熟悉的语法构建 WebAssembly 代码。那曾经是那种你只能用 Rust 或 Go 做的事情，现在它也是打字稿了！

如果你在 B2B 领域，在那种曾经使用 Macromedia 的 Flex 的公司，或者在 Microsoft stack 上使用 ASP 的公司，你今年会想花些时间学习 WebAssembly 工具。

# **统一工装**

节点生态系统很棒，但因为它是有机增长的，当我们浪费大量时间让所有的构建工具一起工作时，这可能是一个麻烦。因此，我们看到了像 [Rome](https://github.com/rome) 和 [Deno](https://deno.land/) 这样的项目的出现，它们拥有用于编译、运行、林挺和测试 Javascript 和类型脚本代码的一体化工具链。Deno 现在甚至允许你将代码编译成本地可执行文件。

如果你是后端开发人员，我鼓励你至少在 2021 年看看 Deno，看看 Ecmascript 模块怎么样了，没有`node_modules`运行并编译成本地可执行文件是什么样子。

# **Monorepos**

不管你喜不喜欢 monorepos，它们都不会很快消失，这是因为节点生态系统的本质鼓励开发和使用更小的软件包，它们可以协同工作，而不是单一的应用程序。monorepos 简化了一组相关库的开发。

[Yarn workspaces](https://classic.yarnpkg.com/en/docs/workspaces/) 让 monorepos 变得更加容易，现在 [npm@7](https://docs.npmjs.com/cli/v7/using-npm/workspaces) 也支持它们。这意味着对 monorepos 的支持被嵌入到我们的包管理工具中。你可以在其上放置一层 [lerna](https://github.com/lerna/lerna) 来添加软件包版本控制工具。

如果你打算在一个专业的环境中编码，你应该花一些时间学习工作空间，并确保你知道如何将库链接在一起，这样你就可以在开发过程中实时更新。

# **奖励:静态部署**

我在我的 2020 年视频中强调了静态部署，现在理解这种新旧方法的架构优势也同样重要。静态部署意味着网页服务速度快，网站不需要服务器监控。

随着 NextJS 9.3 及其后续版本的发布，开发静态部署的应用程序比任何时候都要容易。现在，您可以一页一页地选择哪些页面将在客户端呈现、在服务器端呈现或静态生成。他们让事情变得超级简单。

我有一个关于蓝领编码人员的视频[,视频中使用了 NextJS 中的所有三种部署模型。您可以以此为起点来尝试静态，或者您可以使用现有的 Create React App 或 NextJS 应用程序并静态部署它，以评估性能和稳定性优势。](https://youtu.be/2tJedF8I-8Q)

# **奖励奖励:黑暗模式和造型变化**

我知道的一个预测将在 2021 年实现，那就是黑暗模式的普及。如果你做一个内容网站，在 2021 年你将不得不考虑黑暗模式，如果你还没有的话。一些框架(包括 Tailwind 2.0)现在支持黑暗模式。查看这篇关于自动黑暗模式检测的[优秀文章](https://css-tricks.com/dark-modes-with-css/)，并调查您选择的 UI 工具包是否自动处理黑暗模式切换。

我也认为会有一个造型的改变。“扁平外观”已经有很长一段时间了，但随着[神经形态](https://uxdesign.cc/neumorphism-in-user-interfaces-b47cef3bf3a6)和[玻璃形态](https://uxdesign.cc/glassmorphism-in-user-interfaces-1f39bb1308c9)成为最大的竞争者，很有可能出现一种新的风格。

对于前端、后端 TS/JS 或全栈开发来说，这是一个激动人心的时刻！谁知道 2021 年会带来什么。但是投资在你自己的技能上是值得的。