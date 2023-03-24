# 先认识纱线再学习 Node.js

> 原文：<https://levelup.gitconnected.com/know-yarn-before-learning-node-js-bf39a50fb27f>

![](img/d6d8f9222b899b71984f6b79348e2820.png)

图片由[塔拉·埃文斯](https://unsplash.com/@taradee)在 [Unsplash](https://unsplash.com/photos/IcvR0jFbsz0) 拍摄

我想学 *Node.js* 因为我听说把它设置成服务器很简单。和其他人一样，我首先在谷歌上搜索“ *Node.js Tutorial* ”。坦率地说，我不得不说找到的教程材料都是一流的。

然而，他们漏提了一个如今在社区中非常流行的东西: [*纱*](https://yarnpkg.com/lang/en/) 。

# 什么是纱线？

它是一个我们可以用来做很多事情的依赖管理工具，比如 *Node.js* 、 *React* 等等。

它的前身(也可能是对手)是 [*npm*](https://www.npmjs.com/) 。他们应该能够做同样的事情。据说 yarn 更可靠、更快，但是 npm 已经赶上了。

# 我还能用 npm 吗？

是的，你可以。它还在工作。事实上，我看到的所有教程仍然把它称为下载第三方软件包模块的工具。

尽管如此，作为一个新用户，在学习了 *yarn* 之后，我更喜欢用它而不是 *npm* 来建立一个新项目。

# 为什么我更喜欢纱线？

我意识到要正确使用 *npm* ，我们应该相应地设置 *package.json* 文件。这可以通过使用命令`npm init`来完成。(注:并不是所有的 *Node.js* 教程都这么告诉你)。

## package.json 的使用

通过设置 *package.json* ，您使用 *npm* 安装的后续模块将被记录到 *package.json* 及其锁文件中。这个文件可以提交给 git。在未来，用户只需要获得这个文件并键入`npm install`就可以获得为你的应用程序安装的所有依赖模块。

当从 *package.json* 安装一个模块时，它会把它们安装到文件夹 *node_modules* 中。如果该文件夹不存在，将会创建它。

因此，如果您忘记执行`npm init`并立即执行`npm install <package>`，您将无法正确记录您的依赖模块，并且它不会存储到 *node_modules* 文件夹中。

## 纱来救援。

对于*纱线*，不需要显式`yarn init`(相当于`npm init` ) 。您只需输入`yarn add <package>`(相当于`npm install <package>`)，就会自动创建 *package.json* 和 *node_modules* 文件夹。

以后如果你想在 *package.json* 中设置更多的字段，那么你只需要`yarn init`。

> 提示:如果你喜欢在 GitHub 上存储你的项目，记得在。gitignore 文件，以避免存储所有依赖包。在您获取一个干净的 git 项目之后，您只需要键入`*yarn install*`(相当于`*npm install*`)来获取您所有的依赖项*。*

## 纱线比 npm 快

根据我的经验，*纱*的下载速度似乎比 *npm* 快。

*   *纱*花了大约 0.85 秒用于`yarn add upper-case`
*   *npm* 用了大约 1.75 秒用于`npm install upper-case`(如果 *package.json 存在)*
*   *npm* 对于`npm install upper-case`(如果没有 *package.json)* 大约用了 3.75s

# 如何获得纱线？

你可以使用 npm 来安装纱线😅。仅仅

`npm install -g yarn`

要使用*纱线*，只需在命令上将所有 *npm* 关键字替换为*纱线*。在大多数情况下，它们应该是相同的。

感谢阅读。你可以在这里查看我的其他话题。

关注我关于[](https://medium.com/@elye.project)**[*Twitter*](https://twitter.com/elye_project)*[*脸书*](https://www.facebook.com/elyeproj/) 或 [*Reddit*](https://www.reddit.com/user/elyeproj/) 关于移动开发等相关话题的小技巧和学习。~Elye~***