# 在 Windows 10 上配置 Node.js 开发环境

> 原文：<https://levelup.gitconnected.com/configure-a-node-js-development-environment-on-windows-10-2e8fa1e1ecbc>

![](img/6c688683afe6ad2bcffbe7a67459c895.png)

> 在学习 Node.js 的路上不要在树林中迷路，在 Windows 10 上开始编写服务器端 JavaScript 所需的一切都在这里。

# 概观

如果你想学习编程，入门可能是一个令人困惑的障碍。在本教程中，我提供了一种简单的方法来安装在 Windows 10 上开始使用 Node.js 所需的所有工具。

**注意**:当一个简单的命令应该产生某种类型的输出时，你会看到`command` → `output`。

# 安装 Chocolatey:Windows 软件包管理器

[Chocolatey](https://chocolatey.org/) 允许我们从网上下载并安装开源软件包。Chocolatey 和用户的实践一样安全。它通过为您的开发机器下载已知的安全包来降低风险。

我们将使用 [Windows PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/getting-started/getting-started-with-windows-powershell?view=powershell-7) 来安装 Chocolatey。

要启动 PowerShell，请在 Windows 开始菜单中输入`PowerShell`,并从菜单中选择“以管理员身份运行”。

在 PowerShell 终端中，输入命令:

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

预期输出如下:

# 安装节点版本管理器(NVM)

现在我们可以使用我们的 Chocolatey 软件包管理器下载并安装 [NVM](https://github.com/nvm-sh/nvm/blob/master/README.md) ！处理需要不同版本 Node.js 的项目是很常见的。如果我们直接安装 Node.js，在这样的项目之间切换会带来一个难题。但是 NVM 将使这个问题变得容易解决。

这一次，使用 Windows 终端。在 Windows 开始菜单中键入“cmd ”,然后选择“以管理员身份运行”。

要安装 NVM，请输入命令:

```
choco install -y nvm
```

预期输出如下:

关闭终端窗口并打开一个新窗口(再次以管理员身份)。这允许终端查找环境信息并查看我们新安装的包管理器。

尝试命令`nvm version` → `1.1.7`。如果你得到一个数字版本的结果，我们很好！

接下来我们安装 Node.js。

# 安装 Node.js

现在我们可以用 NVM 下载安装 [Node.js](https://nodejs.org/en/about/) ！再次打开 Windows 终端(您应该不需要管理员权限)。输入命令:

```
nvm install node@13.12.0
```

预期输出如下:

在庆祝之前，让我们用命令`nvm use 13.12.0`设置我们的默认 Node.js 版本。

验证您的安装:

您已经准备好编写测试 Node.js 的代码了！

# 你好世界！

在您的 Windows 终端中，输入`node`。这将启动节点 REPL 终端。

在终端中输入命令:

```
console.log("Hello World!")
```

您的“Hello World”声明将被打印到终端上

然后，通过输入`.exit`退出 REPL。

# 添加更多版本的 Node.js(可选)

正如我前面提到的，参与需要不同版本 Node.js 的多个 Node.js 项目是很常见的。例如，您可以选择在 Node.js 版本 **13.12.0** 中编写自己的项目，但需要帮助另一个使用 Node.js **12.16.2** 开发项目的开发人员。这将帮助你解决那个问题。

要添加节点的另一个版本，请打开 Windows 终端:

```
nvm install node@12.16.2 nvm use 12.16.2
```

验证您的最新安装:

要切换回版本 13，在这之后，我们将再次使用命令:`nvm use 13.12.0`。

任何时候，你都可以用`nvm ls`看到你本地安装的 Node.js 版本。

# 安装 Git

在大多数情况下，您将编写需要版本控制的代码。为此，没有什么比 Git 更好的了。在我们结束之前让我们做那件事。这很容易。我保证。

[从 Git 网站下载最新的安装程序](https://git-scm.com/download/win)(下载应该会自动开始)。

下载完成后，使用默认设置进行安装。不要大惊小怪。我们可以稍后配置 Git。你会在一瞬间完成。

安装完成后。打开新的 Windows 终端，输入命令`git --version` → `git version 2.26.0.windows.1`。如果您看到所描述的版本，那么 Git 配置正确。

# 安装 Visual Studio 代码

Visual Studio 代码是我首选的 Node.js 开发环境，关于[入门](https://code.visualstudio.com/docs/introvideos/basics)有很多优秀的指南。

简而言之，下载[Visual Studio Code](https://code.visualstudio.com/)for windows 的最新二进制。

下载完成后，使用默认设置进行安装。不要大惊小怪。

最后一步，访问 [Visual Studio 代码市场](https://marketplace.visualstudio.com/VSCode)，并安装:

当这两个都安装好后，重新启动 Visual Studio 代码，继续探索，了解一下您可以使用的工具。

# 你完了！

您已经配置了 NVM 来管理 Node.js 的两个版本。最重要的是，您添加并配置了自己的代码编辑器。恭喜你！

准备好迎接新的挑战了吗？[从这里开始用 Node.js](https://www.harveyramer.com/blog/2020-04-09-start-here-to-make-a-useful-covid-19-tracker-with-node-js/) 做一个有用的新冠肺炎跟踪器。

如果您有任何问题或反馈，请[联系我](https://www.harveyramer.com/contact)。

特色图片鸣谢:[多纳尔·赖斯科弗](https://commons.wikimedia.org/wiki/File:Brussels_Zonienwoud.jpg) / [CC BY-SA](http://creativecommons.org/licenses/by-sa/3.0/)

*原载于 2020 年 4 月 8 日 https://www.harveyramer.com**的* [*。*](https://www.harveyramer.com/blog/2020-04-08-configure-a-nodejs-development-environment-on-windows-10/)