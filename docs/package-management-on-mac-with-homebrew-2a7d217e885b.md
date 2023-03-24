# 在装有自制软件的 Mac 上进行包管理

> 原文：<https://levelup.gitconnected.com/package-management-on-mac-with-homebrew-2a7d217e885b>

如果你是一个在 mac 设备上工作的开发人员，你很有可能使用过自制软件在你的机器上安装或更新应用程序和实用程序。因为在 mac OS 上没有默认的包管理器，所以 Homebrew 已经成为 mac 用户的首选解决方案，为那些习惯使用命令行的人提供了一种相对无缝且易于理解的方法。

如果你是一个 mac 用户，还没有安装家酿，这是非常容易做到的。您需要做的就是在命令行中输入以下内容。

```
/bin/bash -c "$(curl -fsSL [https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh](https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh))"
```

一旦你安装了家酿，你现在有一个简单的方法来执行一些任务。

**管理命令行实用程序:**

有了家酿，有数以千计的命令行实用程序，可访问和容易安装。有这么多实用程序可供选择，可能很难找到您可能需要的。幸运的是，在[家酿网站](https://formulae.brew.sh/formula/)上可以找到完整的清单。一旦您找到想要安装的实用程序，您所需要做的就是在命令行中输入它，前缀为 brew install。

```
brew install [whatever utility you want]
```

**用自制软件管理您的应用:**

家酿还能让你使用近 5000 种应用程序。使用子命令“cask”，您可以安装目录层次比单个二进制文件更复杂的应用程序(如简单的实用程序)。就像实用程序一样，所有可用的应用程序都列在家酿的网站上。要安装其中一个应用程序，您只需在命令行中输入以下内容。

```
brew cask install [whatever application you want] 
```

**用自制软件更新软件:**

自制软件的一个最好的特点是，它使你的所有软件保持最新变得非常简单。如果您想要避免错误并确保您始终拥有最新的功能，将软件更新到最新版本是非常重要的。要轻松更新所有使用家酿软件安装的软件，只需在命令行中输入以下内容。

```
brew upgrade
```

或者，如果您只想查看有哪些可用的升级，而不想自动安装它们，您可以运行:

```
brew upgrade -n
```

**结论:**

对于 mac 用户来说，Homebrew 是一个很好的软件包管理资源。它允许有效地安装实用程序和应用程序，并使您的软件保持最新的无痛过程。如果你是 mac 用户，我强烈推荐使用家酿作为你的首选软件包管理器！

P.S. Homebrew 一直在寻找贡献者来帮助扩展和改进它的功能。如果你对自制感兴趣，请访问 GitHub 库[看看他们需要什么帮助。](https://github.com/Homebrew/brew)