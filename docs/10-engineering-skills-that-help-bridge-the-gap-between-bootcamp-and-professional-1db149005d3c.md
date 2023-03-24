# 帮助弥合训练营和专业人员之间差距的 10 项工程技能

> 原文：<https://levelup.gitconnected.com/10-engineering-skills-that-help-bridge-the-gap-between-bootcamp-and-professional-1db149005d3c>

![](img/42b9ac1bc55c6c0a80004ead563f3118.png)

约瑟夫·巴里恩托斯在 [Unsplash](/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

软件工程训练营可以为新的技能组合提供一个很好的基础，但它们仅仅是一个*基础，*意味着它应该建立在这个基础上。训练营不是魔法，但实际上远非如此。

在训练营结束时，被问到的问题几乎总是“我如何找到一份工作？”你必须记住，你从你的项目中得到的建议和指导，与提供给各种项目中成百上千的其他工程师的建议和指导是一样的，他们都有大致相同的资历、简历和技能，都申请了和你一样的工作。

所以，问题其实不是“我如何找到工作？”，而是“我如何让自己与众不同？”训练营很棒，但他们不能教授所有的东西，而且有一些主题和技能对于在专业环境中成为一名附加团队成员至关重要。你必须记住，在一家公司，你不再只是试图满足通过测试的要求，而是建立一些将优化功能，集成必要的安全性，并最终成为一个非凡的用户体验，让用户回来。在我看来，大多数公司对从新兵训练营雇佣工程师的最大保留是，他们有责任弥合这一差距。

弥合这一差距的技能不胜枚举，但我已经整理了一份基本技能的清单，我认为这些技能是补充你的训练营教育所必需的。至少对这些有一个基本的了解将有助于你在面试中谈论工程时脱颖而出，并使每个人融入团队的过程更加顺利。

# 1.Git 和版本控制

我猜几乎每个 bootcamp 都使用 Git 和 GitHub，然而本质上它只是用于远程存储而不是版本控制。Git 的 Bootcamp 用法与专业或团队用法非常不同，因为它服务于不同的目的。

当加入一个团队时，理解 Git 流程、最佳实践和标准约定是很重要的。Git 旨在解决与多个工程师在同一个应用上工作相关的问题，但为了做到这一点，必须正确利用它的工具。

主要的区别是，它不仅仅用于存储，而是作为一种集成代码的方式，除非您想不断地处理可怕的合并冲突，否则您应该花些时间了解这意味着什么。

## **资源**

1.  [介质:Git & GitHub 101](https://medium.com/@bryn.bennett/git-github-101-ec7850487e97)
2.  [Udemy: Git 和 GitHub 初学者速成班 2020](https://www.udemy.com/course/git-and-github-beginners-crash-course-2017/)
3.  [GitHub 指南:你好世界](https://guides.github.com/activities/hello-world/)
4.  [GitHub 指南:了解 GitHub 流程](https://guides.github.com/introduction/flow/)

# 2.请求-响应循环& HTTP

正如 Mozilla 文档所说，HTTP 是“网络上任何数据交换的基础”，所以你可以明白为什么它会出现在这个列表中。现代 web 开发在提供神奇的用户体验方面做了令人难以置信的工作，在这种情况下，事情似乎只是在我们需要的时候出现。但这当然不是魔法。数据在世界各地旅行，到达我的 MacBook 或 iPhone，这个旅程现在与作为开发人员的你极其相关。发出请求和处理响应是开发的一个重要部分，为了构建真实的、可伸缩的、复杂的应用程序，您需要对整个生命周期和用于传输数据的协议有一个坚实的理解。

## 资源

1.  [Mozilla Docs:HTTP 概述](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)
2.  [代码层次:请求-响应循环](https://www.codecademy.com/articles/request-response-cycle-static)

# 3.错误处理

作为一名专业开发人员，我不能过分强调优雅的错误处理和日志记录的重要性。未处理的错误是破坏用户体验的一种非常快速的方式。理解状态代码也属于这一类——在正确的时间发送 500 或 404 可以让您作为开发人员知道问题是什么，并相应地进行处理。有些公司会在面试中问不同的身份代码是什么意思。

不同的语言会有不同的错误处理最佳实践——对于 JavaScript，try/catch 可以做到这一点。重要的是，您有适当的代码来捕捉错误，在前端处理错误，并记录错误，即使在生产中也是如此。有些 SDK 在生产中提供错误监控和日志记录，比如 [Sentry](https://sentry.io/welcome/) 。

## 资源

1.  [Mozilla Docs: HTTP 状态代码](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
2.  [Mozilla 文档:控制流和错误处理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Control_flow_and_error_handling)

# 4.与语言无关的编程基础

这个有点宽泛，但可能是这个列表中最重要的技能。我所说的“语言不可知的编程基础”是指你真的需要理解“为什么”，而不仅仅是“什么”。训练营非常擅长教授特定的技能和语法。但是编程远不只是记忆语法。

在你的工程生涯中，你会面临新的问题，有时需要完全学习新的语言。在这些情况下，特定的记忆语法没有任何帮助。你真正需要理解的是编程的构件。所有语言都被编译成二进制代码。它们都在以不同的方式解决相同的问题，并且实际上具有相同的基础构件——循环、变量、数据结构等。如果您能够从语言不可知的层面理解这些工具实际上在做什么——当您声明一个变量或迭代某些东西时，在您的计算机和程序中实际上发生了什么——在语言之间移动和解决问题将变得非常容易。

## 资源

1.  Coursera/杜克大学:编程基础

# 5.阅读其他开发人员的代码

大多数工程工作不会从头开始构建应用程序，而是添加到现有的代码库中——可能比你见过的任何东西都更大、更复杂。因此，有必要能够遍历不是您自己编写的代码，并识别不同代码块的用途和功能。

最好的方法是开始为 GitHub 上的开源项目做贡献。即使你没有贡献，你也可以在代码库中练习识别功能。这也是一件很棒的事情，因为你会让自己接触到新的架构模式和实现目标的方法。

## 资源

1.  [GitHub:首次投稿](https://github.com/firstcontributions/first-contributions)
2.  [GitHub:趋势库](https://github.com/trending)

# 6.开发和部署

你不需要成为这方面的专家——如果你没有被专门聘用担任 DevOps 的角色，你的团队中很可能会有其他人负责这项工作。然而，正在部署的是您的代码，因此了解这个过程是有帮助的，尤其是在出现问题的时候。此外，有些概念，如可伸缩性和性能，在开发过程中也很重要。

不同的团队会使用不同的工具，你需要熟悉他们使用的工具。然而，可以肯定地说，熟悉 AWS、Docker 和 Heroku 是很好的选择。

## 资源

1.  [Udemy:2020 年终极 AWS 认证开发者助理](https://www.udemy.com/course/aws-certified-developer-associate-dva-c01/)
2.  [AWS:官方文件](https://docs.aws.amazon.com/index.html?nc2=h_ql_doc_do_v)
3.  [中型:AWS &无服务器架构](https://medium.com/@bryn.bennett/aws-serverless-architecture-c99ef21dbfb1)
4.  [Heroku:入门](https://devcenter.heroku.com/start)
5.  [Udemy: Docker 和 Kubernetes:完整指南](https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide/)
6.  [Docker:官方文件](https://docs.docker.com/get-started/)

# 7.CSS 和前端设计

说到发展，一本书永远是以封面来评判的。这适用于你接受采访时遇到的编码挑战，任何与你的名字相关的部署和活动，以及你提交的任何专业工作。

您的工作*将在应用程序加载后立即得到*的评判。在最初的判断中，即使是最少的复杂设计也会产生巨大的差异。我个人喜欢使用 Sass、Google 字体和 Lottie 动画来让我的应用程序看起来简单而时尚。

你已经部署的任何看起来不像专业应用程序的东西(特别是如果你向潜在的公司推荐它的话)，现在就去修复它。即使你申请的是后端的角色，如果你是转前端的界面，只要做一点小事情让它看起来好看就行了。

## 资源

1.  [Udemy:完整的 App 设计课程——UX、UI 和设计思维](https://www.udemy.com/course/the-complete-app-design-course-ux-and-ui-design/)
2.  Udemy:用 HTML5 和 CSS3 建立反应灵敏的真实世界网站
3.  [Medium:让你爱上 CSS 的 5 个 Sass 特性](/5-features-of-sass-that-will-make-you-love-css-25e707253ea5)
4.  正式文件
5.  [谷歌字体](https://fonts.google.com/)
6.  [洛蒂托文件](https://lottiefiles.com/)
7.  [npm: React-Lottie](https://www.npmjs.com/package/react-lottie)

# 8.安全性、授权和认证

这将包括从保护您的数据库，到保护您的路线，到安全登录，信用卡支付等一切。等等。

有一些 SDK 可以处理不同的需求，很有可能您会在专业环境中实现这些 SDK，而不是从头开始构建它们。然而，重要的是理解概念和基本原理，以便理解您正在实现的是什么。

我建议从探索密码散列、授权请求头、令牌和 Firebase 开始。

## 资源

1.  [Udemy:入侵和保护 JSON 网络令牌(JWT)](https://www.udemy.com/course/hacking-and-securing-jwt/)
2.  [Mozilla Docs: HTTP 授权头](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization)
3.  [Firebase:官方文档](https://firebase.google.com/docs/?gclid=CjwKCAjwltH3BRB6EiwAhj0IUAgvP7TJoGLuGKXKfnepKFwLDv1l_JJZZlFKVGdXgICFJ_r6TKlO_hoC02YQAvD_BwE)
4.  [介质:Firebase 101 —认证](https://medium.com/swlh/firebase-101-authentication-6aaa874aa7c4)
5.  [Medium:你宠物的名字如何成为安全(或不那么安全)的密码](/how-your-pets-name-becomes-a-secure-or-not-so-secure-password-95ceb27eb3ce)

# 9.架构:可维护性、可重用性、可读性

您在专业环境中编写的代码是一个不断发展的、有生命的东西。它将被迭代和更新，直到应用程序不再存在的那一天，很可能在你离开之后。

因此，我们的目标不仅仅是编写功能代码，还要编写易于迭代的代码。这意味着使其易于维护，尽可能多地构建可重用的组件和功能，并以一种其他人可以直观导航的方式编写代码。

因此，架构非常重要，在训练营中很少讨论。随着代码库的增长，这三件事变得比你想象的更重要。

## 资源

1.  罗伯特·c·马丁的清洁建筑
2.  [Codementor.io:干代码 vs .湿代码](https://www.codementor.io/@joshuaaroke/dry-code-vs-wet-code-89xjwv11w)

# 10.计算机科学

你不能直接和一个新兵训练营的 CS 学位竞争，但是你肯定可以给自己一个优势。计算机科学与算法和优化有关——比如大 O 符号、数据结构、图形遍历等等。这些都是编写最佳和可伸缩应用程序的重要概念。

同样，你不需要成为专家，但是你需要足够的知识来在编程时做出明智的决定。选择一个对象而不是一个数组，编写函数是 *n* 而不是 *n* ，诸如此类。

## 资源

1.  [Coursera &普林斯顿:计算机科学——有目的的编程](https://www.coursera.org/learn/cs-programming-java)
2.  Udemy:计算机科学 101:掌握编程背后的理论
3.  [中:代码中的广度优先搜索和深度优先搜索](https://medium.com/swlh/bfs-and-dfs-in-code-ba3f01c16156)
4.  [介质:数据结构——代码中的堆](https://medium.com/javascript-in-plain-english/heaps-of-data-structures-creating-heaps-in-code-80d215dcf53d)

[](https://skilled.dev) [## 编写面试问题

### 掌握编码面试

技术开发](https://skilled.dev)