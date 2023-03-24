# 用“git 平分”追踪讨厌的 bug

> 原文：<https://levelup.gitconnected.com/track-down-pesky-bugs-with-git-bisect-71a34754a565>

![](img/8963c57587656cbaf2bae2f55ae0459f.png)

Krzysztof Niewolny 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

最近，我遇到了几个难以追踪的开发问题。

通常，修复 JavaScript 中的错误就像重现问题、查看错误和堆栈跟踪、找到适当的源文件并修复代码一样简单。

但是如果错误不管出于什么原因都没有帮助呢？或者没有错误怎么办？也许从技术上来说没有什么是“坏”的，但是你的应用程序中发生的行为完全不是你所期望的。或者错误可能来自您最近升级的第三方依赖项。

无论是哪种情况，如果您不容易找到源代码中错误的来源，git 有一个非常简洁的命令可以帮助您。

# Git 平分

`git bisect`是一个命令，它使用二分搜索法来标识引入问题的提交。使用它真的很简单。

要开始这个过程，您需要输入`git bisect start`。

接下来，您需要识别两个提交，一个存在问题，另一个不存在问题。

所以，也许你现在正在你的`master`分支上下载最新的代码，你看到了这个错误。所以您可以告诉 git 当前的提交是错误的。为此，您输入`git bisect bad`。

现在，您需要找到一个不存在 bug 的提交。为此，您可以通过输入`git log --oneline`来查看您的回购协议的最近提交。这将为您提供一个过去提交的列表，从您当前正在进行的提交向后进行，每个提交都在它自己的行上。

使用您自己的判断来选择您认为可能没有错误的提交。如果您不确定，可以检查一个提交，编译您的代码，查找错误，然后如果您还没有找到一个好的提交，就用不同的提交再试一次。

一旦找到一个好的提交，就可以通过输入`git bisect good <good commit hash here>`来告诉 git。

现在，git 开始了它的二分搜索法。它实际上是在好的和坏的提交之间抓住中间的提交并检查它。既然提交已经签出，您就可以编译代码并进行测试，看看问题是否存在。

如果这个提交存在问题，您可以用`git bisect bad`告诉 git。如果这个提交不存在问题，那么用`git bisect good`告诉 git。

收到这个反馈后，git 会在这个提交过程中检查另一个提交，以及好/坏的开始点，这取决于您给它的反馈。

同样，您编译您的代码，测试它，并让 git 知道您是否看到了使用`git bisect bad`或`git bisect good`的问题。

这个过程重复进行，并且由于二分搜索法的存在，它以最少的步骤以最有效的方式进行。

一旦你完成了，你将会发现是什么导致了这个问题。为了告诉 git 你已经完成了对分，你输入`git bisect reset`。

此时，您可以在 GitHub(或您正在使用的任何东西)中检查 pull 请求，通读代码，并更好地了解问题到底出在哪里。

# 结论

![](img/eeea7ff1c8260cea53a5df11a250196b.png)

照片由[安德烈·亨特](https://unsplash.com/@dre0316?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

`git bisect`在这些情况下，它成了我的救命稻草。通过使用它，我已经能够跟踪违规的提交，更好地了解问题的根本原因，然后开始修复问题。

谢谢饭桶！

你可以在这里阅读 git 的完整文档。