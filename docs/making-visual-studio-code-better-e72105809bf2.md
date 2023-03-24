# 让 Visual Studio 代码变得更好🔥✨🛠

> 原文：<https://levelup.gitconnected.com/making-visual-studio-code-better-e72105809bf2>

## 它最出名的是:扩展

![](img/c60875add09b4b8ec395a2de0d6570cd.png)

Jan Kahánek 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

对开发者来说，文本编辑器就是一切。作为一名程序员，我们每天都坐着编辑文本。我们大部分时间都在看代码，寻找问题的最佳解决方案。阅读和写作。思考为什么这个函数或那个方法不起作用。一行一行地、一个变量一个变量地遍历代码，然后压缩🐛虫子在左边，右边和中间。您选择的文本编辑器决定了您将如何与代码和开发环境进行交互。

![](img/1a61b6cce75089e33c6cc3b168bbd4bd.png)

乔恩·泰森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

现在，你可以在 Windows 上使用记事本这样的传统文本编辑器，或者你可以变得超级花哨，只在 XCode 中开发(因为[苹果需要 Apple for Apple](https://developer.apple.com/xcode/) )。有 VIM，EMACS，Sublime，Nano，Notepad++，gedit，Ulysses。[图形化，非图形化，更多](https://en.wikipedia.org/wiki/List_of_text_editors)。然而，许多文本编辑器只是:文本编辑器📝(谁想到的！)，作为开发人员，我们需要的不仅仅是将文本流编辑到文件中。

就我个人而言，我坚持使用 Visual Studio 代码(VSC 或 VSCode)。它是命令行之外的首选编辑器(我是一个成熟的 VIM 爱好者，但这是另一个故事)，最大的原因是因为所有出色的扩展！VSC 的扩展更好的原因是它们由大型社区积极维护🌐使用 VSC。这真的是开源的缩影:你对代码投入的精力越多，它就越“好”。

有了“不仅仅是一个文本编辑器”的初步了解，让我们来谈谈我发现的一些有助于我的工作流和编程的扩展。

![](img/29192721020d46aaf74a37e0b936e1cb.png)

由[Petr macha ek](https://unsplash.com/@machec?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## **语言支持**🆘

因为我懂几种语言，所以我喜欢用几种语言编程。有很多小技巧可以携带跨语法(我知道这是一种谬误的思维方式，但我仍然认为那里有真理)。幸运的是，对于大多数语言来说，有一个扩展可以将 VSCode 转换成您正在使用的语言的编辑器。比如:

**Visual Studio IntelliCode**

该扩展将为您提供与 Visual Studio 中相同的智能感知。它不是默认安装的(它应该是)，所以它是我列表中的第一个。这个扩展覆盖了一些语言，我还列出了其他要安装的扩展，但是在这个扩展上，我认为越多越好。

[](https://marketplace.visualstudio.com/items?itemName=VisualStudioExptTeam.vscodeintellicode) [## Visual Studio intelli code-Visual Studio 市场

### Visual Studio 代码的扩展-人工智能辅助开发

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=VisualStudioExptTeam.vscodeintellicode) 

**巨蟒**

我最喜欢的脚本语言之一。当我想要一个快速的脚本时，我会使用 Python，对于快速脚本，我们还需要一些快速语言支持。您可以在 VSCode 的右下角更改扩展使用的 Python 版本。

[](https://marketplace.visualstudio.com/items?itemName=ms-python.python) [## Python - Visual Studio 市场

### 一个对 Python 语言有丰富支持的 Visual Studio 代码扩展(对于所有积极支持的版本…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 

**C/C++**

真正让我爱上编程的是:C/C++。微软提供的另一个扩展，它会给你所有的 C 项目提供支持，除了 C#但是…

[](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) [## C/C++ - Visual Studio 市场

### 这个 C/C++扩展的预览版为 Visual Studio 代码增加了对 C/C++的语言支持，包括特性…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) 

**C#**

看，我们有一个 C#扩展。也是微软出的(哇！).如果你想和 Unity 或者。这是一个必须的扩展——在提到的智能感知之上。

[](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) [## C# - Visual Studio 市场

### 欢迎使用 Visual Studio 代码的 C#扩展！该扩展提供了以下功能…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 

**Red Hat 对 Java(TM)的语言支持**

Java 在 1200 万台机器上运行？为什么不提供一些支持，以防您不得不打开遗留 Java 项目。

[](https://marketplace.visualstudio.com/items?itemName=redhat.java) [## Red Hat 对 Java(TM)的语言支持- Visual Studio Marketplace

### 通过 Eclipse JDT 语言服务器提供 Java 语言支持，该服务器利用 Eclipse JDT、M2Eclipse 和…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=redhat.java) 

**NPM**

节点包管理器。魔鬼对所有事物的前端。虽然这是一个必要的缺点，但是使用这个扩展可以帮助您处理`package.json`文件，并为您提供一个方便的边栏工具来运行您的所有脚本。

[](https://marketplace.visualstudio.com/items?itemName=eg2.vscode-npm-script) [## npm - Visual Studio 市场

### 这个扩展支持运行 package.json 文件中定义的 npm 脚本，并验证安装的模块…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=eg2.vscode-npm-script) 

**德诺**

Deno 是下一个最伟大的 TypeScript 运行时。没错，JavaScript 正在升级，而且是安全的升级。 [Deno](https://deno.land) 是 NodeJS 的创造者创造的，所以它一定很棒，对吧？

[](https://marketplace.visualstudio.com/items?itemName=denoland.vscode-deno) [## Deno - Visual Studio 市场

### 功能:带有 LSP 的客户机/服务器模型。该扩展将客户机/服务器与 LSP 分开，这意味着复杂的…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=denoland.vscode-deno) 

**YAML**

如果您需要在 JSON 文件之外存储数据，YAML 文件非常有用。我从来没有完全深入到我的搜索深度，只是我需要在这里和那里使用它，所以当我打开那些`.yaml`或`.yml`文件时，我有这个扩展。

[](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-yaml) [## YAML - Visual Studio 市场

### 通过 yaml 语言服务器为 Visual Studio 代码提供全面的 YAML 语言支持，内置…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-yaml) 

## **格式化/林挺🚩**

现在，格式化和林挺可以归入语言支持，但是有一些扩展**只是**用于格式化和林挺语言；既然现在用了，我就不能回去了。

**支架对着色机 2**

这是我最喜欢的扩展之一(仅次于智能感知)。当我编程时，BPC2 只是添加了额外的*pop*。尤其是 Python。当我在寻找一个函数或方法的括号在哪里相遇时，我简单地沿着彩虹——字面上的意思——找到 bug 所在的地方。

[](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer-2) [## 括号对着色程序 2 — Visual Studio 市场

### 这种扩展允许用颜色来识别匹配的括号。用户可以定义匹配哪些令牌，并且…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer-2) 

**美化**

美化是一个很好的代码格式化程序，但是，只有当你是一个前端开发人员时，它才是最好的(正如你将在链接的描述中看到的)。

[](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify) [## 美化- Visual Studio 市场

### 美化 Visual Studio 代码中的 javascript、JSON、CSS、Sass、HTML。VS 代码内部使用 js-美化，但是缺少…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify) 

**更漂亮**

我发现漂亮是我最常用的格式。同样，虽然它主要对前端开发人员有用，但我发现每当美化失败时我都会使用它。

[](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) [## 更漂亮的代码格式化程序- Visual Studio 市场

### Visual Studio 代码的扩展-使用更漂亮的代码格式化程序

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) 

**VS 代码的编辑器配置**

编辑器配置是一个了不起的项目。重复本文的开头，有这么多不同的编辑器。更不用说操作系统、文本格式等了。格式差异太大！这就是`.editorconfig`文件派上用场的地方。大多数新的编辑器能够读取根项目文件夹中的这个文件，并遵守为项目设置的格式规则！

[](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig) [## VS 代码的 editor config-Visual Studio 市场

### 此插件试图用. editorconfig 文件中的设置覆盖用户/工作区设置。没有额外的或…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig) 

**诚信通**

[TypeScript 去年正式转投 ESLint。](https://medium.com/palantir/tslint-in-2019-1a144c2317a9)从那以后，我只使用 ESLint，就像这个社区的其他人一样，我不会再回头了。

[](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) [## ESLint - Visual Studio 市场

### Visual Studio 代码扩展——将 ESLint JavaScript 集成到 VS 代码中。

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 

## **片段✂**

我没有尽可能多地使用片段。但是当我安装的时候，我会安装已经为我做好的。

[](https://marketplace.visualstudio.com/items?itemName=burkeholland.simple-react-snippets) [## 简单的 React 片段- Visual Studio 市场

### React 片段和命令的基本集合。只有你需要的，仅此而已。没有 Redux。没有反应…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=burkeholland.simple-react-snippets) 

你也可以自己制作片段。

## **其他有用的工具🧰**

实在想不出他们还能去哪里。

**GitLens — Git 增压**

这是一个非常方便的开发工具。当您阅读代码时，光标所在的那一行会显示一个 git-fall，并让您知道上次更新的时间。这在团队项目中非常方便。

[](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) [## GitLens — Git 增压— Visual Studio 市场

### Visual Studio 代码扩展—增强内置于 Visual Studio 代码中的 Git 功能—可视化代码…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) 

**待办事项高亮显示**

我用这个扩展太多了。也就是说，它帮助我跟踪我的任务项在给定文件中的位置。不仅如此，你还可以在设置中自定义和添加自己的亮点！我用:TODO，NOTE，BUG，REVIEW，TEST 来做我自己的笔记。

[](https://marketplace.visualstudio.com/items?itemName=wayou.vscode-todo-highlight) [## 待办事项突出显示— Visual Studio 市场

### 突出显示代码中的 TODO、FIXME 和其他注释。有时你会忘记查看你添加的待办事项…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=wayou.vscode-todo-highlight) 

**Unity 调试器**

游戏界的人可能听说过 Unity3d 引擎。不用等 8 个小时来安装 Visual Studio，你可以在 3 分钟内安装 VSCode 和这个扩展，[在 Unity](https://code.visualstudio.com/docs/other/unity) 中更改你的编辑器首选项，然后你就可以开始了！

[](https://marketplace.visualstudio.com/items?itemName=Unity.unity-debug) [## Unity 调试器- Visual Studio 市场

### Unity Technologies 并未正式支持此扩展。使用 Visual Studio 代码调试您的 Unity C#…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=Unity.unity-debug) 

**码头工人**

如果你使用 Docker，你可能需要这个扩展。使用 Docker 扩展会给你提供 Docker 文件检查的小优势，以及一个漂亮的侧边栏工具来帮助本地运行和管理你的 Docker 容器。

[](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) [## Docker — Visual Studio 市场

### Docker 扩展使得从 Visual Studio 代码构建、管理和部署容器化的应用程序变得容易。它…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) 

**汽车评论区**

做注释和文档是一件痛苦的事情，但是当你离开一个项目一段时间后，它会派上用场。那些笔记是编程黑暗洞穴中的蜡烛棒。花点时间正确地评论，有了这个扩展，格式化评论就轻而易举了。

[](https://marketplace.visualstudio.com/items?itemName=kevinkyang.auto-comment-blocks) [## 自动注释块- Visual Studio 市场

### Visual Studio 代码的扩展-为 Javadoc 风格的多行注释和…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=kevinkyang.auto-comment-blocks) 

## 主题

**紫色的深浅**

到目前为止，我最喜欢的 VSCode 主题。我暂时不会换…你只要自己去看就好了。

[](https://marketplace.visualstudio.com/items?itemName=ahmadawais.shades-of-purple) [## 紫色的阴影- Visual Studio 市场

### Visual Studio 代码的扩展-🦄一个专业的主题套件，为您的虚拟世界精心挑选大胆的紫色色调

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=ahmadawais.shades-of-purple) 

**熊猫主题**

我下一个最喜欢的主题是那些令人沮丧的日子。我也很喜欢这里的简单。

[](https://marketplace.visualstudio.com/items?itemName=tinkertrain.theme-panda) [## 熊猫主题- Visual Studio 市场

### 一个极小的，黑暗的语法主题。这是熊猫语法主题的最新版本。这是一个黑暗的语法主题…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=tinkertrain.theme-panda) 

**虚拟代码图标**

你不想整天坐着看文件名吧？不要！让我们在 VSCode 中添加一些漂亮的图标，让它真正成为一种体验。

[](https://marketplace.visualstudio.com/items?itemName=vscode-icons-team.vscode-icons) [## vscode-icons - Visual Studio 市场

### 将图标添加到您的 Visual Studio 代码中(支持的最低版本:1.31.1)要安装该扩展，只需执行…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=vscode-icons-team.vscode-icons) 

编程时将这些工具放在我的工具箱中，使我的开发生活变得更加容易，也更加丰富多彩。我希望你们中的一些人能够为了自己的利益使用这些扩展。也许你会寻找更多更适合你工作流程的东西，或者[你会对你自己的](https://code.visualstudio.com/api/get-started/your-first-extension)做一些惊人的扩展！所有的权力都给你，让我们找到或做一只鞋👟很合适！

你最喜欢的编辑器是什么？是 VSCode 吗？是 Visual Studio 吗？崇高？VIM？你是 EMACS 女孩吗？我很好奇你用的是什么！让我们开始讨论吧。

感谢您抽出时间阅读。祝一切顺利，保持安全——斯潘塞

# 分级编码

感谢您成为我们社区的一员！ [**订阅我们的 YouTube 频道**](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1) 或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题

### 掌握编码面试的过程

技术开发](https://skilled.dev)