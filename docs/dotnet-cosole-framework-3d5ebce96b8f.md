# 我为什么以及如何创建另一个控制台框架

> 原文：<https://levelup.gitconnected.com/dotnet-cosole-framework-3d5ebce96b8f>

## 有时候，为了学习，我们需要找借口开始一个新项目

控制台应用程序正在成为开发者的病毒工具。在将每一个非可视化工具转移到 GUI 以获得更好的控制的趋势之后，最近几年，我们看到了 CLI 的成功。提醒一下 Docker 和 Kubernetes。在过去的几个月里，我开发了一个名为 [RawCMS](https://github.com/arduosoft/RawCMS/) 的开源无头 CMS，并在上面放了一个 CLI 应用程序。我通过使用。Net market，最后体验不怎么样。我完成了任务，但是下一次我必须开发客户端应用程序时，我决定投入一些时间尝试做得更好。我开发了一个简单的控制台框架，可以自动执行命令并与用户交互。

在这篇文章中，我将解释我是如何做到的——集中在我学到的令人兴奋的部分。像往常一样，代码可以在 GitHub 上获得，我发布了一个 Nuget 包用于测试(文章末尾有链接)。

![](img/a0d2fbf1a3a150299867a020a0b26ca4.png)

由[哈尔·盖特伍德](https://unsplash.com/@halgatewood?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 为什么是另一个控制台框架

诚实的回答是因为我需要找一个借口来测试 Github 的行为。我可以为此做一个虚拟项目，但我不喜欢太多的假东西。这就是为什么我接受了我最后一次可怜的经历。net RawCMS CLI 可以尝试做一些有用的东西。现在我会试着找一个理性的理由。

我到目前为止使用的框架 Commandlineparser 工作正常，并且结构良好。它有一个简单的方法:

这导致代码组织如下:

*   您为映射输入每个命令创建一个类
*   在这个类中，您将在每个将从命令行输入解析的字段上定义注释
*   您将定义一段接收类输入的代码

我发现这种方法结构非常好，对模型和业务逻辑有很好的定义。无论如何，对于一个简单的项目，要求你产生许多类，保持它们同步，这对于非常简单的程序来说是一堆时间。此外，它不支持在交互模式下工作或自动化脚本。

为了克服这些限制，我决定使用一种不同的方法:

1.  参数和命令定义将从方法注释中自动导出
2.  我将实现一个架构，允许用户直接调用一个命令，或者简单地定义一个脚本并运行它，利用其中的命令

如果我试图解释的不清楚，也许我们需要一个例子。

你可以像这样使用它:

```
> MyProgram.exec CommandName --arg value --arg2 value
> MyProgram.exec Exec MySettings.yaml # (settings from file input!)
> MyProgram.execWelcome to MyProgram!
This is the commands available, digit the number to get more info
0 - info: Display this text
1 - MyMethod: Execute a test method
```

我希望现在已经有点清楚了。

# 它是如何工作的

这个工具非常简单。它可以从上到下分解成几个子部分:

1.  流畅的注册课程
2.  程序定义(要执行的命令序列)
3.  命令实现
4.  基本执行

## 流畅的注册课程

该类旨在提供一种集成到控制台应用程序中的简单方法。你需要这样的东西:

## 程序定义(要执行的命令序列)

控制台解析以表示程序定义的类的水合结束。该类包含要执行的命令列表和它们需要工作的参数。所有将在运行时执行的东西都来自这些命令。例如，欢迎消息是每次执行的命令，或者有一个显示所有可用选项的“info”命令。

这是程序定义:

它是从 YAML 脚本文件或通过命令行调用加载的。

## 命令实现

命令实现表示可以调用的命令。我创建了两个注释，一个用于将常规方法提升为命令，另一个用于描述参数。

用法大概是这样的:

ConsoleAuto 扫描所有程序集以查找所有命令，并将其添加到内部可用命令集中。

## 基本执行

当用户要求执行命令时，该类由。net core DI 和使用反射执行的方法。

# DevOps 设置

在这个有趣的库中呆了几个小时后，我终于可以开始测试 GitHub 的动作了。

我想要建立的是一个简单的流程:

1.  构建代码
2.  测试代码
3.  将代码打包成一个 Nuget 包
4.  将包推送到 NuGet

git hub 的设置很简单，您只需要在。github/workflows 文件夹。

我用的文件是这个。

请注意基于渐进式版本号的自动版本控制。

这些步骤是:

1.  修补版本
2.  建设
3.  试验
4.  发布到 NuGet

替换中的版本可以很容易地完成路径。csproj 文件。这是第一步，以便所有后续编译都使用正确的内部版本号。

```
sed -i "s/1.0.0/1.0.$GITHUB_RUN_NUMBER.0/g" ConsoleAuto/ConsoleAuto.csproj
```

该脚本假设在 csproj 中总是有版本标签 1.0.0。您可以使用 regexp 来改进这个解决方案。

build\test 命令非常标准，相当于

```
dotnet restore
dotnet build --configuration Release --no-restore
dotnet test --no-restore --verbosity normal
```

最后的发布步骤是一个第三方插件，它在内部运行 pack 命令和 push 命令。如果你看看 GitHub 的历史，你会发现类似的东西:

```
dotnet pack  --no-build -c Release ConsoleAuto/ConsoleAuto.csproj 
dotnet nuget push *.nupkg 
```

# 我从这次经历中学到了什么

在我们的领域里，我们所需要的一切都已经被发现或完成了。完成一项任务的最快方法是用你找到的东西去做。永远不要重新发明轮子。无论如何，当你需要学习新的东西时，你可能会受到鼓励，尝试创造一些有用的东西。这样可以让你的学习更实际，让你和现实问题产生冲突。

在这个图书馆度过了一个星期六之后，我学到了:

*   已经有一个库可以自动将表单方法映射到命令行参数。这个库做得非常好，它还有一个 web 界面，可以使用 swagger UI 可视化地运行命令。
*   GitHub 的动作非常酷，但是问题出在第三方插件上，这些插件没有很好的文档记录，也不能完全正常工作。给他们一些时间让他们成熟。但是对于这个话题，我需要另一篇文章😃

# 参考

*   [GitHub 上的 ConsoleAuto 项目](https://github.com/zeppaman/ConsoleAuto)
*   [RAWCMS](https://github.com/arduosoft/RawCMS) —上面提到的无头 CMS
*   文中提到的库有 [Commandlineparser](https://github.com/commandlineparser/commandline) 和 [ConsoleAppFramework](https://github.com/Cysharp/ConsoleAppFramework) ，我建议测试一下作为首选。