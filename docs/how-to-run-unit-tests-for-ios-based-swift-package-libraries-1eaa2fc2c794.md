# 如何对基于 iOS 的 Swift 包库运行单元测试

> 原文：<https://levelup.gitconnected.com/how-to-run-unit-tests-for-ios-based-swift-package-libraries-1eaa2fc2c794>

用于 iOS 的 Swift 软件包库有时无法使用标准的 swift 测试运行程序运行。

![](img/5db95e90d997c765c67bfc2b7d11c703.png)

*原发布于*[*https://fek . io*](https://fek.io/blog/how-to-run-unit-tests-for-i-os-based-swift-package-libraries/)*。*

我最近在尝试为基于 iOS 的 Swift 包设置自动化单元测试时遇到了一个问题。为 iOS 项目创建包或代码库有几种不同的方法。Swift Package Manager 已经成为创建可重用模块的一种比较流行的方法。

Swift Package Manager(简称 SPM)提供了一种创作和分发这些模块的绝佳方式。SPM 还被设计成跨平台的，也就是说 swift 包可以在 macOS、tvOS、watchOS 和 iOS 之间共享。Swift 命令行工具有一个内置的测试运行器，您可以使用它来执行您的单元测试。但是，如果您使用的依赖项只是 iOS，会发生什么呢？换句话说，他们不能使用内置的测试运行程序在 Mac 上运行。

# 纳入浪子

[浪子](https://docs.fastlane.tools/)是一款自动化工具，面向希望简化构建、测试、自动截图和项目构建的 Android 和 iOS 开发者。在这篇文章中，我将结合使用浪子和 SPM 来创建一种在 iOS 平台上运行单元测试的方法。

# 安装浪子

浪子要求您使用装有 Ruby 2.5 或更高版本的 Mac。一旦你确认你有足够新的 Ruby 版本，你就可以安装浪子了。

*   运行`gem install bundler`安装捆扎机。
*   在项目的根目录下创建一个./gem 文件，内容如下。

```
source "https://rubygems.org" gem "fastlane"
```

您也可以通过运行以下 brew 命令，使用 Homebrew 安装浪子:

```
$ brew install fastlane
```

# 将 fastlane 添加到您的 Swift 套餐

从终端中，导航到软件包的根文件夹，然后键入以下命令:

```
$ fastlane init
```

选择手动设置，这应该是第四个选项。这将在您的包文件夹中创建一个`fastlane`文件夹。在这个文件夹中，你应该会看到一个`Appfile`和一个`Fastfile`。

# Swift 包和 Xcode

Xcode 不再需要一个`xcodeproj`项目文件夹来打开包。您可以通过在终端中键入项目根目录下的`xed .`或直接在 Xcode 中打开您的文件夹来打开您的项目。为了在浪子运行测试，我们需要一个`xcodeproj`文件夹。下面是 SPM 命令，您可以使用它来生成一个`xcodeproj`文件夹。

```
swift package generate-xcodeproj
```

您应该会在终端中看到一条消息，内容如下:

```
warning: Xcode can open and build Swift Packages directly. 'generate-xcodeproj' is no longer needed and will be deprecated soon. generated: ./YourPackageName.xcodeproj
```

# 配置快车道

编辑`Fastfile`进行测试。我们将创建一个名为`tests`的通道。默认情况下，`Fastfile`看起来像下面的例子。

更改文件，使其看起来像下面的示例:

如果您想运行浪子`lane`进行测试，可以通过键入`fastlane ios tests`来完成。我们当前的 Fastfile 只输出文本`running our tests!`。我们将更改`Fastfile`,使其执行以下三个动作。我们将告诉 fastlane 为我们的 swift 包创建一个新的`xcodeproj`文件夹，然后运行我们的单元测试，最后删除我们刚刚创建的`xcodeproj`文件夹。

编辑您的`Fastfile`，使其看起来像下面的例子:

这个测试通道的第一部分使用`spm`动作来生成运行测试所需的`xcodeproj`文件夹。下一个动作是`run_tests`动作，它将编译我们的包并运行任何测试。最后一部分使用我们用于运行 shell 脚本命令的`sh`动作来删除我们的`xcodeproj`文件夹，因为除了运行浪子单元测试之外不需要它。

现在，当您运行`tests`通道时，您应该得到如下所示的输出:

浪子还将在以下路径下生成三个报告文件:

```
/fastlane/report.xml 
/fastlane/test_output/report.html 
/fastlane/test_output/report.junit
```

# 结论

我不知道过去几年我是如何在不使用浪子的情况下开发移动应用的。浪子集成了许多构建、测试和部署应用程序的基本工具。也可以扩展到自己写插件。

浪子是在 Ruby 的基础上构建的，所以在和浪子一起工作时，拥有一些 Ruby 的经验会有所帮助，但这不是必须的。如果你想写浪子插件，了解一点 Ruby 可能会派上用场。