# 如何远程面试软件工程师

> 原文：<https://levelup.gitconnected.com/how-to-interview-software-engineers-remotely-8a2b5e890a2a>

## 我在疫情雇佣程序员的经历

![](img/c7278b099435a4622dab8ca3d1431ae2.png)

由[路](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[车头](https://unsplash.com/@headwayio?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄

我是一名软件工程师，也是一个开发小组的组长。最近几个月，我的团队不得不填补几个职位，我们需要根据这个疫情更新我们的招聘流程。我们正常的面试流程是让人们参加面试，让团队成员与他们见面，并涉及一些白板编码。诚然，这是一个非常过时的进程，在 2 月之后，所有这些都不再是一个选项。

这给了我们一个很好的现代化机会，所以我与管理层和其他首席工程师合作，提出了一个更好的解决方案。

一些非常大的科技公司的面试过程会持续很多天，但是没有人会忍受像我们这样的小公司。我们需要一个适合候选人时间表的流程。

我们决定使用一个名为 Coderbyte 的网络平台来进行一次有针对性的技能测试。然后，我们利用微软团队进行“面对面”采访。在使用这种方法时，我面试了几个候选人，并最终聘用了其中的两个。

以下是我们如何成功进行面试的:

# Coderbyte

Coderbyte 有一个提供面试准备的[开发者端](https://coderbyte.com/)和一个提供面试能力的[雇主端](https://coderbyte.com/organizations)。我们使用雇主方创建了一个简短的技能测试，以验证候选人的基本知识和沟通技巧。我们的目标是设计一个测试，不到一个小时就能完成，但能让我们深入了解候选人，剔除那些明显不适合的人。

在 Coderbyte 中设置技能测试并不困难。我们选择了一种使用 4 到 8 道选择题、3 道简答题和 3 道“挑战”题的形式。Coderbyte 允许您从现有问题中选择或编写自己的问题。除了挑战性问题，我们选择根据过去用过的面试问题编写自己的问题。

下面我将解释方法，而不是问题本身，因为 Coderbyte 提供了大量的免费问题供使用，也因为“[我应该问什么问题](https://www.indeed.com/hire/c/info/best-interview-questions-to-ask-candidates)[在面试中](https://business.linkedin.com/talent-solutions/resources/interviewing-talent/software-engineer)”是一个被谈论得太多的话题。这篇文章是关于为什么这种形式有效，以及在每一类中应该关注什么。

![](img/0d0c7058d7cb2c19c5b784d084300d03.png)

照片由[费伦茨·阿尔马西](https://unsplash.com/@flowforfrank?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

简答题是根据职位、编程语言和职位技能水平量身定制的，旨在确保候选人理解其工作所需的语言。特别是，我们可能会问一两个关于我们使用的框架或架构的概念性问题，以及几个显示语言基础知识的“这段代码会输出什么”问题。如果一个候选人不能正确回答这些问题(或大部分正确)，我们会马上知道他们不适合这个职位。

对于简答题，问题本身并不重要。这些问题的主要目的是看候选人的书面交流有多好。在当前的远程工作环境中，成为一名强有力的沟通者是一项关键技能。

最后，Coderbyte 提供了“挑战”问题，即简短的编码问题。这里我们使用了 Coderbyte 库中的内置问题。Coderbyte 记录候选人的工作，进行一些基本的抄袭检测，然后根据多个输入测试答案，以生成一个分数。我们在这一部分寻找编程风格和正确性。

我们对照我们自己的工程师在相似的位置上校准这些测试。当我们的工程师在他们的水平下的测试中得分高于 70%并且在大约 45 分钟内完成时，我们认为测试已经准备好了。

在成功的技能测试之后，候选人会接到人力资源部门的电话，以确定可能的开始日期，分享福利待遇，并让他们问一些基本问题。负责团队的经理通过电话或 MS Teams 电话进行自我介绍，并决定候选人*是否可以开始与我们合作(工资范围、搬迁费用、工作时间等)。).如果这两个快速的电话都进展顺利，那就是团队面试了。*

# 微软团队面试

![](img/c8fb46523beb841ba53f8bc8ab33d9d5.png)

由 [Austin Distel](https://unsplash.com/@austindistel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

对于最后的“团队”面试，我们将从一个十五分钟的虚拟会议开始，包括我自己、一两个其他的高级工程师和一个初级工程师。我们首先回顾了候选人的 Coderbyte 测试，记录了多项选择部分的错误答案和挑战部分的问题或风格问题。我们也看了候选人的简历。

接下来，我们创建了一个大约 50 行代码的样本，它足够完整，可以编译和运行，但是其中有一些 bug。我们将把它用于动态调试会话。我们的代码直接来自我们开发的产品，并被简化成独立的组件。我们也可以用这个来描述我们商店的代码风格。

然后我们准备好了我们的视频采访。视频很重要，因为它能帮助你们所有人建立联系，让候选人对团队有更好的感觉。

采访形式本身如下:

*   不到五分钟:经理介绍团队和候选人。经理在介绍结束后离开面试。
*   五分钟:团队领导介绍他们自己和项目/公司，包括团队遵循的流程(敏捷、monorepo 等)的快速介绍，以及这次访谈的形式。这让候选人知道接下来会发生什么，以及他们哪里有时间提问。
*   五分钟:快速介绍面试中的工程师，包括他们对项目、经理、流程和公司的看法。
*   十分钟:让候选人自我介绍。问一些温和的问题:你为什么想在这里工作，什么听起来不错，什么听起来很难，这个项目有什么有趣的地方，等等。
*   十分钟:复习考德比特的问题。我最喜欢回顾挑战性问题(屏幕分享)，让候选人解释他们的想法。如果任何挑战性问题不正确，这是一个谈论边缘案例或改进的机会。
*   十五分钟:接下来，是现场调试时间了。我们会通过团队的文件共享功能来共享文本(警告:我们经常不得不将其重命名为。txt，因为团队可以阻止多种文件格式)，请候选人分享他们的屏幕，并从客户可能发现的问题开始，例如:“当我按下此按钮时，没有任何反应。”我们会让候选人告诉我们如何从那里调试。第一个 bug 相当简单。我们引入的代码中会有第二个更难发现的 bug(希望如此)。这是为了看看当我们引入第二个问题时，候选人是如何寻求帮助的。寻求帮助是优秀候选人的一个很好的标志，尽管这样做很令人沮丧。
*   剩余时间，通常是 10 分钟:最后，轮到候选人问答。希望我们已经建立了一些友谊，这样候选人就可以坦率地问那些他们可能不想问管理层或公司代表的“棘手”问题。关于我们的直接经理和“真正的”公司文化的问题是常见的，也是受到鼓励的。

面试结束后，我们的内部团队会安排最后 15 分钟来讨论候选人。通常这很简单。我们问自己，候选人是否很适合团队，以及我们认为他们将属于哪个级别(初级开发人员、开发人员、高级开发人员等)。然后，我们会向管理层提出建议，由管理层和候选人来寻找对他们有利的交易。

![](img/f56dc62ee9de0b79474a33cf3efd87b4.png)

图片由[穆罕默德·哈桑](https://pixabay.com/users/mohamed_hassan-5229782/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3738261)拍摄，来自[皮克斯拜](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3738261)

现在你知道了。对候选人来说不太繁重(他们的时间略多于两个小时)，对大型工程团队来说不需要太多时间，这是一个很好的方法来判断一个新来的候选人，同时淘汰那些可能不应该被面试的人。最初几次创建这些测试对我来说很慢，但是一旦我有了这个过程的经验，我很快就能够构建测试了。

我们公司使用这个系统成功地找到并雇佣了几个优秀的开发人员。我希望，即使我们能够回到白板上的面对面访谈，我们选择不这样做，而是继续这种新的方法。

如果你觉得这很有帮助，看看我们关于面试和面试准备的其他文章:

[这个简单的面试问题也是最难回答的](https://medium.com/illumination/this-simple-interview-question-is-also-the-hardest-to-answer-b90c72ecb63d)

[这是面试准备中最容易被忽视的一步](https://medium.com/the-innovation/this-is-the-most-overlooked-step-in-interview-prep-d7211ecbc165)

[Citizen Upgrade](https://medium.com/@citizenupgrade) 是一个涵盖技术、社会和个人发展的专家社区。请访问我们的[网站](https://citizenupgrade.com)，访问[脸书](https://www.facebook.com/citizenupgrade)，或者访问[推特](https://twitter.com/CitizenUpgrade)。