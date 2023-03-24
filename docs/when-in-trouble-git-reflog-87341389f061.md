# 有困难的时候，Git Reflog！

> 原文：<https://levelup.gitconnected.com/when-in-trouble-git-reflog-87341389f061>

代码编码 GIF 由 EscuelaDevRock(GIPHY.com)制作

假设我们正在为一个软件项目做文档。我们初始化一个 git 存储库，并将名为`file.txt`的文档文件提交给它。

我们进一步向文档中添加了几行。

然后，我们向存储库提交新的变更。

`git log`的输出显示了这个存储库的历史:

让我们“意外地”删除最近的提交！

运行`git log`的新输出如下:

您会看到第二次提交已经消失。

这几乎是我们项目的一个致命错误。

我们写文档的所有努力都白费了。

**不尽然！** `**git reflog**` **来了！**

![](img/da8cb95bc1c0a750b17a51c4df0f825b.png)

罗曼·辛克维奇·🇺🇦在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 什么是“git reflog”？

`reflog`是一个 git 命令，输出“**参考日志**”。

该日志跟踪对`HEAD`所做的所有更改，即指向本地存储库中分支的最新提交的指针。

当我们在终端中使用`git reflog`命令时，我们会看到以下输出:

# 删除了怎么恢复？

要将更改恢复到最近一次提交的时间点(被删除的时间点)，我们使用:

```
$ git reset --hard 8cb16bb
```

这里，`8cb16bb`是我们想要恢复的提交的散列。

上述命令的输出是:

```
HEAD is now at 8cb16bb Added lines to the documentation
```

当我们再次使用`git log`时，我们到达了历史的同一点。

感谢 Git，我们的努力得到了很好的照顾！

![](img/134685026142a99963b7b8155aa10f39.png)

瑞兰德·迪恩在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

点击此处阅读更多关于`git reflog`的信息:

[](https://git-scm.com/docs/git-reflog) [## Git - git-reflog 文档

### 该命令采用各种子命令，以及取决于子命令的不同选项:git reflog [show] [ ] [ ]git…

git-scm.com](https://git-scm.com/docs/git-reflog) 

非常感谢你阅读这篇文章！

[](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium-Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)