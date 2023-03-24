# 如何格式化一个目录中的所有文件

> 原文：<https://levelup.gitconnected.com/how-to-format-all-files-in-a-directory-with-prettier-5f0ff5f4ffb2>

## 在漂亮的 CLI 中使用漂亮的代码格式化程序格式化任何项目、文件夹或工作区。

![](img/ddaf3b6e89ffe235fcdbc22441fc07aa.png)

[Jp 瓦列里](https://unsplash.com/@jpvalery?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

[更漂亮的代码格式化程序](https://github.com/prettier/prettier) ( [更漂亮. io](https://prettier.io) )是一个宝藏，也是一种享受。如果你没有使用它，你应该立即开始。

但是你如何在一个全新的项目中使用它呢？

当克隆、派生或启动一个存储库时，格式可能会与您喜欢的不一致。

令人欣慰的是，使用 Prettier 可以在 5 秒钟内轻松修复格式。

# 不仅仅是一个 VS 代码扩展

我通常只是从内部使用 beauty[VS Code](https://code.visualstudio.com/)(使用[beauty-VS Code](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)扩展)，所以我对 beauty 的命令行威力感到惊讶。

> "从命令行使用`prettier`命令运行得更漂亮。不带任何参数运行它，查看[选项](https://prettier.io/docs/en/options.html)。”— [更漂亮的 CLI 文档](https://prettier.io/docs/en/cli.html)

[](https://prettier.io/docs/en/cli.html) [## CLI 更漂亮

### 从命令行使用更漂亮的命令运行更漂亮。不带任何参数运行它来查看选项。至…

更漂亮. io](https://prettier.io/docs/en/cli.html) 

更漂亮的 CLI 使得对整个目录应用格式变得容易。

# 如何格式化整个目录

首先，打开你最喜欢的终端。(我喜欢在 Windows 上使用[命令](https://cmder.net/))。接下来，使用[NPM install-g](https://docs.npmjs.com/cli/install)prettle 进行全局安装:

```
npm install --global prettier
```

从你想要格式化的目录，用`--write`运行更漂亮:

```
prettier --write .
```

这将使用 Prettier 递归格式化整个目录。

如果您不希望全局安装得更漂亮，那么您可以使用`[npx](/how-to-format-all-files-in-a-directory-with-prettier-5f0ff5f4ffb2?sk=8c6f88f74a298849df8c1ca5be7677f4)`命令(npm package runner)达到同样的效果:

```
npx prettier --write .
```

# 如何定制更漂亮

如果你想改变漂亮器的配置，把一个`[.prettierrc](https://prettier.io/docs/en/configuration.html)` [文件](https://prettier.io/docs/en/configuration.html)放在你项目的根文件夹里，然后运行漂亮器 CLI。

分叉项目可能已经有一个，例如:

```
{ "printWidth": 80, "singleQuote": true, "trailingComma": "all", "semi": false}
```

请注意，当您在项目文件夹中工作时，这个`.prettierrc`文件应该覆盖任何本地 VS 代码扩展设置。

# 享受更美好

我坚信使用 Prettier 可以通过使我的代码更容易阅读来减少错误，所以我在 VS 代码中自动使用它。

当从起始文件或样板文件打开一个新项目时，我想格式化整个项目以匹配我的偏好。

在命令行中使用 pretty 来格式化整个目录非常容易，在 Prettier CLI 中使用`--write`标志。

我希望你和我一样喜欢漂亮！

德里克·奥斯汀博士是《职业规划:如何在 6 个月内成为成功的 6 位数程序员》一书的作者，该书现已在亚马逊上出售。