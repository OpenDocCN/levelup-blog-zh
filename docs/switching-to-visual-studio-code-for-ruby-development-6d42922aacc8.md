# 切换到 Visual Studio 代码进行 Ruby 开发

> 原文：<https://levelup.gitconnected.com/switching-to-visual-studio-code-for-ruby-development-6d42922aacc8>

我如何以及为什么从商业 Ruby IDE 转换到 VSCode

![](img/abf43c93956dfe3e4a55d6653e41e9df.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=838667) 的[贝西](https://pixabay.com/users/bessi-909086/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=838667)

在过去的 12 年里，我一直使用商业 IDE 来开发 Ruby 和 Ruby on Rails，因为我第一次开始使用 Ruby 这种出色的编程语言进行开发。我真的很开心，但是最近，我想说是去年，我的开发流程变得越来越慢。IDE 很难索引我的文件，但是，一般来说，当我加载大项目时，我的笔记本电脑 CPU 也开始变得很难。

注意:我是一个 MacBook Pro 用户，虽然比较老但是仍然很强大(对于我做的事情来说)我相信:Retina 15 英寸 2015 年中，2,5 GHz 四核英特尔酷睿 i7，16 GB 1600 MHz DDR3，Intel Iris Pro 1536 MB，500GB SSD。

因为我的许多同事都在使用 Visual Studio 代码，所以我决定尝试一下。

# 判决

我对 VS 代码非常满意，我会推荐它。它速度很快，并为我提供了我在商业 IDE 中使用的所有工具。我什么都没错过。

# 我如何切换—我安装的扩展

如果你决定在 Ruby 开发中使用 VS 代码，你将不得不安装一些扩展来使它工作良好。在 VS 代码上安装扩展绝对是轻而易举的事情。

# 扩展ˌ扩张

这里是我已经安装的扩展列表，加上一些其他有用的提示，我相信你应该知道。请注意，我不会对每个扩展的文档进行扩展。

## 自动重命名标签

[](https://github.com/formulahendry/vscode-auto-rename-tag) [## GitHub-formula Hendry/vs code-auto-rename-tag:自动重命名成对的 HTML/XML 标签

### 自动重命名成对的 HTML/XML 标签。为 formula Hendry/vs code-auto-rename-tag 开发做出贡献，创建一个…

github.com](https://github.com/formulahendry/vscode-auto-rename-tag) 

**注意** : VS 代码`”editor.linkedEditing”: true`应该做同样的事情，但是它对`*.html.erb`文件不起作用。

## 自动关闭标签

[](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-close-tag) [## 自动关闭标记- Visual Studio 市场

### Visual Studio 代码的扩展-自动添加 HTML/XML 结束标记，与 Visual Studio IDE 或 Sublime 文本相同

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-close-tag) 

**注意** : VS 代码原生支持各种文件类型。但不是针对`*.html.erb`的文件。

**重要:**要让这个扩展支持`*.html.erb`文件，你需要在扩展设置`auto-close-tag.activationOnLanguage`里面添加`html.erb`。

## 剪贴板历史

[](https://github.com/aefernandes/vscode-clipboard-history-extension/) [## GitHub-aefernandes/vs code-剪贴板-历史记录-扩展

### 保留您复制和剪切项目的历史记录，并在需要时重新粘贴。保存所有复制和剪切项目的历史粘贴自…

github.com](https://github.com/aefernandes/vscode-clipboard-history-extension/) 

注意，这个扩展可能不能很好地工作，除非你删除了默认的 VS Code 快捷键来把东西放到剪贴板上，并保留扩展自带的快捷键。

## 代码拼写检查器

[](https://github.com/streetsidesoftware/vscode-spell-checker) [## 一个简单的源代码拼写检查器

### 一个基本的拼写检查器，适用于代码和文档。这个拼写检查器的目标是帮助捕捉常见的…

github.com](https://github.com/streetsidesoftware/vscode-spell-checker) 

请务必阅读说明，并启用 Ruby 和 RSpec 文件类型支持。

## 黄瓜(小黄瓜)全语言支持

[](https://github.com/alexkrechik/VSCucumberAutoComplete) [## GitHub-alexkrechik/VSCucumberAutoComplete:Cucumber(小黄瓜)对 VSCode 的完全支持扩展

### VSCode Cucumber(小黄瓜)语言支持+格式+步骤/页面对象自动完成语法突出显示基本片段…

github.com](https://github.com/alexkrechik/VSCucumberAutoComplete) 

## DotENV

[](https://github.com/mikestead/vscode-dotenv) [## GitHub - mikestead/vscode-dotenv:用于安装超过 1M 的 vscode 的 dotenv 扩展

### vscode 的 DotENV 端口。如果你用特定的。环境文件命名惯例(例如. env.jenkins)您可以添加 dotenv…

github.com](https://github.com/mikestead/vscode-dotenv) 

## 纵向

[](https://github.com/kaiwood/vscode-endwise) [## GitHub - kaiwood/vscode-endwise:明智地在 Ruby 中添加“end”。

### 这是一个扩展，它明智地将“end”关键字添加到 Ruby 或 Crystal 等语言的代码结构中，同时…

github.com](https://github.com/kaiwood/vscode-endwise) 

## ERB 助手标签

[](https://github.com/rayhanw/vscode-erb-helpers) [## GitHub-ray hanw/vs Code-erb-helpers:对编码有用的 Visual Studio 代码片段的集合…

### 这个扩展是 ERB-VSCode-Snippets 的一个分支，它是 ERB-Sublime-Snippets 的一个分支，Visual Studio 的一个集合…

github.com](https://github.com/rayhanw/vscode-erb-helpers) 

## GitLens — Git 增压

[](https://github.com/gitkraken/vscode-gitlens) [## GitHub-Git kraken/VS Code-Git lens:充能 Git inside VS Code，解锁未开发的知识…

### 在 VS 代码中充能 Git，并在每个知识库中释放未开发的知识

github.com](https://github.com/gitkraken/vscode-gitlens) 

## IntelliJ IDEA 键绑定

[](https://github.com/kasecato/vscode-intellij-idea-keybindings) [## GitHub-kase Cato/VS Code-IntelliJ-IDEA-key bindings:VS 代码的 IntelliJ IDEA 键绑定的端口。

### VS 代码的 IntelliJ IDEA 键绑定端口。包括流行的 JetBrains 产品的键盘映射，如 IntelliJ Ultimate…

github.com](https://github.com/kasecato/vscode-intellij-idea-keybindings) 

只有当你想从 Intellij IDEA 或者类似的 JetBrains IDE 如 RubyMine 中切换时，这才是有用的。

## 键盘宏测试版

[](https://github.com/tshino/vscode-kb-macro) [## GitHub-t shino/vs Code-k B- macro:Visual Studio 代码的可记录键盘宏

### 使用这个 Visual Studio 代码扩展，您可以记录和回放您的键盘输入。Ctrl+Alt+R:开始/停止…

github.com](https://github.com/tshino/vscode-kb-macro) 

## 轨道

[](https://github.com/bung87/vscode-rails) [## GitHub-bung 87/vs code-Rails:vs code Rails 扩展，Ruby On Rails“资产标签助手”和“表单…

### vscode rails 扩展，Ruby On Rails“资产标签助手”和“表单助手”片段。erb 语法重点，相关…

github.com](https://github.com/bung87/vscode-rails) 

## Rails 数据库模式

[](https://github.com/aki77/vscode-rails-db-schema) [## GitHub-aki 77/vs code-Rails-d b-Schema:Rails DB 模式的定义和完成提供程序。

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/aki77/vscode-rails-db-schema) 

## 导轨符合规格

[](https://marketplace.visualstudio.com/items?itemName=sporto.rails-go-to-spec) [## Rails 走向 Spec - Visual Studio 市场

### Visual Studio 代码的扩展——在 Rails 中的代码和规范之间切换

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=sporto.rails-go-to-spec) 

## 轨道运行规格

[](https://github.com/noku/rails-run-spec-vscode) [## GitHub-noku/rails-run-spec-vs Code:用于运行规范文件的 Visual Studio 代码扩展

### 该扩展提供了在内置 vscode 终端中运行规范文件的基本命令。可用命令:边栏…

github.com](https://github.com/noku/rails-run-spec-vscode) 

## 红宝石太阳图

[](https://github.com/castwide/vscode-solargraph) [## GitHub-cast wide/vs Code-solar graph:solar graph 的 Visual Studio 代码扩展。

### Solargraph 是一个语言服务器，它为 Ruby 提供智能感知、代码完成和内联文档。

github.com](https://github.com/castwide/vscode-solargraph) 

绝对喜欢。请仔细阅读这个扩展的文档。我自己生成了一个`.solargraph.yml`文件，并将`solargraph`放在我的`Gemfile`(组`development`和`test`)中。

## Ruby 测试运行程序

[](https://gitlab.com/mateuszdrewniak/ruby-test-runner) [## Mateusz Drewniak / Ruby 测试运行程序 GitLab

### 用于运行 Ruby 测试(minitest 和 rspec)的 VSCode 扩展

gitlab.com](https://gitlab.com/mateuszdrewniak/ruby-test-runner) 

## Ruby 测试浏览器

[](https://github.com/connorshea/vscode-ruby-test-adapter) [## GitHub-Connor shea/VS Code-ruby-Test-adapter:一个用于 VS 代码测试的 Ruby 测试适配器扩展…

### 这是一个针对 VS 代码测试浏览器扩展的 Ruby 测试浏览器扩展。该扩展支持 RSpec 和…

github.com](https://github.com/connorshea/vscode-ruby-test-adapter) 

如果您想使用包含搜索功能的原生 UI，您可能需要设置`testExplorer.useNativeTesting: true`。

## 草稿栏

[](https://github.com/buenon/scratchpads) [## GitHub-buenon/scratchpads:Visual Studio 代码的 Scratchpads 扩展

### 创建多个草稿栏文件，以便在编码时涂鸦。

github.com](https://github.com/buenon/scratchpads) 

太棒了，因为它允许我创建任何类型的临时文件，而不用将它们添加到我的工作空间或工作树中。

## 微小的

[](https://marketplace.visualstudio.com/items?itemName=sianglim.slim) [## Slim - Visual Studio 市场

### 对基于[https://github.com/slim-template/ruby-slim.tmbundle]Visual Studio 代码的精简语言支持…

marketplace.visualstudio.com](https://marketplace.visualstudio.com/items?itemName=sianglim.slim) 

只有当您使用[[slim](http://slim-lang.com/)]([http://slim-lang.com/](http://slim-lang.com/))作为视图模板语言时，才需要这样做。

## vscode-gemfile

[](https://github.com/bung87/vscode-gemfile) [## GitHub-bung 87/vs code-gemfile:vs code 扩展在 Gemfile 中提供悬停链接指在线…

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/bung87/vscode-gemfile) 

## 虚拟代码-图标

[](https://github.com/vscode-icons/vscode-icons) [## GitHub-vs Code-Icons/vs Code-Icons:Visual Studio 代码的图标

### 将图标添加到您的 Visual Studio 代码中(支持的最低版本:1.40.2)管理拉请求和行为代码…

github.com](https://github.com/vscode-icons/vscode-icons) 

## vscode-pdf

[](https://github.com/tomoki1207/vscode-pdfviewer) [## GitHub-tomoki 1207/vscode-PDF viewer:在 vs code 中显示 PDF 预览。

### 在 VSCode 中显示 pdf。下载最新的预构建版本。提取 ZIP 文件。覆盖。/lib/*通过提取的目录。如果…

github.com](https://github.com/tomoki1207/vscode-pdfviewer) 

# 一些有用的 VSCode 功能

## 概述

我非常喜欢*概要*导航器功能。它为您提供了手头文件的结构视图。问题是它不能很好地处理 RSpec `spec.rb`文件。但我接受了。向我显示测试列表的*测试*选项卡足以显示测试的结构，并允许我跳到规范文件中的正确位置。所以，我没事。

## 时间表

这是一个很棒的特性，我已经在我的商业 IDE 中拥有了，所以我很高兴在 VS 代码中也发现了它。它允许我导航到文件的以前版本，既可以在源代码库中，也可以在本地文件存储/工作树中。

## 源代码控制

*源代码控制*导航器也非常强大，允许您查看文件的变化并比较分支。

# 结论

设置 VS 代码来开发 Ruby 和 Ruby on Rails 项目可能需要很小的努力，但这并不困难，也是值得的。你会得到非常好的体验，而且非常快。在将这种设置用于大小项目之后，我非常高兴，我不会再回到商业 IDE。不过还是那句话，*永不言弃*。

*注意*:我会回来发布一篇额外的帖子，介绍我在 VS 代码中的 React 和 React 原生开发经验。

# 分级编码

感谢您成为我们社区的一员！更多内容见[升级编码出版物](https://levelup.gitconnected.com/)。
跟随: [Twitter](https://twitter.com/gitconnected) ， [LinkedIn](https://www.linkedin.com/company/gitconnected) ，[简讯](https://newsletter.levelup.dev/)
向上一级是转型科技招聘👉 [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)