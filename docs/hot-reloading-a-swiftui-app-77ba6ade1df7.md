# 热重装 SwiftUI 应用程序

> 原文：<https://levelup.gitconnected.com/hot-reloading-a-swiftui-app-77ba6ade1df7>

![](img/a316f070401c2db2a956ee83b1f36879.png)

无需编译您的 SwiftUI 应用程序，即可在 iOS 模拟器中更改代码并立即看到更新！

你需要

*   `[Inject](https://github.com/krzysztofzablocki/Inject)`，由[Krzysztof zaocki](https://twitter.com/merowing_)创建的 Swift 包
*   `[InjectionIII](https://github.com/johnno1962/InjectionIII)`，这是一个 macOS 应用程序，用于执行繁重的工作(即，监视修改后的源代码并插入新功能，就好像它已被编译到 SwiftUI 应用程序中一样)

您可以在以下文章中了解更多关于 Swift 产品包的信息。

[](https://www.merowing.info/2022/04/hot-reloading-in-swift/) [## Swift 中的热重装

### 现在是 2040 年，我们最新的 MacBook M30X 处理器可以瞬间编译大型 Swift 项目…

www.merowing.info](https://www.merowing.info/2022/04/hot-reloading-in-swift/) 

我还推荐阅读约翰·霍尔兹沃思的《迅捷的秘密》。他是`InjectionIII`(也称为“Xcode 注入”)的创造者，并分享了 iOS 开发热重装的概念！

[](https://books.apple.com/us/book/swift-secrets/id1551005489) [## Swift 秘密

### InjectionIII 的创建者，这是一个流行的 macOS 应用程序，用于更新函数/方法的实现…

books.apple.com](https://books.apple.com/us/book/swift-secrets/id1551005489) 

有大量的阅读材料，所以我想让你看看热重装的行动！在我的 YouTube 视频中，我向您展示了测试 SwiftUI 应用程序所需的所有步骤。

1.  添加 Swift 包
2.  在包含 SwiftUI 视图的源文件中导入其模块
3.  将`@ObservedObject private var iO = Inject.observer`变量添加到 SwiftUI 视图中
4.  在你的视图体定义的末尾调用`.enableInjection()`

视频跳过了如何安装`InjectionIII.app`(详情见[此处](https://github.com/krzysztofzablocki/Inject#individual-developer-setup-once-per-machine))但我会告诉你如何使用 macOS 应用程序。

我也分享一些观察和技巧和提示！

你可以在我的 GitHub 上找到测试应用程序，提交历史可视化了为 SwiftUI 应用程序进行热重装所做的每个步骤。

[](https://github.com/MarcoEidinger/InjectSwiftUIExample) [## GitHub-MarcoEidinger/InjectSwiftUIExample:示例如何在…

### 示例如何在 SwiftUI 应用程序中使用 krzysztofzablocki/Inject 77d 45 f 7:添加演示应用程序而不注入 25c14c1:添加…

github.com](https://github.com/MarcoEidinger/InjectSwiftUIExample) 

*最初发布于*[*https://blog . ei dinger . info*](https://blog.eidinger.info/hot-reloading-a-swiftui-app)*。*