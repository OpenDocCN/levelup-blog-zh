# 生成静态网站

> 原文：<https://levelup.gitconnected.com/generating-static-website-d666e529226a>

![](img/e6af46b127496af8f84189dfcf5df69c.png)

照片由 [Unsplash](https://unsplash.com/s/photos/website?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的[agency followeb](https://unsplash.com/@olloweb?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄

早在 21 世纪初，当我还是个孩子，对专业编程一无所知的时候，网站开发是我热衷的事情。没有 JavaScript，甚至没有 CSS。我的第一个网站是用 Microsoft FrontPage 创建的，它主要基于表格。当我 10 岁的时候，这对我来说太有趣了。

在那之后，我已经学会了 HTML，为什么我还需要别的东西呢？然后 Flash 网站和其他不同框架的时代来了，包括 JavaScript。我在 PHP 的帮助下做了一些网络开发，但是由于“一些”原因，我对它失去了兴趣。反正有一个 WordPress，所以无论我需要什么都用它。我一直很喜欢 HTML 和 CSS，只是太糟糕了，用纯 HTML+CSS 创建网站效率不高。然后，出乎意料地…

… **发布**，Swift 中的静态网站生成器由 John Sundell 发布。我对此感到兴奋，并期待着发布它。我有一年的时间没有写博客，我向自己保证，一旦我用 Plot 重写我的博客，我会重新开始。所以我们来了…

# 普通编码

文章最早发表在我的博客上——普通编码——[这里](https://ordinarycoding.com/articles/generating-static-website/)。它是使用 Publish 生成的。我真的推荐在那里阅读，因为它突出了一些代码。

# 计算机网络服务器

在我们开始用 Swift 开发我们的网站之前，首先，我们需要一个 *http 服务器*，谢天谢地，如果我们在 macOS 上，它已经安装了 python。我们只需要下面的命令

```
python -m SimpleHTTPServer 8080
```

它将在本地主机的当前目录中启动一个服务器——我们将在保存所有生成的文件的目录中运行这个命令。

在远程服务器上测试所有东西是可能的，但这将使我们的生活更加困难，手动上传所有的更改非常耗时，我们更希望立即测试我们的更改。

> 我想知道是否有可能用 iOS 应用程序(用 SwiftUI 编写)包装生成器，以便在移动设备上测试它。有一天我一定要试一试。

# 准备

除了工作服务器，我们还需要规划项目的文件结构。对于我的博客，当我在博客上工作时，我不想担心在电脑之间切换时丢失任何文件，所以博客的所有资源都被添加到 Xcode 项目，并用于生成 HTML 或在需要时复制。它包括文章文件(markdown)，静态图像(即。徽标)以及所有其他需要的文件，如样式文件和. htaccess。

下面你可以看到它的样子

```
├── Content
│   └── articles
│       ├── configuring-macos-for-ios-development.md
│       ├── creating-custom-siri-shortcuts.md
│       ├── flutter--my-thoughts-and-impressionspart-i.md
│       ├── fluttermy-thoughts-and-impressionspart-ii.md
│       ├── fluttermy-thoughts-and-impressionspart-iii.md
│       ├── fluttermy-thoughts-and-impressionspart-iv.md
│       ├── fluttermy-thoughts-and-impressionspart-v-final.md
│       ├── generating-static-website.md
│       ├── outdated
│       │   └── generating-static-website.md
│       ├── simple-custom-uinavigationcontroller-transitions.md
│       └── swifty-coredata.md
├── Package.resolved
├── Package.swift
├── Resources
│   ├── fonts
│   │   ├── SourceSansPro-Bold.ttf
│   │   ├── SourceSansPro-LightItalic.ttf
│   │   └── SourceSansPro-Regular.ttf
│   ├── images
│   │   ├── about-me.png
│   │   ├── articles
│   │   │   ├── configuring-macos-for-ios-development
│   │   │   ├── creating-custom-siri-shortcuts
│   │   │   │   ├── 1.png
│   │   │   │   ├── 10.gif
│   │   │   │   ├── 2.png
│   │   │   │   ├── 3.png
│   │   │   │   ├── 4.png
│   │   │   │   ├── 5.png
│   │   │   │   ├── 6.png
│   │   │   │   ├── 7.png
│   │   │   │   ├── 8.png
│   │   │   │   └── 9.png
│   │   │   ├── flutter--my-thoughts-and-impressionspart-i
│   │   │   ├── fluttermy-thoughts-and-impressionspart-ii
│   │   │   │   ├── 1.png
│   │   │   │   ├── 2.png
│   │   │   │   └── 3.gif
│   │   │   ├── fluttermy-thoughts-and-impressionspart-iii
│   │   │   ├── fluttermy-thoughts-and-impressionspart-iv
│   │   │   │   ├── 1.png
│   │   │   │   ├── 2.png
│   │   │   │   └── 3.png
│   │   │   ├── fluttermy-thoughts-and-impressionspart-v-final
│   │   │   │   └── 1.png
│   │   │   ├── generating-static-website
│   │   │   ├── simple-custom-uinavigationcontroller-transitions
│   │   │   │   └── 1.gif
│   │   │   └── swifty-coredata
│   │   │       ├── 1.png
│   │   │       ├── 2.png
│   │   │       └── 3.png
│   │   ├── favicon.png
│   │   ├── logo.png
│   │   ├── projects
│   │   │   ├── expenses-pal.png
│   │   │   └── watermaniac.png
│   │   └── social.png
│   └── styles.css
├── Sources
│   └── OrdinaryCoding
│       ├── main.swift
│       ├── models
│       │   └── Project.swift
│       ├── theme
│        │   ├── PlotExtensions.swift
│       │   ├── Theme+OrdinaryCoding.swift
│       │   ├── WebsiteContent.swift
│       │   ├── WebsiteFooter.swift
│       │   ├── WebsiteHeader.swift
│       │   └── WebsitePages.swift
│       └── website
│           ├── OrdinaryCodingWebsite.swift
│           └── QuotesPublishPlugin.swift
└── Output
```

因为我使用的是**发布**框架，它几乎是一个默认的结构，最适合这个工具。生成的网站在*输出*文件夹中，准备上传到生产服务器。在这个目录中，我还运行我的 localhost HTTP 服务器。

文章的文件名是我希望特定文章拥有的 URL 路径的名称。在`Resource`目录中是被复制的静态文件(包括`.htaccess`)。

# 项目

我已经使用自述文件的*快速入门*部分中描述的[发布](https://github.com/JohnSundell/Publish)命令行工具生成了 Xcode 项目。

我们将使用的依赖项是 Publish、Plot、Ink 和 Splash(或者实际上是 Publish 的插件)。

# 出版

> Publish，一个专门为 Swift 开发者打造的静态站点生成器。它支持使用 Swift 构建整个网站，并支持主题、插件和大量其他强大的定制选项。

除了*剧情*，这也是我们将要处理的主要框架。它用于管理我们的文件—资源、内容—以及实际的网站。html 文件。

# 情节

> 用于在 Swift 中编写类型安全的 HTML、XML 和 RSS 的 DSL

静电发生器的核心。这是最“神奇”的地方。

# 墨水

> 用 Swift 编写的快速灵活的 Markdown 解析器

如果你想用 markdown(一种轻量级的标记语言)来保存你的文章，它是可选的，但是非常方便。你可以坚持用它来绘制和生成文章的 HTML，但我认为用 Markdown-ie 来写更快。现在我正在 Markdown 写这篇文章。我有点忙，所以用其他方式写文章对我来说效率不高。我在所有设备上同步 markdown 文件，所以只要我有一点时间，我就在 Mac Book、装有 Windows 的 PC 或我的手机上写作。苹果的笔记是你最需要的(如果有人知道任何好的同步应用程序，请告诉我)。

# 溅泼的量

> 一个快速，轻量级和灵活的 Swift 语法荧光笔博客，工具和乐趣！

如果你想在你的文章中包含 Swift 代码，它是必备的，也可以很好地与其他语言兼容，如果没有，可以定制它。在我的博客上，我发布了 Swift 和 Dart 代码，正如你可能已经看到的那样，它对于 Dart 开箱即用也很好。不过，我很可能会在未来努力改进它。

# 发生

首先，仔细阅读 Plot 的自述。它包含了许多有用的提示，如果你不读它，你会后悔不知道。API 非常大，所以逐行阅读所有代码，或者寻找特定的函数可能会很有挑战性。

> 请记住，我的这个小项目仍在开发中，我展示的一些代码很可能不是最终版本。

`main.swift`是静止站点发生器的起点。

```
try OrdinaryCoding().publish(using: [
    .installPlugin(.splash(withClassPrefix: "swift-")), // 1
    .installPlugin(.quotes()), // 2 .copyResources(), // 3
    .addMarkdownFiles(), // 4
    .addDefaultTitles(), // 5 .addPage(.erro404()), // 6
    .addPage(.contact()), // 7
    .addPage(.projects(with: myProjects)), // 8 .generateHTML(withTheme: .ordinaryCoding), // 9 .generateRSSFeed(including: [.articles]), // 10
    .generateSiteMap(excluding: [ "404" ]) // 11
])
```

生成我的博客需要 11 个步骤。我来解释一下:1。安装用于语法高亮显示的 ["SplashPublishPlugin"](https://ordinarycoding.com/articles/generating-static-website/) 。2.安装一个我写的小插件，让我的文章中的引用有一点不同。3.从`Resource`目录中复制所有资源。5.将默认标题添加到*文章*页面。6.创建用于处理 404 http 错误的自定义页面。7.创建*联系人*页面。8.创建包含我的公共项目的*项目*页面——发布到商店和/或开源。9.生成所有的`.html`文件，包括我在前面步骤中添加的页面。请记住，这里的顺序是非常重要的，如果你在添加页面或做任何其他与网站相关的具体工作之前调用这个动作，它不会影响它。10.使用来自*文章*部分的所有文件生成 RSS 提要。11.生成排除 404 页面的站点地图，因为它不应该包含在那里。

> 如果你想知道更多关于具体步骤的信息，请在 Twitter 上告诉我(或者查看它的代码)。

`OrdinaryCoding` class 是一个符合 *Publish* 的`Website`协议的类，它包含了 *Publish* 的引擎生成网站所需的关于我的博客的一些基本信息。

```
struct OrdinaryCoding: Website {
    enum SectionID: String, WebsiteSectionID {
        case articles
    } struct ItemMetadata: WebsiteItemMetadata {
    } var url = URL(string: "https://ordinarycoding.com")!
    var name = "Ordinary Coding"
    var description = "Just another blog about programming - iOS, macOS, Flutter"
    var language: Language { .english }
    var imagePath: Path? { "images/social.png" }
    var tagHTMLConfig: TagHTMLConfiguration? { TagHTMLConfiguration(basePath: "topics") }
}
```

我也使用自定义主题(作为基础)🙂).

如果没有文章，我们的博客会怎样？我把它们都放在*内容/文章*目录中作为*降价*文件。在 *Publish* 发布之前，我有一点点不同的结构——我更喜欢将每篇文章放在不同的文件夹中，并与相关的图片分组，但遗憾的是 *Publish* 强迫我这样做——幸好它是开源的，也许有一天我会有时间以我想要的方式改进它。

所有与 HTML 相关的代码我都保存在从 *Plot* 框架到`Node` enum 的扩展中。而且我认为这是保持项目干净的最好方法，分享任何可以分享的东西。

# 结论

我花了大约 2 周的时间(每天大约 1-3 个小时)从零开始创建网站，最耗时的任务是用 markdown 语言重写 9 篇文章并准备 CSS 文件。除此之外，你还需要至少有一个基本的知识，如何写 HTML 和如何用 CSS 样式化它。css 文件，但我们大多数人更愿意定制它)。

在使用这个 DSL 的同时，仍然有空间让你自己的技能发光或者学习新的东西，并且有无限的可能性如何使用这个框架。看看人们会带来什么会很有趣。

> Plot 是 Swift 和 HTML 之间缺失的一环。

我真的希望静态网站生成器会越来越受欢迎。我在玩 Publish 和 Plot 的时候获得了很多乐趣，并把它推荐给每个想建立这样一个网站的人。

# 最后的话

我希望你在使用 Plot 建立你的网站的时候会发现这篇文章是有用的。如果您有任何问题，请随时[联系我](https://ordinarycoding.com/contact)，最好是通过 Twitter 或电子邮件。

感谢您的阅读。