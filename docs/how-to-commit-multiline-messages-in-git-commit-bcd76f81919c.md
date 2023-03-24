# 如何在 git 提交中提交多行消息

> 原文：<https://levelup.gitconnected.com/how-to-commit-multiline-messages-in-git-commit-bcd76f81919c>

## 有一些简单的方法可以做到这一点，这完全取决于你的个人喜好

![](img/2f2f4465e737e657f8e1cc1caaf1f2d5.png)

由 [Ksenia Makagonova](https://unsplash.com/@dearseymour?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

作为开发人员，当使用 Git 向远程存储库提交代码时，我们需要编写关于这一修改的信息。在命令行上，我们使用`git commit`命令，比如`git commit -m`允许你添加一行信息。但有时带有标题和具体描述的多行消息可能更能表明您的意图，例如:

```
Commit Title: Briefly describe what I changed
Commit Description: Detailed instructions for changing it
```

那么如何实现这个呢？

# 1.使用文本编辑器

使用不带`-m`或`git commit -v`的`git commit`，这将带你到一个文本编辑器。这样你就可以用你最喜欢的文本编辑器添加多行文本。

# 2.多个`-m`选项

如果你不想看到冗长的差异，你可以使用多个`-m`选项。就像这样:

```
$ git commit **-m** "Commit Title" **-m** "Commit Description"
```

这是因为如果给出了多个`-m`选项，它们的值将被连接成单独的段落，这可以在 [git 文档](https://git-scm.com/docs/git-commit#Documentation/git-commit.txt--mltmsggt)中找到。

接下来的`git log`会是这样的:

```
$ git log
commit 1e8ec2c4e820fbf8045b1c7af9f1f4f23262f755
Author: Your Name you@example.com
Date:   Sat Sep 24 20:18:15 2022 -0700Commit TitleCommit Description
```

# 3.打开引号，按回车

另一种更简单的方法是键入`git commit -m "`并点击`Enter`来输入多行，并在结束时使用右引号。这看起来像这样:

```
$ git commit -m "
> Commit Title
> Commit Description"
```

接下来的`git log`将会是这样的:

```
$ git log
commit 7d75a73e41b578a1e2130372a88a20ed1a0a81e4
Author: Your Name you@example.com
Date:   Sat Sep 24 20:22:02 2022 -0700Commit Title
    Commit Description
```

# 4.Shell 环境变量

不要忘记您可以在 shell 中定义环境变量，例如，您可以用换行符定义临时环境变量:

```
$ msg="
> Commit Title
> Commit Description"# or
$ msg="$(printf "Commit Title\nCommit Description")"
```

接下来，您可以:

```
$ git commit -m "$msg"
```

就这样，`git log`会输出:

```
$ git log
commit 056e35c37d199c0f3904e47d2107140267608c4a
Author: Your Name you@example.com
Date:   Sat Sep 24 20:42:11 2022 -0700Commit Title
    Commit Description
```

# 5.使用`-F`选项

来自[文档](https://git-scm.com/docs/git-commit#Documentation/git-commit.txt--Fltfilegt)的介绍:

> -F <file>—file =<file></file>
> 
> 从给定文件中获取提交消息。使用`-'从标准输入中读取消息。

因此您可以在提交之前在一个临时文件中编写一个多行消息。像下面这样:

```
$ printf "Commit Title\nCommit Description" > "temp.txt"
$ git commit -F "temp.txt"
```

或者使用标准输入代替临时文件:

```
$ printf "Commit Title\nCommit Description" | git commit -F-
```

# 结论

下面是我看到的几种方法，你可以根据自己的喜好选择其中一种，希望有帮助。

如果你有其他方法，欢迎分享。

*感谢阅读。如果你喜欢这样的故事，想支持我，请考虑成为* [*中等会员*](https://medium.com/@islizeqiang/membership) *。它每月收费 5 美元，可以无限制地访问媒体内容。如果你通过* [*我的链接*](https://medium.com/@islizeqiang/membership) *报名，我会得到一点佣金。*

你的支持对我来说非常重要——谢谢。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**将像你这样的开发人员安置在顶级创业公司和科技公司**](https://jobs.levelup.dev/talent/welcome?referral=true)