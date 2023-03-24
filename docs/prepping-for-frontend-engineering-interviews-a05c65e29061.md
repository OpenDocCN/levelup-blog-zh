# 为前端工程面试做准备

> 原文：<https://levelup.gitconnected.com/prepping-for-frontend-engineering-interviews-a05c65e29061>

![](img/2bf3f597543416c38c79f8e4b1867de2.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的 [ThisisEngineering RAEng](https://unsplash.com/@thisisengineering?utm_source=medium&utm_medium=referral)

本周，我将在脸书开始一项新的工作，担任高级前端工程师。最近经历了面试过程，我想我可以分享我用来准备前端面试的技巧和资源，以及一些通用的面试准备技巧。由于像脸书这样更大、更成熟的公司倾向于关注基本概念，而不是一个人使用框架的能力，所以我将关注我如何更新和巩固我对普通 JavaScript 和 DOM APIs 的理解。

# 前端实践

熟练掌握 JavaScript 语言并彻底了解浏览器 DOM APIs 对于通过前端面试非常重要，对于成为一名出色的前端工程师也非常重要，当他们遇到前端框架的极限时，他们可以解决低级前端问题。过去，我已经通过[在没有库或框架的情况下构建复杂的 UI 模式](https://www.willhastings.me/posts/building-a-vanilla-js-image-zoomer)投资了这个技能集。在准备最近的面试时，我利用了以下资源和技巧来复习。

## JavaScript.info

我发现一个对重温这些话题特别有用的网站是 [JavaScript.info](https://javascript.info/) 。它有许多关于 JS 语言、内置浏览器/DOM API 和各种其他主题的文章，包括 web 组件和正则表达式。我读的文章简洁明了。一些特别有用的方法包括:

*   [DOM 树](https://javascript.info/dom-nodes)
*   [DOM 导航](https://javascript.info/dom-navigation)
*   [节点属性](https://javascript.info/basic-dom-node-properties)
*   [属性和特性](https://javascript.info/dom-attributes-and-properties)
*   [日期和时间](https://javascript.info/date)

它尤其帮助我重温了 DOM 中节点的核心特征，如何遍历和操作它们，以及不同的类型(元素、文本、注释)。

## 安基

由于我不一定能够在面试中查找 web APIs(我通常的参考是 [MDN](https://developer.mozilla.org/en-US/) )，所以我想记住重要的 API。我发现一个有用的记忆信息的工具是 [Anki](https://apps.ankiweb.net/) ，这是一个闪存卡应用程序，管理你应该多久检查一次给定的卡片。当一张卡是新的时，它会让你在一次会议中回顾几次，并报告这个答案有多容易回忆起来。随着时间的推移，它会增加时间(天数、周数等)。)根据你回忆信息的难易程度，向你展示同一张卡片。它有桌面、iOS 和 Android 版本，以及在它们之间同步你的卡的免费在线支持。

我使用 Anki 排练信息，包括:

*   JS [映射](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)和[设置](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)API
*   JS [日期 API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)
*   JS 数组排序回调如何工作
*   在 DOM 中添加、移除和替换节点的方法
*   获取 DOM 节点/元素的父节点、兄弟节点和子节点
*   [DOM 节点继承层次](https://javascript.info/article/basic-dom-node-properties/dom-class-hierarchy.svg)
*   [的返回值 getBoundingClientRect](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect)

## API 示例

在某些情况下，我发现通过创建以后可以引用的代码示例来试验某些 JS 特性和浏览器 API 是很有用的。对我来说，实际上写代码比仅仅阅读一个主题更能巩固知识。我还喜欢用注释来注释我的代码示例，用我自己的话来解释概念，这是增强我对这些技术主题的理解和回忆的另一种有用的技术。

例如，下面是我的[代码示例，展示了静态和动态节点列表](https://github.com/whastings/coding_practice/blob/master/javascript/frontend/nodeList.html)之间的不同:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>NodeList</title>
</head>
<body>
  <div class="foo"></div>
  <div class="bar"></div>
  <div class="foo"></div> <script type="module">
    const bodyChildren = document.body.childNodes
    const barNodes = document.querySelectorAll('.bar') const newElement = document.createElement('div')
    newElement.className = 'bar'
    document.body.appendChild(newElement) console.group('Live NodeList')
    console.log(
      'bodyChildren[bodyChildren.length - 1] === newElement',
      bodyChildren[bodyChildren.length - 1] === newElement
    )
    console.groupEnd('Live NodeList') console.group('Static NodeList')
    console.log('barNodes.length', barNodes.length)
    console.groupEnd('Static NodeList')
  </script>
</body>
</html>
```

## 预习练习面试

20 年 8 月 5 日更新:不幸的是，PrepTick 可能不再运行，因为他们的网站目前不工作。

[PrepTick](https://www.preptick.com/) 是一项付费服务，它为你匹配另一名工程师，该工程师可以与你进行模拟远程编码面试。他们给我找了一位经验丰富的工程师，他在一家与脸书规模相似的公司工作，能够给我一次很好的前端编码面试机会。之后，他向我提供了书面反馈，说明我做得好的方面以及我可以改进的地方。这与现场面试并不完全相同，但它给了我练习核心技术面试技巧的机会，比如提出澄清性问题、大声思考，以及在不运行代码的情况下验证代码的正确性。

还有其他一些服务可以让你和你的同事进行免费模拟面试，你和你的同事可以轮流面试对方，比如 [InterviewBit](https://www.interviewbit.com/mock-interview/) 和 [Pramp](https://www.pramp.com) 我最终也使用了其中的一个，我将在后面讨论，但是如果你时间紧迫，想要确保你得到一个良好的模拟面试体验，并且不介意花一些钱，PrepTick 是一个很好的解决方案。

## 其他前端资源

除了我在上面详述的资源和方法之外，以下内容也很有帮助:

*   [准备顶级科技公司的前端 Web 开发面试](http://web.archive.org/web/20200723030731/https://davidshariff.com/blog/preparing-for-a-front-end-web-development-interview-in-2017/):这篇来自亚马逊一位工程经理的帖子涵盖了你在准备前端面试时应该学习的话题。我特别喜欢它解决了前端系统设计面试，这是一个我很难找到指导的话题，因为系统设计面试传统上是以后端为重点的。
*   [Performant 前端架构](https://www.debugbear.com/blog/performant-front-end-architecture):这些年来，我阅读了很多关于前端性能的优秀文章，这篇文章很好地总结了一些技术和重要的关注领域。如果你以前深入研究过前端性能文献，这是一个很好的复习。如果你还没有，我推荐这篇文章作为你可以进一步研究的主题列表。

# 一般面试准备

面试一个前端职位需要你的面试官根据许多适用于其他工程职位的相同标准来评估你。这些标准包括一般的问题解决方法和在回答行为面试问题时展示软技能。除了上面讨论的特定于前端的技术和资源之外，我还在这些方面使用了一些技术和资源。

## 写出我的职业故事

重要的是能够以令人信服的方式讲述你的职业生涯，让面试官保持兴趣，同时提供适当的细节。为了收集我的想法并练习如何讲述我的故事，我写了一个大纲，总结了我在过去几份工作中参与的主要项目和达到的主要里程碑。我在采访前会定期回顾一下，这样细节就会在我的脑海中记忆犹新，这样我就可以在讲述我的故事时不用费力组织我的想法或记住各种细节。

## 创建备忘单

对于大多数类型的面试来说，有一些重要的步骤和行为可以帮助你成功。许多人已经在技术、系统设计和行为访谈的背景下写了这些。在阅读完它们之后，我通过写“备忘单”总结了我所学到的东西，我可以在面试之前回顾这些备忘单，这样我就可以记住重要的步骤，比如在我编写代码之前与面试官讨论我提出的解决技术问题的方法。

例如，以下是我为技术面试写的内容:

```
- Step 1: Clarify the problem
    - Repeat it back in your own words
    - Ask about inputs, outputs, edge cases, performance
    - When/if to validate inputs
    - Error handling
- Step 2: Solution Design
    - DON’T CODE YET
    - Consider some example inputs
    - Explain and discuss possible solutions
    - If none of your approaches will work, explain why
    - FOCUS ON TRADEOFFS
    - DISCUSS TIME AND SPACE COMPLEXITIES
    - Come to agreement w/ interviewer on solution to try before coding
    - Pseudocode if needed
        - But let interviewer know real code is coming
    - ASK: Do you want me to code this now?
- Step 3: Code
    - TALK THROUGH EVERYTHING
    - Confirm you can use the data structures or libraries you want to use
    - Use data structures generously
        - Existing or of your own creation
    - Step through code with example input
    - Abstract away logic into helper functions that you can write later
        - Later, confirm if the interviewer still wants you to write them
- Step 5: Test Code
    - REVIEW EVERYTHING
    - Carefully analyze bugs before fixing
- Step 5: Optimize
    - Increase performance
    - Add error handling
    - Handle edge cases
        - e.g. Empty input, input of size one, input with duplicates
    - Refactor into multiple functions
- Step 6: Check with interviewer if you’re done
```

除了创建备忘单之外，我还在我的 Anki 卡片组中添加了卡片(参见前面关于 Anki 的部分),上面有每张卡片的要点，以帮助我记住它们。

除了涵盖不同类型面试的重要步骤的备忘单，我还创建了一个备忘单，上面列有常见的行为面试问题，以及如何回答每个问题的笔记。我经常查看这张表，并让我的妻子对我的每个问题进行测验，看看我对自己的答案记得有多好。

以下是我准备的几个问题示例:

*   告诉我你做过的一个有趣的项目或者你做得最好的时候。
*   你想在 2-5 年内达到什么目标？
*   告诉我你遇到的一个具有挑战性的 bug，以及你是如何解决的。
*   你如何指导和提升你周围的人？

## 行为模拟面试

除了模拟面试作为技术面试的练习，我还使用模拟面试匹配服务 [Pramp](https://www.pramp.com/dev/uc-behavioral) 进行了一次模拟行为面试。我发现这是目前唯一支持这种模拟面试的服务。它为我找到了另一位工程师，我可以和他轮流面试。在我们预定的时间前一天左右，我收到了一封电子邮件，上面列有我在面试时会问的问题，这样我就可以提前复习了。

会议期间，我们有足够的时间互相采访并给出反馈。后来，Pramp 提供了一个结构化的表格，通过它我可以向我的同事提供更正式的反馈。总的来说，我发现这次经历很有帮助，是一次在更像真实面试的环境中练习的机会，我也从同事那里得到了宝贵的反馈，帮助我在现场做得更好。

虽然进行和接受模拟面试都意味着整个面试过程需要更长时间，但从面试官的角度体验面试并了解另一位工程师如何处理行为问题也是有帮助的。我发现这也是结识新朋友的好方法。会话结束后，Pramp 会让您选择让您的同事连接，如果您的同事同意，它会与您的同事共享您的电子邮件。因此，除了有益的面试实践，我最终建立了一个新的职业关系。

# 结论

这是我在最近的工作转型中准备面试时使用的最重要的资源和方法的概述。我对他们与其他工程求职者的经历相比如何很感兴趣，所以如果你有想法要分享，请让我知道。