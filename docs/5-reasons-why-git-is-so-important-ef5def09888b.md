# Git 如此重要的 4 个原因

> 原文：<https://levelup.gitconnected.com/5-reasons-why-git-is-so-important-ef5def09888b>

![](img/d6aba2bfb59e34ce19e9693e19113dab.png)

布鲁克·卡吉尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

假设你是一个五人团队，正在开发一个医院管理系统。因为它可能需要 3-4 个月的开发，所以您希望确保开发过程顺利进行。

你首先要确保你的队友之间的适当合作，这样你就不会在同一件事情上结束工作。此外，如果你们中的一个人做了一个导致整个应用程序崩溃的更改，该怎么办呢？如果你能撤销那个改变，不是会有帮助吗？

这就是为什么需要一个[版本控制系统](https://www.geeksforgeeks.org/version-control-systems/) (VCS)。有许多 VCS，Git 是其中之一。

在这篇文章中，我将解释 Git 如此重要的 5 个原因。

# 分布式开发

![](img/c1026c8c9cdab4a1a9227d80a3417e35.png)

[女同胞](https://unsplash.com/@cowomen?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

有两种类型的存储库，远程(托管)存储库和本地存储库。当多个开发人员在一个项目上工作时，每个人在他们的系统上都有一个存储库的本地副本，并且他们将他们的更改推送到一个单独的远程存储库。

这样，多个开发人员可以在同一个特性的不同部分上工作。在一个医院系统中，假设您正在设计一些注册表单，那么一个开发人员可以处理样式，同时另一个开发人员可以处理功能。

这样，一个开发者就不必等到另一个完成了才开始工作。当推送到远程存储库时，开发人员首先必须[合并](https://www.javatpoint.com/git-merge-and-merge-conflict)对远程存储库所做的任何更改。这确保了不会覆盖任何内容。

# 功能分支协作

![](img/3a02e9f7eba40093a73a4fef4774d11a.png)

照片由 [krakenimages](https://unsplash.com/@krakenimages?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Git 的一个重要部分是能够创建项目的不同分支。当一个存储库被创建时，Git 有一个分支，即包含初始项目结构的主分支。添加新特性时，最佳实践是为它创建一个单独的分支。

假设，在我们的应用程序中，您正在添加一个显示所有患者病历的特性，您可以为它创建一个单独的分支，并将您的更改推送到该分支。这不影响主枝。

一旦您完成了这个特性，您就可以提出一个 pull 请求(GitLab 中的 merge 请求),通知团队并打开它进行审查和讨论。在请求被接受后，该特性与主分支合并，主分支通常是被部署的分支。

如果需要修改特征，这也很有用。假设用户不喜欢页面的外观。您可以创建一个单独的分支，在不影响现有应用程序的情况下改进页面。对于 bug 修复也是如此。

# 跟踪您的更改，并在必要时恢复

![](img/75f676645030e611d9693d49f4d160a4.png)

由[马库斯·温克勒](https://unsplash.com/@markuswinkler?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

当您提交和推送您的更改时，它们会保存在远程存储库中。您可以查看参与该项目的所有开发人员的提交历史。它们代表项目的不同版本。这有助于您跟踪项目的进展。提交消息指定在特定提交中做了什么。

如果您不喜欢最近的提交，您可以随时恢复到以前的提交。每个提交都有一个唯一的哈希代码，可用于恢复到该提交。

假设一个新的提交搞乱了应用程序的主要部分。也许，这是一些新的代码，在一个功能上工作得很好，但在其他功能上却搞砸了。通过恢复到先前的提交，您可以回到项目的先前状态，以便尝试不同的方法。

# 开源贡献

GitHub 上有很多开源库，任何人都可以在他们的项目中使用。现在，如果您想要向这个库中添加一个新的特性或者进行错误修复，您可以使用特性分支和拉请求来完成。这里的是你如何在 GitHub 上做开源贡献。开源项目也是如此。

开源贡献有助于开发您的编码和开发技能，并从社区获得有价值的反馈。此外，最重要的是，它使你能够回馈社会。

# 如何入门？

![](img/ad3625649c486d01b70a941da74cedff.png)

约翰·施诺布里奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我们已经看到了 Git 如此重要的四个原因，但是如何开始使用它呢？访问[这里](https://product.hubspot.com/blog/git-and-github-tutorial-for-beginners)开始使用 Git。一旦完成安装并学习了基本命令，就可以开始向项目中添加 Git 了。

与其他开发人员一起熟悉协作。一开始可能看起来很复杂，但是相信我，随着时间的推移，它会变得越来越简单和方便。

请点击查看我的 GitHub 简介[。](https://github.com/KunalN25)

# 结论

Git 是一个非常强大的协作工具，它提供了很多特性来使开发更快更流畅。使用分布式开发，每个开发人员可以同时处理一个特性的不同部分。特性分支有助于团队在不影响主项目的情况下专注于一个特性。

您可以通过跟踪提交来度量项目的进度，并在必要时返回。最后，开源贡献提供了一个对社区做出贡献并获得有价值反馈的好方法。

如果您无法理解内容或对解释不满意，请在下面评论您的想法。新想法总是受欢迎的！如果你喜欢这篇文章，请鼓掌。**订阅**和**关注**我获取每周内容。另外，不要忘记看看我的其他[帖子](https://medium.com/@kunal.nalawade25)。到那时，再见！