# React 中的流与打字稿——我的两分钱

> 原文：<https://levelup.gitconnected.com/flow-vs-typescript-in-react-my-two-cents-d4d0c657d236>

## 打字稿和流的利弊

**更新。**写这个故事的时候，巴别塔对 TypeScript 的支持还没有发布。这极大地改进了 React 项目中 TypeScript 的集成。Expo 是 React 本地生态系统中的一个关键元素，它最近宣布将从 Flow 转向 TypeScript(在这里阅读公告)。

我现在结合使用林挺的 ESlint 和类型检查的 TypeScript。编译由巴别塔完成。

在下一个 React 项目中应该使用 Flow 还是 TypeScript？我最近对这个问题有点左右为难，我想分享一下我对这个话题的想法。如果您已经在使用 Flow 或 TypeScript，并且对您当前的设置感到满意，那么这篇文章可能是一篇评论，但是也请留下评论来分享您的设置和体验。如果您没有在 React 项目中使用静态类型检查，请允许我建议您可以通过使用静态类型检查来大幅降低开发成本。

![](img/ff15d6f7ae34d1f8014a60335e63400b.png)

[严格类型:打字稿、流、Javascript——生存还是毁灭？](https://codeburst.io/strict-types-typescript-flow-javascript-to-be-or-not-to-be-959d2d20c007)

Flow 和 TypeScript 都是限制 JavaScript 项目中技术债务的极好方法，允许您满怀信心地发展您的代码库。我总是试图对静态代码分析施加最大的压力。我使用类型作为我的应用程序的不同组件之间的真实的单一来源。每当我被一个 bug 绊倒时，我都会问自己:“这可能是静态分析发现的吗？”类型安全也是我喜欢 React 胜过 Angular 或 Vue 等其他框架的原因之一。有了 JSX，我们有了一个跨越应用程序逻辑、样式和表示的统一类型系统。其他库似乎没有相同级别的类型安全。

所以 JavaScript 类型的系统很棒。但是应该使用 Flow 还是 TypeScript 呢？

## 进退两难

如果您正在构建一个 React 项目，TypeScript 是一个更自然的选择:它与 [create-react-app](https://github.com/facebook/create-react-app) 和[material-ui](https://material-ui.com/)(React 应用程序中 UI 的一个常用库)具有对 TypeScript 的一流支持。在 React Native 中，流动感觉更自然。Expo 是用 Flow 实现的，据我所知[create-react-native-app](https://github.com/react-community/create-react-native-app)没有很好的类型脚本支持。

有一段时间我用 TypeScript 写 React projects，用 Flow 写 React Native projects，一切都是爱，一切都很好。

然而对于一个新的 React 项目，我考虑使用 Flow。这将使我能够在所有代码库中保持一致的代码风格和林挺规则。此外，对 React 在 TypeScript 和 Flow 中的支持变得越来越复杂，最好只在这两种类型系统中的一种中发展强大的专业技能。在这里，我开始为`material-ui` [开发一个强流类型定义，我真的需要一些帮助🙏🏻。](https://github.com/wcandillon/material-ui-flow)

下面是我思考这个问题时整理的清单。

# **流程:优点&缺点**

## **优点**

*   **Eslint 插件**💄:React 和 React Native 的 Eslint 插件提供了有价值的静态分析，似乎优于它们的 tslint 插件。
*   **使命宣言**💂🏼:TypeScript 的最初目标是提供现代 JS 特性以及静态类型。现在 JS 已经很好地赶上了现代特性，TypeScript 仅仅被认为是一个类型系统，这就是 Flow。
*   **Premium React 支持⚛️:**react 的流量支持有三个方面。首先，来自 Flow 的 React 类型定义比 TypeScript 更精确一些。您可以轻松地键入组件子组件。高阶组件的类型支持是无可挑剔的。其次，TypeScript 的主要特色是 OOP，而 Flow 的特色是函数性的。React 的主要特色是功能性的:组件是通过组合而不是继承构建的，这使得 Flow 非常适合。最后但同样重要的是，Flow 是一个 Babel 插件，大多数 React 项目已经将它集成到他们的构建系统中。
*   **轻松集成&自动化重构**🤖:由于 Flow 基于 Babel，所以在各种工具链中集成得非常好。我可能永远不需要这么做，这听起来可能有点牵强，但是如果它是基于 Babel 的，你就能够为你的项目构建自动代码重构。

## 骗局

*   **疯狂的版本改动**😱:从 Flow 的一个版本到另一个版本的变化有时会让人感到有点不知所措。这可能是由于该项目比 TypeScript 更新得多……以及来自其母公司的*“快速移动和打破事物”*座右铭。
*   **未记录的特征**📚:并非流程中的所有公用设施类型都有记录。虽然这个*可能很快就会被修复。*
*   **流程结果检查器**🤨:这个有点大。Flow 不提供消除来自 *node_modules* 的类型错误的能力，这些错误是您无法控制的。默认行为应该报告所有错误，这是有道理的，但是[应该有一个标志来消除它们](https://github.com/facebook/flow/issues/869)。有一个非常有用的名为 [flow-result-checker](https://github.com/jbreckel/flow-result-checker) 的包可以做到这一点，但是它不能用于最新版本的 flow，因为它的报告格式已经改变了。
*   **隐式 any in 类型定义**🤦🏻‍:我注意到在编写库定义时，类型导入被隐式地当作 any 而不是抛出一个错误(如果导入是在模块声明之外完成的)。这有点可怕，因为您可能认为您的代码正在接受类型检查，而实际上并没有。希望库定义是唯一可能发生这种情况的地方。

# **打字稿**:赞成&反对

## 赞成的意见

*   **更大的社区**👨‍👨‍👧‍👧:因为它是一个较老的项目，所以它的社区更大。但这不一定意味着流量就赶不上了。目前，TypeScript 库定义的质量比用 Flow 编写的高一个数量级。在 TypeScript 中 IDE 支持似乎更好。就我个人而言，我使用的是 Atom IDE，所以这对我来说不是一个因素。
*   **比流量快**🏎:在笔记本电脑上工作时，TypeScript 比 Flow 快得多，似乎也没有 Flow 消耗那么多电池寿命。
*   **前期编译错误**🚨:这可能可以在 TypeScript 和 Flow 中进行配置，但是在测试代码时，使用预先编译错误而不是非强制性的类型检查似乎可以节省时间，因为它可以防止您运行伪造的代码。

## 骗局

*   **较小的 React 支持** ⚛️: React 支持在类型脚本中不如在流中好，尤其是在创建高阶组件时。然而，这种情况将来可能会改变。

## 你对此有什么想法？

你同意这个列表或者有什么建议吗？留言评论！我期待着听到你对此的想法。

不要脸的 plug:你在找一个高级的 React 原生入门套件吗？您应该签出 React 本机元素。

[](https://hackernoon.com/react-native-sketch-elements-889f010f9626) [## 反应原生草图元素

### 经过两个月的制作，React 本地草图元素终于出现了。反应原生元素涵盖了广泛的…

hackernoon.com](https://hackernoon.com/react-native-sketch-elements-889f010f9626) [![](img/9914c5dd23ac08b70eea6f4f9ba6fed2.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/typescript) [## 学习 TypeScript -最佳 TypeScript 教程(2019) | gitconnected

### 18 大 TypeScript 教程-免费学习 TypeScript。课程由开发人员提交并投票，从而实现…

gitconnected.com](https://gitconnected.com/learn/typescript)