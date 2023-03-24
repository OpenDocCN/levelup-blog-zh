# 用电子在 Node.js 中打印(Electron forge+node-printer+web pack)

> 原文：<https://levelup.gitconnected.com/printing-in-node-js-with-electron-electronforge-node-printer-webpack-5f106073aefc>

如果你在电子应用中遇到一些关于[节点打印机](https://www.npmjs.com/package/printer)的问题——在开发期间或者甚至在安装期间——这篇文章可以给你提供帮助。

那么，在 node.js 中打印，嗯？
实际上，在 node.js 中单独使用节点打印机应该是一件容易的事情，当你在一个电子应用程序中尝试同样的方法时，挑战就开始了(或者也许我只是一个不幸的家伙)。

## 关于图书馆

目前我写这篇文章给你，据我所知，node 中唯一现存的打印库是 [node-printer](https://www.npmjs.com/package/printer) 。坏消息是它是一个无人维护的库，但好消息是我们有一个很棒的社区和许多分支😊。

据我所知，目前维护最多的 fork 是 [@thiagoelg/node-printer](https://www.npmjs.com/package/@thiagoelg/node-printer) 。因此，现在当提到术语“节点打印机”时，我将讨论这个分支。

## 第一层

还没有谈到我们的主要目的，但已经谈到了电子节点打印机的警告，我们必须小心游戏中 3 个角色的版本之间的兼容性:节点打印机，node.js 和电子。您必须使用 node.js 版本，该版本既可以与 node-printer 版本一起使用，也可以与您将使用的电子版本一起使用。

由于 node-printer 是一个比 electron 更老的库，您可能应该开始匹配它与 node.js 的兼容性— [您可以在 lib](https://github.com/thiagoelg/node-printer/commits/main) 的提交中搜索——然后找到一个合适的 electron 版本(大多数最新版本应该可以工作)。

匹配的版本，你应该能够得到节点打印机安装。要让它运行起来，你还需要一个步骤——只有在不使用 Electron Forge 时才需要，因为它会自动处理它——关于 electron 中的[原生模块(安装后脚本中的 electron-rebuild)。](https://www.electronjs.org/pt/docs/latest/tutorial/using-native-node-modules)

## 第二层，老板已经

这篇文章的真正意义在于当我们想要使用我们亲爱的节点打印机和[电子锻造模板和 webpack](https://www.electronforge.io/templates/webpack-template) (例如在 react 应用程序中)。你会看到，即使做了我们上面看到的所有事情，你仍然会得到一个“找不到模块节点-打印机”(或类似的东西)。

webpack 构建可能没有正确处理 lib 包中的 node_printer.node 文件。为了解决这个问题，我们必须进行两种不同的配置，以便在开发模式和生产模式下都可以解决这个问题。

要让它在开发模式下工作，首先你必须让 webpack 忽略整个 lib 包，这很简单，只需将它添加到你的 main.webpack.js:

```
...
externals: { '@thiagoelg/node-printer': 'commonjs @thiagoelg/node-printer',},
...
```

就我的研究而言，当 webpack 将一些库的内部路径转换成新的编译文件夹时，问题就出现了。webpack”，使该包成为外部包将阻止 webpack 这样做，并且该包将能够正常地从 node_modules 恢复其路径。

现在，要在生产模式下解决这一点更具体，问题是我们现在的外部包不会被添加到”。webpack "文件夹中，在可执行文件生成过程结束时不会被添加到 asar 文件中。

所以我们手动添加这些文件，首先安装 [copy-webpack-plugin](https://www.npmjs.com/package/copy-webpack-plugin) ，然后将以下代码添加到 main.webpack.js:

```
const CopyPlugin = require('copy-webpack-plugin');*module*.*exports* = {
  ... plugins: [
    **new** CopyPlugin({ patterns: [ { from: 'node_modules/@thiagoelg/node-printer/lib/', to: 'node_modules/@thiagoelg/node-printer/lib/', }, { from: 'node_modules/@thiagoelg/node-printer/build/', to: 'node_modules/@thiagoelg/node-printer/build/', }, { from: 'node_modules/@thiagoelg/node-             printer/package.json', to: 'node_modules/@thiagoelg/node-printer/package.json',
          }, ], }), ],
...}
```

在一系列的尝试和失败中，我发现只需要将“lib/”、“build/”和“package.json”复制到最终的构建中，我想这是有意义的。

现在，我希望您可以运行 make 脚本，然后执行应用程序，不会看到任何错误。

就这些，谢谢你给我一些时间。
*PS:这是我的第二个帖子，我为任何愚蠢的事情道歉(在第三个帖子里，我会停止给那个借口)*