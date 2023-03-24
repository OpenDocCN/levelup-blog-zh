# 用 Docker 处理多种环境

> 原文：<https://levelup.gitconnected.com/handling-multiple-environments-in-react-with-docker-543762989783>

本文讨论了使用 Docker (Bonus:和 Kubernetes)在 React 应用程序中处理不同环境的四种方式(比如用于开发、阶段和生产的 API 端点)。

![](img/8d94ac3dbfe3cef048817d790a831a8d.png)

图片由 [Artem Sapegin](https://unsplash.com/photos/ZMraoOybTLQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

我是一个热情的 react 开发人员和 devops 爱好者。我们公司的许多团队将 React 与 Docker 和 Docker Compose 结合用于本地开发，将 Kubernetes 用于部署应用程序。

在大多数项目中，我们有几个环境(生产、登台和本地开发)，在一些项目中，甚至每个特性或分支一个环境。许多 React 开发人员在某种程度上面临着如何管理这些环境并使他们的 React 应用程序指向正确的 API 端点的挑战。

来自后端开发的我想:嗯，这很容易，我使用环境变量，这总是很顺利…不幸的是，它并不那么容易。在这篇文章中，我将向你展示我在试验和与其他开发者交流时遇到的四个解决方案。但是首先我要谈一点关于…

# 背景 React 中的环境变量有什么问题？

环境变量显然是在应用程序的环境中定义的变量，并且可以用于相应地配置应用程序，例如，将前端指向正确的 API 端点。

对于 React 应用程序(或任何 JavaScript 应用程序)，环境是…用户的浏览器。不幸的是，我们不能要求每个用户在他的本地机器上设置一些环境变量，以便我们的网站工作；)因此，webpack 为我们提供了一个方便的特性，可以使用 [DefinePlugin](https://webpack.js.org/plugins/define-plugin/) 来设置它们(如果您使用 create-react-app，那么您的 webpack 配置默认包含这个特性)。Create React App 通过在变量前添加`REACT_APP_`来识别我们想要传递给浏览器的变量。

当我们运行`npm run build`时，webpack 将此时的环境变量**硬编码到 JavaScript 代码包中。这也意味着您在**命令`npm run build`之后设置的任何环境变量都不会有任何影响，因为旧的已经被硬编码，新的不会被解释。这要求我们必须意识到在哪个点运行`npm run build`，以及在哪个上下文中定义环境变量。我尝试了以下选项:

# 选项 1:在服务器启动时运行 npm 运行构建

在该选项中，我们存储了一些默认设置(例如在`src/config.js)`中),并可选地用环境变量覆盖它们:

```
**const** defaultSettings = {
  REACT_APP_API_URL: '*http://localhost:3001'*,
  REACT_APP_*GA_PROPERTY: 'track me',*
};**const** settings = {defaultSettings, ...process.env};**export default** settings;
```

重要提示:你必须给你的变量加上前缀`REACT_APP`，否则 Webpack 会忽略它们。

现在，我们在启动 web 服务器之前运行`npm run build`。我们的 Docker 命令可能如下所示:

`docker run my-image sh -c "npm run build && serve build"`

该解决方案的优势:

*   它简单而直观:你可以以任何你想要的方式设置你的环境变量，例如，将它们传递给 Docker ( `docker run -e REACT_APP_API_URL=http://localhost:3001 my-image sh -c "npm run build && serve build"`)或者在 Kubernetes 中将它们设置为环境变量。

缺点:

*   缩放时的持续时间:如果您启动同一个映像的多个容器，您将多次运行(非常昂贵和冗长的)构建过程，即使所有容器都使用完全相同的代码。
*   缩放时的会话处理:运行`npm run build`时，webpack 会在生成的文件名中附加一个哈希(例如`main1234.js`)，帮助浏览器正确刷新缓存。如果您用不同的代码启动容器，那么即使代码的其余部分是相同的，它也可能包含不同的散列。如果您的负载平衡器引导流量循环，您的`index.html`可能会指向`main.js`，它不在这个容器中，而是在另一个容器中。提示:启用[会话粘性](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)来解决这个问题。
*   阻塞的资源:`npm run build`命令在 CPU 和内存方面非常消耗资源。尽管提供现成的 html 和 js 文件非常便宜。如果在运行`npm run build`时使用小实例，它们可能没有足够的资源而退出，即使它们足以提供内容。在 Kubernetes 中，您的容器可能需要很高的资源限制并永久阻塞资源，这实际上只在启动容器的前几分钟需要。

好吧，这种解决方案很简单，但是在扩展或工作资源效率方面有明显的缺点。显然，对相同的代码只运行一次`npm run build`会很好…

# 选项 2:当 Docker 构建运行时构建 JS

好的，所以我们在 docker 文件中包含了`npm run build`,它将只对每个图像运行一次，而与我从它开始运行多少个容器无关，对吗？对！但是请记住:webpack 将此时的环境变量**硬编码到 JavaScript 代码包中。你的图像将永远指向此刻所指向的 API。这意味着您不能在不同的环境中重用相同的映像，即使它实际上包含相同的代码。**

该解决方案的优势:

*   缩放时没有问题:你的容器将有相同的`mainX.js`文件，你只需要在构建映像时等待一次漫长的构建过程。
*   资源优化:您可以在低资源限制的情况下运行现成的映像，并且只确保在您的`docker build`运行时有足够的资源(例如，在您的 CI 管道中)。

缺点:

*   您不能在不同的环境中使用相同的映像，即使它们包含完全相同的代码。
*   管道中的多个设置:如果您以自动化的方式(CI 管道)创建 Docker 映像，那么您需要使用不同的环境变量来运行构建阶段，以便为每个环境获得不同的映像。如果环境的数量非常大(例如，当每个特性或分支运行一个 Kubernetes 名称空间时)，这可能会非常不方便。

好的，所以理想情况下，我们的解决方案将独立于环境构建相同的 Docker 映像，但仍然将`npm run build`作为`docker build`阶段的一部分运行。这让我们想到:

# 选项 3:使用相对 API URL

如果我们的 API-url 是相对的(例如`/api`)，我们的解决方案将满足这一要求。例如，您可以通过以下方式之一完成此操作:

1.  让 Kubernetes 负载均衡器根据路径将流量导向前端或后端容器，这对于使用[Ingres](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource)来说非常容易。
2.  只有一个包含前端和后端的 Docker 映像，并在内部进行路由。我不会进一步考虑这个解决方案，因为我想保持一种微服务方法，并且在前端和后端使用完全不同的技术。

使用 Kubernetes 负载平衡的优势:

*   它简单、直观，开箱即用。听起来很完美，但是…

缺点:

*   除非您想花时间在本地运行类似的负载平衡(例如，通过 Minicube 或 nginx)，否则您不能将相同的设置用于本地开发。但是这将意味着把只需要本地开发的功能放到您的项目中。
*   这个解决方案只适用于 API URL。如果您有其他特定于环境的设置(例如，Google Analytics Id，一些错误跟踪工具，如 Sentry)，这种方法不能为这些提供解决方案。

因此，理想情况下，我们的解决方案将构建相同的 Docker 映像，将`npm run build`作为`docker build`阶段的一部分运行，并使用非 URL 的设置。我们开始吧:

# 选项 4:使用单独的配置文件

在这个过程中，我们根本不使用环境变量，而是将配置从 webpack 构建的资源中移出。实现这一点的一种方法是提供一个单独的文件，在 React 启动之前加载该文件。您可以在文件中指定您的设置(例如`src/public/config.js`):

```
*window.env.API_URL=http://my-server.com/api/v1/
window.env.GA_PROPERTY=TRACK_ME*
```

然后，在 React 加载之前，您将该文件加载到您的`<HEAD>`中的`public/index.html`中:

```
<head>
  <title>I like this</title>
  <script src="%PUBLIC_URL%/config.js"></script>
```

现在，您可以在 React 代码中直接访问这些设置。如果你不喜欢一直在 React 中直接访问`window`(像我一样)，你可以在`src/config.js`这样的中央位置将设置“翻译”成可导入的常量:

```
**const** settings = {
  API_URL: *window.env.API_URL*,
  *GA_PROPERTY: window.env.GA_PROPERTY,*
};

**export default** settings;
```

我通常将本地开发所需的设置放在一个默认文件`src/public/config`中。在不同的环境中，我将包含环境设置的文件版本挂载到容器中。如果您使用 plain Docker，它可能看起来像这样:

```
docker run -v %{PWD}/config-prod.js:/app/build/config.js my-image serve build
```

如果您使用 Kubernetes，我建议将文件的内容放在一个 ConfigMap 中并挂载它。

该解决方案的优势:

*   您只需要运行`npm run build`一次，就可以在所有环境中使用相同的映像。
*   您可以用这种方式处理所有设置，而不仅仅是 API URL。

缺点:

*   需要再加载一个外部文件(虽然我不认为这对站点加载时间有明显的影响)。
*   不使用环境变量，而是使用一个挂载的配置文件，这可能看起来不熟悉(尽管在 Kubernetes 中这很常见)。

# 结论——我应该使用哪个选项？

到目前为止，我尝试过的所有上述选项都有优点和缺点。目前在我的项目中我更喜欢选项 4，因为在 Kubernetes 的一个卷中挂载一个 ConfigMap 对我来说没问题，并且额外加载文件的成本相当低。然而，我不认为这是完美的，我很好奇你是如何处理的。您在项目中使用哪种解决方案？

[![](img/ff5028ba5a0041d2d76d2a155f00f05e.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/webpack) [## 学习 Webpack -最佳 Webpack 教程(2019) | gitconnected

### 8 大 Webpack 教程-免费学习 Webpack。课程由开发人员提交并投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/webpack)