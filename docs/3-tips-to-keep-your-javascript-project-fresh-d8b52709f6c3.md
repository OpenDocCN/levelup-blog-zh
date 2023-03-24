# 保持 JavaScript 项目新鲜的 3 个技巧

> 原文：<https://levelup.gitconnected.com/3-tips-to-keep-your-javascript-project-fresh-d8b52709f6c3>

![](img/95d9f090cfedbf383bd45914f6f54a81.png)

照片由[布里吉塔·施奈特](https://unsplash.com/@brisch27?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

在这个快速发展的 JavaScript 世界中，保持项目的新鲜感非常重要。

我说的“新鲜的 T7”是指:

*   所有依赖项都更新到最新版本。
*   NPM 自动化包装出版流程。
*   保持高质量的代码。

这些点确实很重要，但对于开发人员来说，每次关注这些点都很耗时。

只要使用正确的工具和方法，所有这些点都可以**自动化**，在这篇文章中，我将教你如何轻松实现它们。

# **1。所有依赖项都更新到最新版本。**

为了达到这一点，我们将使用一个名为 [**的非常好的工具来修复**](https://github.com/renovatebot/renovate) **。**简而言之，这是一个每当有新版本时都会更新你的依赖项的工具。

要开始使用这个工具，你只需要从 [GitHub marketplace](https://github.com/apps/renovate) 安装它，并在`.github`文件夹中创建一个`renovate.json`文件。

一个基本的 renovate.json 配置。

配置完成后，Renovate 将在您的存储库中打开一些 PRs，其中包含更新的依赖项。此外，如果更新通过测试并满足您的自动合并规则，rules 可以在没有人工干预的情况下合并一些更新。

# 2.**自动化的包裹出版流程传到 NPM。**

为此，我们需要一个 NPM 令牌和一个 GitHub 动作。

NPM 令牌可以从您的 NPM 个人资料的设置部分生成。

生成令牌后，我们需要复制它并将其添加到 GitHub 机密页面中的**库机密**中，该页面可以通过单击 GitHub 库的设置选项卡来访问。

最后一步是编写一个 GitHub `publish.yml`动作，当你每次合并东西到你的主分支时，它可以发布你的包到 NPM。

publish.yml

一件很酷的事情是，这个 GitHub 动作会自动将你的包版本更新到一个更大的版本，并将这些更改提交给你的 repo。

# 3.保持高质量的代码。

有很多工具可以实现这一点，比如 ESlint 和 XO，但我们的主要任务是自动化这一过程。

我们将再次使用 GitHub 动作来自动化这个过程，因此让我们为此在`.github`文件夹中创建一个`check.yml`文件。

check.yml

正如你所看到的，每次打开一个新的 PR 时，这个动作都会运行`lint and test`命令，所以现在你可以放心了，因为未测试的代码将会更新合并到你的主分支中。

感谢阅读。我希望这有所帮助。