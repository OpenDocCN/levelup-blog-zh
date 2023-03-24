# 24 小时内学会反应

> 原文：<https://levelup.gitconnected.com/learning-react-in-24-hours-b768642176aa>

## 我学习 React 的经验是没有 Javascript 专业知识

![](img/aeee0424bd81fb77542abf9663a4c8a0.png)

在受到我的“[在 24 小时内学会 Golang](https://www.kislayverma.com/post/learning-golang-in-24-hours)”练习的启发后，我决定通过学习 [React](https://reactjs.org/) UI 框架来进一步扩展我的开发工具包。当然，我不得不保持相同的主题，所以这将是一个“24 小时内学习反应”的狂欢，并将导致一个简单的 CRUD UI 在我用 Go 构建的 CRUD 服务之上。

当然，有些事情是不同的。我几乎整个职业生涯都是后端工程师。我最后一次做任何有意义的前端工作是在大约五年前，当时我使用 [Backbone js](https://backbonejs.org/) 为我现在已经不存在的图书和旅游评论网站的用户界面编码。即使这样，我也曾试图通过建造一些东西来学习新技术——我学到了[这出戏！框架](https://www.playframework.com/)、 [ElasticSearch](https://www.elastic.co/) 、基础 [Akka](https://akka.io/) 演员、Backbone.js 来搭建。然而，从那以后，我更深入地研究了后端堆栈，根本没有接触过 UI。因此，学习体验不仅包括学习 React，还包括学习前端领域。

此外，在过去的 6-7 年里，前端世界已经远离了像 Backbone、 [Angular](https://angularjs.org/) 、 [JQuery](https://jquery.com/) 和 [YUI](https://yuilibrary.com/) 这样的工具。这些都是强大的工具，但它们的力量来自于非常擅长于促进用户界面开发的旧范式。像 React 和 [Vue](https://vuejs.org/) 这样的下一代工具越来越倾向于模糊网络、移动、可穿戴设备等用户界面开发之间的界限。然后是 pwa、原生技术等等，所有这些都代表了前端技术中的一场架构革命。我不得不走很多路才能看到起跑线。

更复杂的是，我也不懂 Javascript。因此，学习经历还有另一个层面——我努力学习 Javascript 的基础知识，同时也学习 React。我想先坐下来学习语言，但意识到这是行不通的——我必须构建一些东西，为什么要用“纯”Javascript 构建一些东西，然后在此基础上找出 React。所以不管是好是坏，我会立刻解决所有这些事情。

我已经被反复警告过 React 世界中复杂的工具链，所以我非常谨慎地坚持绝对的基础。除了官方的 react 初学者教程之外，我绝对没有使用其他框架。我从 create-react-app 开始，除此之外没有添加任何其他模块。选择的 IDE 是 Atom。

所有的代码都可以在 [Github](https://github.com/kislayverma/react-crud) 上获得。看看吧——我总是很乐意让我的代码得到审查！

以下是我从这次经历中得到的收获。

# TL；速度三角形定位法(dead reckoning)

总而言之，我要说的是，虽然我设法获得了对基本 React 的表面处理，但我无法像我希望的那样习惯 Javascript。

# 好人

## 用你所学的不要退步

构建基本屏幕的体验与构建网站的感觉非常相似。我已经忘记了当时学到的所有 Javascript 语法，这次基本上也是从头开始。

## React 对后端友好

学习 React 实际上是一次非常好的经历。第一次，在 Javascript 代码中移动对我来说感觉很熟悉——代码的结构比我记忆中的 JQuery/Backbone 时代要好得多。面向对象的本能在这里找到了很好的立足点——有类，状态很好地代表了实例变量。

语法更加自由，但是足够接近，我可以相当容易地搜索代码。事实上，定义类和变量来直接支持 UI 元素让我想起了在 JSF 工作时，支持数据字段并根据对它们的更改重新呈现 UI。学到的东西:Javascript 列表和映射，if switch 和 for 循环，React 组件基础，事件处理程序，状态管理和基于用户交互或其他事件动态更新 UI，调用 REST API 并渲染结果，代码组织和本地构建。

## React 是一个庞大的生态系统

上面提到的学习只是 Javascript 和 React 整体能力的一小部分。虽然我没有涉及 React 工具包的太多内容，但我觉得我可以比使用老一代框架更容易地探索它。仍有大量的内容有待探索，这意味着尽管通过构建来学习是有效的，但要接触到这些技术词典中相当可观的一部分，还需要构建很多。我还没有深入研究 React 的构建和部署——“NPM start”不符合要求:)

# 坏事

## Javascript 中的弱类型

虽然 Java 添加的函数式语法和构造使理解 Javascript 代码及其函数传递和处理程序变得更容易了(对我来说)，但弱类型仍然是理解发生了什么的巨大障碍。随着代码变得越来越大，我发现自己越来越纠结于不同变量是什么。

# 丑陋的

Atom 缺乏自动完成功能，这实在是太糟糕了。我不得不经常谷歌，因为没有办法探索任何对象公开的方法。

# 现在怎么办？

许多人告诉我，如果我想要类型化的 Javascript，我应该探索 Typescript，但是目前我对前景还没有足够的热情。我要在这里暂停一下，称这是一次不冷不热的经历，就此打住。我的下一个项目是构建一个思维导图软件来组织我的笔记和想法，但是自从发现了 [Roam Research](https://roamresearch.com/) 之后，这种需求就被暂时解决了，而且在我看来还没有真正的项目来测试我新发现的反应技能。

当我最终开始为 [Rulette](https://www.kislayverma.com/post/rulette-a-pragmatic-rule-engine) 构建管理界面时，我会看到有多少东西可以派上用场，或者我是否会忘记一切(又一次)。