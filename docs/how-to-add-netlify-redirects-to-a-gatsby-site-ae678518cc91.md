# 如何将 Netlify 重定向添加到 Gatsby 站点

> 原文：<https://levelup.gitconnected.com/how-to-add-netlify-redirects-to-a-gatsby-site-ae678518cc91>

![](img/98fbb6347194823086066ec8e5c16bb7.png)

照片由[布伦丹·丘奇](https://unsplash.com/@bdchu614?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

用 Netlify 托管你的 Gatsby 站点是一个完美的组合。使用这两者，整个开发工作流程变得非常顺畅。然而，例如，重定向还不能开箱即用，或者至少不能按照您期望的方式工作。因此，本文应该向您展示两种方法，如何向 Netlify 上托管的 Gatsby 站点添加重定向。

**选项 1**
通过`_redirects`文件添加重定向

**选项 2** 通过`createRedirect`动作添加重定向

你选择哪一个取决于你，无论你喜欢什么。然而，由于这两者在《盖茨比》中都不能开箱即用，我们首先必须安装一个名为 [gatsby-plugin-netlify](https://www.gatsbyjs.org/packages/gatsby-plugin-netlify/) 的插件。它是一个官方的 Gatsby 插件，将处理我们在构建过程中设置的重定向。您可以查看链接了解更多信息。要安装插件，使用以下命令:`npm install gatsby-plugin-netlify`

安装完成后，不要忘记将插件添加到您的`gatsby-config.js`文件中。只需将``gatsby-plugin-netlify``添加到你的插件阵列中。完成这些后，您就可以从其中一个选项开始了。

# 选项 1 —通过 _redirects 文件添加重定向

> 确保您已经安装了`gatsby-plugin-netlify` ( [参见此处](#de8f))

我们的第一个选择是通过一个`_redirects`文件添加重定向。如果您以前使用过 Netlify，那么您很可能对此很熟悉。这是用 Netlify 处理重定向的最常见方式。通常，您会将该文件添加到您的构建目录中。但是由于 Gatsby 是在构建过程中动态创建这个目录的，所以以前没有办法在其中放入任何东西。

为了绕过这个问题，我们可以使用 Gatsby 提供的`static`文件夹。在构建过程中，我们放入该文件夹的所有内容都将被复制到我们的构建目录中。因此，我们可以创建一个`_redirects`文件，将其放在`static`文件夹中，之前安装的插件将处理其他一切。因此，我们需要做的是:

1.  如果你还没有完成，在你的 Gatsby 站点的根目录下创建一个名为`static`的文件夹(所以直接在`src`文件夹旁边)。
2.  在`static`文件夹中，创建一个名为`_redirects`的文件
3.  在`_redirects`文件中，你可以写下你所有的重定向。这些重定向应该遵循 Netlify 使用的重定向语法。你可以在[网络游戏场](https://play.netlify.com/redirects)检查你的重定向是否正确。
4.  将您的项目部署到 Netlify 并享受您的重定向！

## 示例:

Netlify 上重定向的一个常见用例是将所有访问者从您的默认 Netlify 子域转发到您的自定义主域。如果您想这样做，您的`_redirects`文件应该是这样的:

# 选项 2-通过 createRedirect 操作添加重定向

> 确保你已经安装了`*gatsby-plugin-netlify*` ( [看这里](#de8f))

通常在 Gatsby 中，客户端重定向是在构建过程中通过`createRedirect`动作创建的。但是使用我们之前安装的插件，我们也可以使用相同的操作来创建服务器端重定向。因此，您可以使用相同的类似 Gatsby 的语法来创建 Netlify 重定向。

要开始创建重定向，我们需要做以下工作:

1.  导航到您的`gatsby-node.js`文件。这里的代码在构建过程中运行一次，因此是创建重定向的完美地方。
2.  通常，在创建页面时会创建重定向。所以我们必须先用`exports.createPages = ({ actions }) => {}`调用`gatsby-node.js`文件里面的`createPages`动作
3.  在`createPages`函数内部，我们可以销毁`actions`参数，从而访问`createRedirect`方法。
4.  通过使用`createRedirect`方法，我们最终可以创建重定向！

## 例子

听起来复杂的事情实际上非常简单。让我们坚持我们已经在选项 1 中使用的例子。在那里，我们创建了一个重定向，将所有访问者从我们的默认 Netlify 子域转发到我们的自定义主域。我们现在想对`createRedirect`动作做同样的事情。

如您所见，我们可以在`createPages`动作中访问`createRedirect`方法。`createRedirect`方法将一个具有以下属性的对象作为参数:

`fromPath`:从
`toPath`重定向的旧 URL:重定向到
`isPermanent`的新 URL:将状态代码设置为 301
`force`:一个特定于网络的属性，我们在这里需要它来创建域名别名重定向

如果你想更深入地了解一下，`createRedirect`动作还有几个属性，你可以在 Gatsby 文档中查看[。](https://www.gatsbyjs.org/docs/actions/#createRedirect)

总之，Gatsby 中的 Netlify 重定向还不能开箱即用，但无论如何都很容易实现。您可以通过一个`_redirects`文件选择类似 Netlify 的方式，或者坚持使用 Gatsby 方法，该方法利用了`gatsby-node.js`中的`createRedirect`动作。

我希望这个简短的教程对你有所帮助，你可以跟着做。如果您有任何进一步的问题或发现一些错误，请让我知道！