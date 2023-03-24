# 如何用截图测试来测试 Android 中的 UI

> 原文：<https://levelup.gitconnected.com/testing-ui-in-android-with-screenshot-testing-7cc633836aad>

## 什么是截图测试，如何在你的项目中使用它

![](img/61f573095717b0ae4ab73c87902ef98c.png)

戴维·特拉维斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果你有一些在生产环境中开发 Android 应用的经验，你应该已经知道测试你的应用的重要性。在测试的时候，我们可以做很多不同的测试，单元测试，集成测试……但是对我来说，让我对代码库更有安全感的是 UI 测试。

传统上，在 Android 中，UI 测试是用 Espresso 完成的，并且需要运行物理或虚拟设备，所以正如你可以想象的那样，我们正在谈论的测试是繁重的，因此，即使你没有庞大的测试覆盖范围，也需要花费很多时间来运行。

几个月前，我发现了截图测试，这基本上是在你认为有效的特定状态下对你的应用程序进行截图，然后将其与测试运行时拍摄的另一张截图进行比较。

好像太容易了吧？嗯，那是因为确实是。

# Android 中屏幕截图测试的替代方法

在我的调查中，我发现有三个顶级的截图测试库:

*   [拍摄](https://github.com/pedrovgs/Shot)
*   [脸书截图测试](https://github.com/facebook/screenshot-tests-for-android)
*   [购物作证](http://shopify.github.io/android-testify/)

> [Dropbox 刚刚发布了一款](https://github.com/dropbox/dropshots)但是说实话，我没有时间去查看或者阅读它，所以我没有把它放在这个故事中，但是如果有任何建议不适合你，请记住它。

每一种都有其优点和缺点，但就我而言，我最终使用了 Shopify evidence。

这一决定背后的原因是，在 Jenkins & Firebase 的 CI 环境中进行 UI 截图测试有一些限制，并且我还发现了更多关于 Shopify 替代方案的文档和支持。据我所知，他们都或多或少地以相同的方式工作，所以如果你从一个团队开始，想换团队，这看起来不会是一个特别痛苦的过程。

# 开始编码前需要知道的事情

实现一个基本的屏幕截图测试非常容易，但是有两个限制需要注意。

首先，当运行屏幕截图测试时，强制要求设备始终是具有相同 Android 版本的设备，或者您使用的仿真器每次都需要具有精确的配置。说到底，这些测试所做的只是在 UI 处于你认为正确的状态时，截屏用作基线，然后再次运行应用程序，截屏并与基线进行比较。如果设备的屏幕大小不同，或者语言不同，或者 Android 版本发生变化，测试就会失败，因为我们正在比较不同的图像。

第二，我不知道我之前建议的其他库，但是 Shopify evidence 只能够在测试配置时对指定的活动进行截图。如果您导航到另一个活动，屏幕截图将不会正确拍摄。我相信这个库是为遵循单一活动模式的应用程序设计的，在这种模式下，应用程序有一个活动作为容器，所有的 UI 都由片段和不同的导航栈驱动，或者可能有一些框架限制，但我更倾向于第一种可能性。

> 就像你可能对 Espresso 做的那样，在运行 UI 测试时，记得在设备的开发者设置中[禁用动画比例](https://github.com/dropbox/dropshots)

好的，一旦我们意识到了局限性，我们可以从一些代码开始。和往常一样，我们需要开始配置 Gradle 项目，在我们的项目 build.gradle 中添加以下依赖项。

```
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath "com.shopify.testify:plugin:1.2.0-alpha01"
    }
}
```

对于应用模块或您将要学习的模块。

```
plugins {
    id("com.shopify.testify")
}dependencies {
    androidTestImplementation "androidx.test:rules:1.4.0"
}
```

在 Gradle sync 之后，是时候写一个测试了。为此，正如您在文档中看到的，我们只需要以下几行。

```
@get:Rule var rule = ScreenshotRule(MainActivity::class.java)

    @ScreenshotInstrumentation
    @Test
    fun default() {
        rule.assertSame()
    }
```

这将启动 MainActivity，并使用该屏幕截图。有一些预先构建的 Gradle 任务可以与库进行交互，所以不要忘记查看它们。

为了运行我们刚刚编写的测试，我们需要记录一个基线截图。默认情况下，这个截图将被保存在`src/androidTest/assets/screenshots/<deviceName>`中，其中`deviceName`是`<apiLevel>-<deviceResolution>@<deviceDPI>-<locale>`。例如，如果我们在运行 Android 10 的 Pixel 3 模拟器中运行测试，截图将存储在`src/androidTest/assets/screenshots/30-1080x2160@440dp-en_US`中。

这就是为什么总是使用相同的仿真器配置是很重要的，因为如果您获取基线截图，然后使用不同的仿真器运行测试，测试甚至不会比较截图，因为它不会找到基线。

为了制作上述测试的基线截图，我们可以在终端中运行以下命令。

```
./gradlew screenshotRecord -PtestClass=com.screenshotSample.MyScreenshotTest#default
```

基线截图完成后，您可以像使用 Android Studio 的其他测试一样运行测试，您将有您的第一个截图测试！

知道您可以在获取基线和测试运行的屏幕截图之前配置规则来运行 Espresso 操作也是很有用的，这样您就可以导航到您需要测试的 UI 部分。

# 您可能会用到的一些额外功能

您可以查看 GitHub 项目中的 [wiki](https://github.com/Shopify/android-testify/wiki) 来了解更多关于该库提供的内容，但是在这里我将总结我在项目中使用最多的三个。

## 从截图中排除某些部分

此功能会忽略您选择的用户界面的特定部分。在基线截图中，你会看到，即使你排除了一些视图，截图显示了整个用户界面，但当测试运行时，它会忽略这一部分，只比较其余部分。例如，对于具有随机组件的视图，或者当您正在进行某项特定的工作，并且不希望您的测试失败时，这个特性非常有用。

## 比较图像

使用截图测试，为了知道测试失败的原因，您需要手动检查基线截图，然后是运行测试时拍摄的截图。有时候很容易看出来，但是可以想象，情况并不总是这样。为此，该库附带了一个 Gradle 任务，它依赖于 [ImageMagick](https://imagemagick.org/index.php) 来比较图像，并给你一个直观的表示，告诉你用户界面中哪些部分是不同的。

## 使用构建变体运行测试

默认情况下，库将采用您设置的第一个构建变体，但是如果您想要采用基线并使用不同的构建变体运行测试，您需要在 Gradle 模块中配置它。也许这不是很多人使用的东西，但对我来说它很重要，并且在文档中没有解释，所以我必须阅读源代码来学习如何做到这一点。记住，如果你需要维基中没有解释的东西，你也可以这样做。

因此，要指定一个构建变体，您只需要用您需要的配置将下面几行添加到 Gradle 模块中。

```
testify {
    installAndroidTestTask ""
    installTask ""
    applicationPackageId ""
    testPackageId ""
}
```

# 结论

如果你在阅读教程的时候一直在编写代码，你应该已经注意到这个测试与 Espresso 相比运行起来要快得多，也容易得多。当然，它们有一些限制，但我认为速度是至关重要的，特别是如果你运行的是按使用付费的 CI 系统。

你怎么想呢?你要试试这个吗？如果你想补充什么或问任何问题，别忘了留下评论。

如果你想阅读更多这样的内容，并支持我，不要忘记检查我的个人资料，或给媒体一个机会，成为会员，以获得我和其他作家的无限故事。一个月只要 5 美元，如果你使用这个链接，我会得到一小笔佣金。

[](https://medium.com/@molidev8/membership) [## 通过我的推荐链接加入 Medium—Miguel

### 阅读米格尔的每一个故事(以及媒体上成千上万的其他作家)。你的会员费直接支持米盖尔…

medium.com](https://medium.com/@molidev8/membership) 

# 分级编码

感谢您成为我们社区的一员！更多内容请参见[升级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在改造理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)