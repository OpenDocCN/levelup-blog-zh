# 如何在 WSL 2 上安装 Jekyll

> 原文：<https://levelup.gitconnected.com/how-to-install-jekyll-on-wsl-2-13c3b285d513>

## GitHub 使用的静态站点生成器

# 介绍

![](img/ffa9134cde97658ccf54d7da7f06cc5e.png)

*照片由* [*周杰伦 S*](https://unsplash.com/@jay_s28) *上 Unsplash*

## 目录

> 我[n 简介](#4656)
> [额外津贴](#06a4)
> [这一切只是为了安装 Ruby](#7ab0)
> [现在我们只需要安装 Jekyll](#86ec)
> [现在我该怎么办？](#c139)

*   在本教程中，您将配置 Windows 10 在 WSL 2(Linux 的 Windows 子系统)上使用 [Jekyll](https://jekyllrb.com/) SSG(静态站点生成器)。
*   如果你不想在虚拟机上安装 Linux 或者只是为了使用 Jekyll，那么好消息是你可以在 Windows 上使用 WSL！
*   为此，您必须在 WSL 中安装 Node.js 和 Ruby。
*   你应该烦恼吗？只有当你是博客新手，还没有使用适合你的内容管理系统。
*   正如我所说的，GitHub 使用它来帮助公司轻松地生成内容，而不必依赖于为每个段落创建 HTML p 标签。这有什么意思呢？
*   此外，在 WSL 2 的 Jekyll 官方网站上安装 Jekyll 的说明已经过时，这也是本文的原因。

Jekyll 是我用过的第一个静态站点生成器，Github 用它来创建自己的站点。我积累的知识已经被写进博客里，它帮助我转换成原始的 HTML，我不用担心。

在我写了几篇博客，甚至有一些被大型科技公司引用后，我明白了为什么 Github 会使用它。这比你手动创建每一个 HTML 段落标签要好 10 倍，我以前就是这么做的。

我正在使用的页面原本是一个减价页面，用 Jekyll 转换成了 HTML。我只是导出了大部分内容，并在 Medium 上做了一些调整。

我使用了 Ubuntu 20.04 (Focal)，因为 Ubuntu 22.04(海哲明)与 Brightbox 个人软件包档案或 ppa:brightbox/ruby-ng 不兼容，后者需要安装 Jekyll 运行的 ruby 语言。

# 额外津贴

您应该非常熟悉 HTML 和一些基本的 Linux 终端导航。您应该已经安装了 WSL 2。如果没有，你需要去 [Windows 文档](https://learn.microsoft.com/en-us/windows/wsl/install)了解如何操作。

**使用了下面的库，请暂时不要安装:**

```
jekyll 4.2.2
node v10.19.0
rbenv 1.2.0-16-gc4395e5
```

老实说，我只是试图安装最新版本。

# 这一切只是为了安装 Ruby

不幸的是，安装可能是设置 Jekyll 最困难的事情。其他的都是一点点练习。

控制台命令复制并粘贴到您的终端来安装 Ruby。哲基尔也装了。

功劳归于被称为[吉米的用户。奉劝，他用的 Node.js 版本过时了。](https://softans.com/question/error-while-executing-gem-gemfilepermissionerror-you-dont-have-write-permissions-for-the-var-lib-gems-2-7-0-directory/)所以我用的是我 Gitgist 里显示的 Node.js 最新版本。

# 现在我们只需要安装 Jekyll

Jekyll 现在应该安装好了。您可以使用命令`jekyll -v`检查这一点，它应该会返回一个版本号。否则你可能不得不重新安装一切。只是确保除非需要，否则不要清除任何包。如果必须清除，可以考虑提前备份 Windows。

如果你去了一个目录并做了`bundle init`，那么你会得到一个类似于这里的[的错误。](https://github.com/jekyll/jekyll/issues/8523)

要修复它，请使用此命令将其添加为。

```
bundle add webrick
```

你应该完全安装 Jekyll。它可能很难安装，但一旦运行，它非常容易使用。例如，Gatsby 团队甚至说静态站点生成器更环保，因为它们不使用数据库。任何人都可以开始写，写任何东西，甚至不一定是关于编程的。只是请客观地写博客，不要让它变成仇恨。

# 我现在该怎么办？

![](img/94b28d332da9d4e698b247472b119809.png)

照片由 [Jasmin Sessler](https://unsplash.com/ja/@jasmin_sessler?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 Unsplash 上拍摄

如果你没有一个旧的网站，那么完美，因为它将更容易使用 Jekyll，因为你只需要下载他们的主题了。GitHub Pages 允许你免费托管你的静态站点。

你可以考虑为你的网站申请一个域名。

我希望你和杰基尔玩得和我一样开心。如果你在工作中遇到困难，请随时联系我们。我买了一些东西。现在再见。

**资源:**

*   [捆绑包初始化错误](https://github.com/jekyll/jekyll/issues/8523)
*   [安装 Ruby 的原始命令](https://softans.com/question/error-while-executing-gem-gemfilepermissionerror-you-dont-have-write-permissions-for-the-var-lib-gems-2-7-0-directory/)
*   [安装 WSL 2](https://learn.microsoft.com/en-us/windows/wsl/install)
*   [Jekyll 官方网站](https://jekyllrb.com/)