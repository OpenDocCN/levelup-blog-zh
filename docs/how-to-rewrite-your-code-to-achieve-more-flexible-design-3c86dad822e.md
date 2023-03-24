# 如何重写代码以实现更灵活的设计

> 原文：<https://levelup.gitconnected.com/how-to-rewrite-your-code-to-achieve-more-flexible-design-3c86dad822e>

## 干净的代码和重构|固体系列

## 终极指南，举例说明如何使用单一责任原则打破等级和分离问题

![](img/513461976a641b7c8c897d0e4145218b.png)

干净代码 *是一门软件开发学科，旨在通过引入实践、原则和模式来提高开发者的编程技能，从而提高 IT 公司的活力。遵循*干净代码*的程序员成为他们领域的专业人士和领导者。*

单一责任原则或 SRP 代表这样一种思想，即每个类或模块应该有且只有一个*责任*。换句话说，每个类或模块都要做一件事，并且做好。

# 软件工程中的责任是什么意思？

罗伯特·c·马丁把责任定义为改变的理由。不要把它和我之前在博客中提到的改变软件的四个主要原因相混淆。

[](https://medium.com/swlh/four-reason-to-change-your-software-bf535916152d) [## 更换软件的四个理由

### Michael Feathers 在《有效使用遗留代码》一书中定义了更改代码的四个主要原因。我…

medium.com](https://medium.com/swlh/four-reason-to-change-your-software-bf535916152d) 

这个*改变*的原因更多的是和模块或者应用特性有关。在理想情况下，一个特性的行为改变意味着一个类或模块要修改。

# Unix 中的 SRP

SRP 是软件设计中一个众所周知的原则，我们几十年前就知道它非常有效。

*Unix* 就是基于这种做一件事的原则，并且做得很好。 *Unix* 基于命令行，所以每个命令都是一个小的可执行程序。它有办法将这些可执行文件组合成更大规模的程序。

Unix 的成功延续了 Linux 的成功，所以我们知道有一些例子说明这个原则可以让系统变得非常稳定和长寿。

如果你更感兴趣的话，我找到了一个包含 1970 年以来历史提交的[库](https://github.com/dspinellis/unix-history-repo)。

# 为什么 SRP 很难实现？

在寻找答案的过程中，我看到了来自用户 Pawan Bhadauria 的一个很棒的 Quora 答案，我想引用其中的一段。

> 考虑下面的句子:“Tihs snetnece 是 hrad 到 raed”。
> 
> 我敢打赌，你能读懂这个句子，因为你是把这个单词作为一个整体来理解的，而不是把每个字母连在一起，然后用它造一个句子。虽然它与原理没有直接关系，但让我们一瞥大脑是如何处理事物的。我们的大脑非常善于观察一系列行为，并把它们组合在一起，即使这些行为构成了不同的责任。

我们倾向于根据我们的本性对行为进行分组。人脑的力量也是开发者在尝试应用 SRP 时的克星。

# 违规指标

最重要的指标是类或函数的大小。*代码的物理行数*测量函数大小。另一方面， *职责的*数量*衡量班级规模。*

> *“班级的首要原则是规模要小。班级的第二条规则是他们应该比那个小。”—罗伯特·马丁*

正确命名类的困难也是指标之一。该名称最长应为 25 个字符，不要使用“如果”、“和”、“或”或“但是”等词语。

有时，命名类的问题通过提出一个观点并使用程序员想到的最常见的名字来结束，如`OrderService.`

订单从装筐到收货的处理过程，不能用这么简单的名字来定义。

# 辅导的

## 先决条件

读者应该对*面向对象编程或* OOP 有基本的了解。基本了解*单元测试*也是合适的，但不是必须的。我是用 C#语言编程的，但 Java 或其他 OOP 语言开发人员理解这个概念应该没有问题。

## 项目介绍

存储库由两个项目组成。第一个是名为`BusinessLogic.`的类库，它包括两个文件夹；`Before`和`After.`

文件夹`Before`包含类`FileStore`，它负责将消息存储到文本文件中。

文件夹`After`包含由三个职责完全相同的类组成的模块，作为一个`Before/FileStore`类。目录的内容是应用 SRP 过程的结果表示。

第二个项目`Tests`也有两个同名的文件夹。每个文件夹都有一个包含完全相同的单元测试的类。测试类`Before/FileStoreUnitTests`几乎与测试类`After/MessageStoreUnitTests,`相同，但是它的测试针对的是不同的被测系统[顾名思义。](https://en.m.wikipedia.org/wiki/System_under_test)

## 确定责任

要点如下图展示了这个职业的样子。

[FileStore.cs](https://github.com/DannyRusnok/Single.Responsibility.Principle.Example/blob/master/Single.Responsibility.Principle.Example/Before/FileStore.cs)

你们中的大多数人都能识别出文件存储类的职责是什么。首先，你应该想到的是*储存信息*。

另一个更明显的职责是*日志*。命令方法`Read`和`Save`都会调用 [NLog](https://nlog-project.org/) 函数来记录当前发生的事情。

最后一个不太清楚，所以我会告诉你。这就是我所说的*编排*。控制前面两个方面如何相互作用的方式也是责任。

就是这样！我们确定了所有三个，让我们重复一遍。

*   存储消息
*   记录事件
*   协调互动

## 下课

打破类实际上是另一个众所周知的原则的应用，叫做[](https://en.m.wikipedia.org/wiki/Separation_of_concerns)*关注点分离。*

*我喜欢架构导师维克多·凯恩对“什么是软件工程中的关注点分离？”这个问题的回答*

*我来引用一下答案的开头；*

> *IT 中关注点分离的哲学旨在防止不同领域“关注”的代码之间的“病态”依赖，同时试图将所有与单个“关注”相关的代码隔离。*

*"..隔离所有与*单一关注点*相关的代码。”听起来很熟悉吧？现在，让我们打破`FileStore.`*

*我从提取*日志*关注点开始。新的`StoreLogger`类封装了关于将事件记录到其方法中的实现细节。`StoreLogger`的初始化是在`FileStore`构造函数中进行的，方法是从与 NLog 函数调用之前相同的地方调用的。*

*FileStore.cs 之前和之后的州际*

*[StoreLogger.cs](https://github.com/DannyRusnok/Single.Responsibility.Principle.Example/blob/master/Single.Responsibility.Principle.Example/After/StoreLogger.cs)*

*现在，我们需要分离*存储消息*和*编排*。让我们在头脑中逻辑地分开两个实现细节；*存储*和*数据*。在我们的例子中，存储是一个文件系统，数据是消息。*

*当我们考虑依赖性时，似乎消息应该依赖于它的存储，但是存储不需要知道任何关于数据形式的信息。我们可以通过提取围绕消息的编排来实现它，并将对存储的关注放在`FileStore.`上*

*我把原来的`FileStore`班分成了两个新的班；`FileStore`和`MessageStore.`*

*[在/FileStore.cs 之后](https://github.com/DannyRusnok/Single.Responsibility.Principle.Example/blob/master/Single.Responsibility.Principle.Example/After/FileStore.cs)*

*`MessageStore`协调消息、日志和存储过程。*

*[MessageStore.cs](https://github.com/DannyRusnok/Single.Responsibility.Principle.Example/blob/master/Single.Responsibility.Principle.Example/After/MessageStore.cs)*

*这一次，我坚持在名称中使用“store”这个词，因为它更精确。有时，我很难为负责编排的类选择正确的名称。所以我通常选择“处理器”这个词，并添加一个前缀来定义职责——例如，`MessageStoringProcessor.`这个名字听起来更准确。*

*我很想听听你是如何处理这个问题的。*

# *摘要*

*我们查看了`FileStore`类，并确定了它的三个职责。首先，我们提取了最简单的一个，即*日志*职责。因此，我们将剩余的关注点*存储消息*和*编排*分离到两个新的类中；`MessageStore`和`FileStore.`*

*存储库还包含带有单元测试的项目，您可以尝试运行这些单元测试，并查看是否有任何丢弃的行为或功能。绿色测试将证明我们做得恰到好处。*

*[](https://www.danielrusnok.com/daniel-rusnoks-newsletter) [## 丹尼尔·鲁斯诺克的时事通讯

### 每个月我都会给你发一封电子邮件，列出我的最新文章。这当然是友好的联系…

www.danielrusnok.com](https://www.danielrusnok.com/daniel-rusnoks-newsletter) [![](img/88f0e07ab6797fc4fcd13ed7410af039.png)](https://www.buymeacoffee.com/danielrusnok)[![](img/f3d94d9b86b4ee7080bab6d72172b50a.png)](https://itixo.com)*