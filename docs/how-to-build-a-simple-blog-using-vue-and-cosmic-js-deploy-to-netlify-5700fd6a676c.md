# 如何使用 Vue 和 Cosmic JS + Deploy to Netlify 构建一个简单的博客

> 原文：<https://levelup.gitconnected.com/how-to-build-a-simple-blog-using-vue-and-cosmic-js-deploy-to-netlify-5700fd6a676c>

![](img/75d1580cecd8ddfac48e4fec0e086e1e.png)

文章最初出现在[宇宙 JS 开发者文章](https://cosmicjs.com/articles/how-to-build-a-simple-blog-using-vue-cosmic-js-and-deploy-to-netlify)中，作者 [Jazib Sawar](https://cosmicjs.com/jazibsawar) 。

在本教程中，我将向您展示如何使用 [Vue](https://vuejs.org/) 、Vuex、 [Cosmic JS](https://cosmicjs.com/) 创建一个简单但令人惊叹的博客，并部署到 [Netlify](http://netlify.com/) 。让我们开始吧。

# TL；速度三角形定位法(dead reckoning)

[下载 GitHub 回购。](https://github.com/cosmicjs/vue-blog-cosmicjs)
[查看演示。](https://simple-vue-blog.cosmicapp.co/)

# 先决条件

在启动之前，您需要安装 NodeJS 和 npm。通过在终端中键入`node -v`和`npm -v`来确保您已经拥有它们。

# 开始使用:

在这个应用程序中，我们使用 [Snipcart Vue 博客演示](https://github.com/snipcart/vue-blog-demo)作为起点。派生并克隆这个存储库，我们稍后将研究它。首先，我们将在宇宙 JS 中设置桶。

**设置铲斗:**

要创建存储桶，请登录 [Cosmic JS](http://cosmicjs.com/) 并点击“添加新存储桶”。

![](img/37dba4982b90402f3e6330b053659ed5.png)

选择从头开始。

![](img/7f80dff4b5f38e6d2580a7037ed82a5a.png)

**导入桶数据**

下载这个 [bucket.json](https://raw.githubusercontent.com/jazibsawar/vue-blog-cosmicjs/backend-cosmicjs/bucket.json) 文件。在`Settings > Import / Export Data`中，点击“添加导入文件”，选择下载的文件。它将导入所有对象类型和所需数据。

![](img/80af9307ae00fb80c3575054cba67b7c.png)

现在我们将致力于应用程序。

# 整合宇宙 JS

铲斗应该完全设置好。现在，我们将把 Cosmic JS 后端集成到分叉的[https://github.com/snipcart/vue-blog-demo](https://github.com/snipcart/vue-blog-demo)存储库中。首先，用这个替换`package.json`:

在终端打开文件夹，做`npm install`。完成之后，我们需要设置 Cosmic JS 配置，以便与 API 进行通信。为此，我们将在`/src/config/`文件夹中创建一个文件`index.js`。

之后分别替换`/config/`文件夹中`index.js`和`prod.env.js`的内容。

`index.js`

`prod.env.js`

这就完成了宇宙 JS 配置。我们需要在项目的根目录下创建一个`.env`文件。它会将所有变量加载到 process.env 中。

**更改 Vue 资源插件以集成 Cosmic JS API**

我们的应用程序就快完成了。我们只需要做一些小的调整，以使它正常工作。

用以下代码替换代码`src/main.js`。我们正在改变一个 API 端点，以便调用 Cosmic JS 后端。

用下面的代码替换`src/plugins/resource/index.js`，我会解释我们在这里做什么。

在这次修改中，我们基本上导入了之前创建的配置，并将 API 请求的端点 URI 更改为 Cosmic JS。

博客供稿

替换`src/resources/BlogFeed.js`以便将来自 Cosmic JS API 的数据映射到博客提要。我们只需要正确地进行映射，因为前端代码独立于 API 调用。

**博客帖子**

同样，更换`src/resources/BlogPost.js`。它将使单一职位的工作完美。

**定稿**

如果你在终端里做`npm run dev`就可以了。但是在我们开始部署之前，我们需要完成一些事情。替换`static/api/blog.json`中的博客标题、作者标签和帖子标签。该字符串将在应用程序中使用。

由于这个博客应用程序是静态的，如果您想将它部署到 Cosmic JS 主机，您需要创建一个简单的 Node.js Express 服务器。在应用程序的根目录下创建`server.js`,并将下面的代码复制到该文件中。

一切都结束了😃。另外，我增加了`TitleComponent`来改变`document.title`。你可以在我的[库](https://github.com/jazibsawar/vue-blog-cosmicjs)里查看代码。

**部署**

现在我将展示如何在 [Cosmic JS](https://cosmicjs.com) 上部署这个应用程序，并使用 Netlify 的免费主机。您需要将这个应用程序代码推送到 GitHub 上的存储库中。

**部署在 Cosmic JS 主机上**

进入 [Cosmic JS](https://cosmicjs.com) dashboard 中你的桶，点击设置- >部署 Web App。

![](img/83eb6ba540ce22a9cc6a555d73e6a5c9.png)

在新窗口中，输入您的存储库 URL 和分支名称。添加完所有需要的配置后，点击“部署 web 应用程序”。它将被部署，成功部署后您将收到一封电子邮件。你不需要像在 Cosmic JS 托管中那样在这里输入任何环境变量，因为一切都已经为我们做好了。

![](img/520208558f09f7d159d58708550b87e8.png)

**在网络上部署**

在 Netlify 上注册，点击“来自 Git 的新站点”。请在那里选择 Git 提供者。就我而言，我将使用 Github。

![](img/e79c0e6b73c6f51b5a2da04cfaefd256.png)

成功认证后，您的所有存储库都将显示出来。选择正确的存储库。

![](img/68cc4c6ae3e129c68a0c74b8c3af56c1.png)

现在您需要选择您的分支并提供一些配置，以便使它正常工作。选择分支成功后，在`build`命令中输入`'npm run build'`，在`publish`目录中输入`'dist'`。最后，点击`'Show advanced'`按钮。

![](img/71669b5122481eb51f7102dc7134fa42.png)

点击前进按钮后，需要添加`3 environment variables`。这里我们必须手动完成，但是 Cosmic JS 主机会自动为我们完成。

在那里加上`COSMIC_BUCKET`、`COSMIC_READ_KEY`和`COSMIC_WRITE_KEY`。您可以从`Settings > Basic Settings`中获得这些值。只需将值粘贴在那里，然后按下`Deploy Site`。它将在几分钟内部署！

![](img/a0ae4b4a47902579d30fc1a1c2ce39ff.png)

# 结论

这是一个使用 Vue、Vuex 和 [Cosmic JS](https://cosmicjs.com) 构建的简单博客应用。我们还学习了如何将这个应用程序部署到 Netlify 以及 Cosmic JS 主机。我希望你喜欢这个教程，并会对你有所帮助。干杯！