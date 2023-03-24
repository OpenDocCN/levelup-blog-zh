# 更新依赖关系，同时保持 package.json 和 yarn.lock 同步

> 原文：<https://levelup.gitconnected.com/updating-dependencies-while-keeping-package-json-and-yarn-lock-in-sync-e98187357a06>

## 如何不发疯地进行更新

![](img/9cf422aa75cebcfcff70bea01f36a807.png)

[莱昂·温特](https://unsplash.com/@fempreneurstyledstock?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

JavaScript 生态系统充满了成千上万的 npm 包供您选择。当您构建项目时，很可能很快就会依赖于至少一些第三方依赖项。

Npm 包一直被他们的维护者更新。这些更新可能是错误修复、安全补丁、新功能和重大重写。

# 语义版本控制

为了帮助这些包的消费者理解每个新版本将如何影响他们的项目，包维护者通常使用所谓的[语义版本化](https://semver.org/)。

语义版本化看起来像`MAJOR.MINOR.PATCH`。例如，可以在`1.0.0`设置包版本。如果一个新版本只包含错误修复，那么这个版本将会被转移到`1.0.1`。如果一个新版本包含不破坏现有 API 的新特性，那么这个版本将会被升级到`1.1.0`。如果一个新的版本包含了突破性的改变，那么这个版本将会被转移到`2.0.0`中，这个改变是软件包的消费者需要知道的，并且需要调整他们使用这个软件包的方式。

# 存储项目的依赖项

![](img/15115cd35ff44ce6c70c72d3adf87aed.png)

Maksym Kaharlytskyi 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

您的依赖关系在您的`package.json`文件中指定。对于在您的`dependencies`或`devDependencies`对象中列出的每个包，您可以精确地指定您希望如何更新该包。

仅包含版本号意味着您只想使用确切的包版本。

在版本号前加一个波浪符号(`~`)意味着您只想在补丁更新可用时接受它们。

在版本号前加上一个插入符号(`^`)意味着您希望在有可用的小版本和补丁更新时接受它们。

如果您使用 Yarn 来管理您的包，那么安装在您的项目中的每个依赖项的精确版本都存储在您的`yarn.lock`文件中。`yarn.lock`文件的主要目的是确保每个将您的代码下载到他们机器上的人的每个包的版本保持一致。

# 更新项目的依赖项

![](img/2a11502b1dfcc3bdc9d7d14d4d2a620b.png)

照片由[尼克·费因斯](https://unsplash.com/@jannerboy62?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如上所述，npm 包更新非常频繁。这意味着，如果您想要保持您的项目使用最新的版本，您必须不断地更新您的依赖项。

我试着每周更新一次项目的依赖关系，这样我就不会落后太多。即使在那个时间段，我也经常会有 10 或 20 个包有新版本。

**现在，我们来看问题的症结**:当运行`yarn upgrade`来升级您的依赖项时，`yarn.lock`文件会更新为最新请求的包版本，但是`package.json`文件不会！

![](img/cb870674692ec6dcea79962315306c4d.png)

[Icons8 团队](https://unsplash.com/@icons8?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

例如，如果在您的`package.json`文件的`dependencies`对象中有一个包`"something-cool": "^2.0.3"`，并且有一个`2.4.0`的可用版本，并且您运行`yarn upgrade`，那么将为您的项目安装版本`2.4.0`，并且版本`2.4.0`将显示为您的`yarn.lock`文件中安装的版本。但是，您的`package.json`文件仍然会显示`"something-cool": "^2.0.3"`。

这是因为您已经指定您可以安装最新版本的软件包，只要它仍然是主版本`2`的一部分。该要求成立，因此即使`yarn.lock`发生变化并且安装了更高版本，也不会改变`package.json`。

对我来说，这有点违反直觉。当我将包从`2.0.3`更新到`2.4.0`时，我希望版本在`yarn.lock` *和* `package.json`中同时更新。

在谷歌上快速搜索了一下，似乎我并不孤单。许多其他开发人员也期待这种行为。

那么，有可能让这种行为发生吗？

是啊！

# 解决方案

![](img/18ed36334937b2adcad240c7bfeb33c5.png)

布鲁斯·马尔斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

到目前为止，我发现的最好的解决方案是使用下面的命令来更新我的包:`yarn upgrade-interactive --latest`。

我们来分析一下。

我们已经知道`yarn upgrade`是用来升级包版本的。

相反，运行`yarn upgrade-interactive`会让你进入一个命令行界面工具，允许你挑选想要升级的包。

传递`--latest`标志是更新`package.json`文件的关键。

现在，重要的是注意到`--latest`标志将您的包版本更新为最新版本，*不管您在`package.json`文件中为该包指定了什么规则。这意味着如果您指定了`"something-cool": "^2.0.3"`，并且有一个版本`3.1.0`可用，那么运行`yarn upgrade --latest`实际上会将那个包更新到版本`3.1.0`，尽管事实上默认情况下您只想进行小的更新和补丁更新。*

这就是为什么我使用`yarn upgrade-interactive`而不仅仅是`yarn upgrade`，这样我就可以选择我想要更新的包。选择的时候，我只选择那些有次要和补丁更新的包。

我更新所有这些，然后运行我的棉绒和测试，以确保我没有打破任何东西。

如果有主要的版本可以升级，我通常会一个接一个地单独处理。这样，如果出现任何问题，就很容易知道是哪个包装损坏了东西。

# 结论

我希望这个技巧能帮助你维护你的 JavaScript 项目和它们的依赖关系。Yarn 有一些关于他们的`[upgrade](https://classic.yarnpkg.com/en/docs/cli/upgrade/)` [命令](https://classic.yarnpkg.com/en/docs/cli/upgrade/)，和关于他们的`[upgrade-interactive](https://classic.yarnpkg.com/en/docs/cli/upgrade-interactive)` [命令](https://classic.yarnpkg.com/en/docs/cli/upgrade-interactive)的文档，但是我发现他们的文档在试图解决这个具体问题时有点混乱。

现在你也可以轻松地在`package.json`和`yarn.lock`中更新你的软件包，同时保持它们的同步。

编码快乐！