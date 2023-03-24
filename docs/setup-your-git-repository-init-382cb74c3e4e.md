# 初始化设置您的 Git 存储库

> 原文：<https://levelup.gitconnected.com/setup-your-git-repository-init-382cb74c3e4e>

![](img/df69fd09150d11db5c476c037c2be6ad.png)

照片由 [Jason Ng](https://unsplash.com/@jason_ng?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](/s/photos/budding-sprout?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

这篇文章是我的另一篇关于我几乎每天都使用的 [Git 命令列表的文章的延续](https://deddytandean.medium.com/git-commands-that-i-use-almost-on-daily-basis-33fbef3c5f62)。在本文中，我将深入探讨您在刚开始新项目时会经常遇到的一个 Git 命令。

提示其实已经在题目上了，就是`git init`。如果你读过我的另一篇关于 list 的文章，你会对这个命令有一个大概的了解。然而，如果你还没有，也许你可以从更广阔的视角去阅读它。如果你刚刚开始使用 Git，但你对它一无所知，因为你急于直接投入到你的新项目中，并且听说了很多关于 Git 的事情，那么你仍然在正确的地方。保持冷静，继续读下去。

# Git 初始化

Git 存储库是项目的存储位置，可以用 Git 命令运行。您可以使用这些命令做很多事情。然而，为了启动存储库的创建，您需要使用`git init`命令。此命令也可用于重新初始化现有的。此外，您还可以初始化现有的项目目录。

基本上，`git init`命令将您的目录转换成 Git 存储库。你怎么知道它什么时候被(重新)初始化？您将看到`.git`子目录已创建。它还会自动创建一个名为“主”分支的新分支。要检查分行名称，只需运行`git branch`或阅读我的其他文章。:D

`.git`子目录包含新存储库所需的所有 Git 元数据。此元数据包括对象、引用和模板文件的子目录。还会创建一个头文件，它指向当前签出的提交。

不要不知所措，尽管有上述所有理论，这个命令使用起来很简单。以下是步骤:

1.  创建您的文件夹(如果您还没有任何想要的项目目录)。
2.  导航到它(如果您还没有这样做)。
3.  初始化您的文件夹。

瞧啊。完成了。现在您有了本地存储库。好吧，这是个例子:

```
$ mkdir myNewProject
$ cd myNewProject/
$ git init
```

然后，找到你的`.git`目录！非常简单。

否则，您也可以使用该命令自动创建一个目录，并随后初始化:
`$ git init [NEW_DIRECTORY]`

示例:

`$ git init NewProject`

一个新的“新项目”目录将被创建在你当前的工作目录下，然后一个`.git`子目录将被创建在其中。

从现在开始，你可以运行你的终端，输入 Git 命令，它们就会像那样神奇地工作。但是，有一点需要注意，您必须使用初始化的 Git 导航到目录中。如果您在文件夹之外并试图运行 Git 命令，它将不起作用。因此，在运行所有 Git 命令之前，一定要确保您在初始化的存储库中。

附言:这个命令有标志或选项，如`-q`或`--quiet`或`--bare`。您可以在 Git 文档中读到更多关于它们的内容，或者您可以运行`help`命令(我在我的另一篇文章中也简单提到过；)).但是，要入门，`git init`本身就够了。

如果你真的想改变你的默认分支名称(默认为“master”)，你可以使用这个标志:
`--initial-branch=<branch-name>`

示例:

对于正常人:
`$ git init --initial-branch=trunk`

这将创建您的初始默认分支作为“主干”而不是“主”。
它还有一个简称:
`$ git init -b trunk`

如果您厌倦了“主”默认分支，只想每次都默认设置其他名称，您可以设置它的`init.defaultBranch`配置来进行设置:

`$ git config --global init.defaultBranch trunk`

这样，您所有的新回购都将使用初始默认分支“主干”而不是“主”来创建。通常人们只需要“主人”就可以了。谁知道你不是。

# 结论

对于这个命令，每个人在开始使用任何 Git 命令时都会直接遇到它。原因是在使用其他命令之前，需要先初始化一个目录！然而，这个命令非常简单明了。所以，不用担心。

本文到此为止。我可能会写更多关于 Git 命令的内容，因为我理解当一个新手刚刚入门，不知道从哪里开始时的感受，我想帮助你们轻松地使用 Git 命令，这样你们就不会感到不知所措。

所以，如果你还有什么要补充的，或者你发现我遗漏了什么，欢迎在下面评论。如果你觉得这篇文章可以帮助其他人学习，也可以分享给他们学习！

感谢阅读，继续学习~