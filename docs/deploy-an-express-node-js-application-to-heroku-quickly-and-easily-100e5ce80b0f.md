# 快速轻松地将 Express Node.js 应用程序部署到 Heroku

> 原文：<https://levelup.gitconnected.com/deploy-an-express-node-js-application-to-heroku-quickly-and-easily-100e5ce80b0f>

使用 Heroku，只需几个步骤就可以将 Express Node.js 应用程序部署到生产环境中。在这篇文章中，我将向您展示如何将 Express Node.js 应用程序部署到 Heroku 的分步指南。

![](img/a7945a619e81a5b065060bcc5b248e57.png)

要快速入门，您可以使用我的回购模板[simeg/express-heroku-example](https://github.com/simeg/express-heroku-example)。

# 什么是 Heroku？

Heroku 是一个平台即服务(PaaS)，不应该与服务即服务(SaaS)混淆。它提供了一个爱好计划，你可以免费部署你的应用程序，但有一些限制。

对于我所有的爱好网站项目，我都使用 Heroku。我创造了像[数独-js](https://github.com/simeg/sudoku-js) 和[不可能-井字游戏](https://github.com/simeg/impossible-tic-tac-toe)这样的东西。有关应用程序的链接，请参见“关于”部分。

# 准备

首先，安装 Heroku CLI。如果你在苹果电脑上运行

```
$ brew tap heroku/brew && brew install heroku
```

否则，前往 Heroku 的网站。

# 将 Node.js 应用程序部署到 Heroku

现在您已经安装了 CLI，我们可以开始编写一些代码了。我们将使用一个 HTTP Express 服务器的最小例子。

## Node.js 应用程序

用`npm init`引导 Node.js 应用程序。然后用`npm i --save express`添加 Express 作为依赖。

现在让我们看看我们在`index.js`的 slim Express 服务器。

Slim Express 服务器

你可以在这里阅读更多关于[快递](https://expressjs.com/)。

这个 HTTP 服务器很简单。它有一个返回`200`和文本`Hello World!`的`GET`端点。

现在我们已经准备好了服务器，我们需要一些额外的东西来将它部署到 Heroku。首先我们需要一个`Procfile`。

Procfile

这是 Heroku 在启动应用程序时读取的文件。如您所见，文件运行`npm start`，因此我们也需要创建它。我们把它加到`package.json`上。

package.json

另外，请注意`engines`部分。这是为了让 Heroku 知道使用什么运行时来运行您的应用程序。你可以在这个网站上看到 Heroku 支持哪些 Node.js 版本。

## 部署到 Heroku

有几种方法可以部署到 Heroku。我们将使用 git，这是最简单的方法。

现在所有的代码都写好了，我们需要提交它。

```
$ git add .
$ git commit -m "Initial commit"
```

然后我们需要在 Heroku 上创建一个应用程序。

```
$ heroku create
```

这个命令还添加了一个名为`heroku`的 git 遥控器。这个遥控器是我们部署应用程序的地方。让我们现在就做吧！

```
$ git push heroku main
```

在这一点上，Heroku 将试图找出使用什么构建包。实际上，您正在部署什么类型的应用程序？因为我们在根目录中有一个`package.json`文件，它知道这是一个 Node.js 应用程序。

当命令完成时，它将输出一个 URL。让我们打开它！

```
...
https://thawing-beyond-32509.herokuapp.com/ deployed to Heroku
...
```

我们可以在浏览器中看到`Hello World!`。易如反掌！

现在您可以检查应用程序的日志了。

```
$ heroku logs --tail
```

# 结论

现在您已经知道如何将 Node.js 应用程序部署到 Heroku。Heroku 提供了很好的工具来快速启动并运行一些东西。但这仅仅是开始！Express 允许您构建复杂的 web 应用程序。借助 Heroku，您可以快速将它们部署到生产环境中。

查看 Heroku 的[node . js 开发的最佳实践](https://devcenter.heroku.com/articles/node-best-practices)以获得提示和技巧。而他们关于 Node.js 的[页面](https://devcenter.heroku.com/categories/nodejs-support)也是有用的。

*在*[*Twitter*](https://twitter.com/prplcode)*，*[*LinkedIn*](https://linkedin.com/in/simeg)*，或者* [*GitHub*](https://github.com/simeg)

*最初发布于*[*prplcode . dev*](https://prplcode.dev/)