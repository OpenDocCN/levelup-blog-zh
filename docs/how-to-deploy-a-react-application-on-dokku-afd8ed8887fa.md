# 如何在 Dokku 上部署 React 应用程序

> 原文：<https://levelup.gitconnected.com/how-to-deploy-a-react-application-on-dokku-afd8ed8887fa>

## 和杜库一起主持你的 React SPA

![](img/e507fa0d86fd76722bf7336c857575b6.png)

照片由[托马斯·凯利](https://unsplash.com/@thkelley?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

[Dokku](https://dokku.com/) 已经成为市场上最受欢迎的 PaaS ( [平台即服务](https://en.wikipedia.org/wiki/Platform_as_a_service))之一。特别是，Dokku 是一个可扩展的、开源的 PaaS，它运行在单个服务器上。

Dokku 使你能够通过一个简单的`git push`命令，经由 [Dockerfile](https://docs.docker.com/engine/reference/builder/) 或者通过用 [Buildpacks](https://buildpacks.io/) 自动检测语言来动态构建应用。具体来说，它基于应用程序的构建映像创建了一个 Docker 容器。然后，它启动容器，启动您的应用程序。

现在让我们了解一下在 Dokku 上部署 React 应用程序需要做些什么！

# 在 Dokku 上部署 React 应用程序

在 Dokku 上部署 React SPA ( [单页面应用程序](https://en.wikipedia.org/wiki/Single-page_application))需要五个简单的步骤。让我们深入研究一下。

## 1.设置 Dokku

你可以在这里学习如何设置 Dokku 服务器[。然后，按照这个](http://dokku.viewdocs.io/dokku/getting-started/installation/)指南来学习如何部署你的应用程序。初始化您的 Dokku 机器后，启动以下命令创建一个新的 Dokku 应用程序:

```
dokku apps:create <YOUR_REACT_APP_NAME>
```

将`<YOUR_REACT_APP_NAME>`替换为您希望在 Dokku 上为 React 应用程序指定的名称。比如，姑且称之为`my-react-app`:

```
dokku apps:create my-react-app
```

## 2.添加 Dokku Git 遥控器

要在 Dokku 上部署 React 应用程序，您必须启动一个涉及特定[遥控器](https://git-scm.com/docs/git-remote)的`git push`命令。在终端中输入 React 项目，并使用以下命令添加 Dokku git remote:

```
git remote add <REMOTE_NAME> dokku@<YOUR_SERVER_IP>:<YOUR_REACT_APP_NAME>
```

用你想给 git 遥控器起的名字替换`<REMOTE_NAME>`。比如可能是`dokku`、`production`等。另外，用您的服务器/VPS 的域或 IP 地址替换`<YOUR_SERVER_IP>`。同样，用您在第一步中使用的 Dokku 应用程序的名称替换`<YOUR_REACT_APP_NAME>`。

在本例中，该命令将如下所示:

```
git remote add production dokku@app.my-domain.com:my-react-app
```

这样，您就添加了 Dokku git 遥控器。您现在可以通过`git push`命令部署您的应用程序。但是首先，您需要一个应用服务器！

## 3 建立一个 Node.js 服务器

现在，您应该设置一个服务器。这是因为任何 React 应用程序都需要通过应用服务器提供服务。只需几行代码就可以配置 Node.js Express 服务器。

首先，确保使用以下命令将`[express](https://www.npmjs.com/package/express)`添加到项目的依赖项中:

```
npm i express
```

然后，在 React 项目的根文件夹中创建一个`server.js`文件，并将其初始化，如下所示:

如您所见，该文件充当 Node.js Express 服务器。特别是，它初始化一个 Express 服务器，并使用它来服务于`/build/index.html`文件。这是 React 构建过程通过`npm run build`命令生成的索引文件。

`*`限定符指定服务器将使用`index.html`文件响应收到的任何 [HTTP GET 请求](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/GET)。这正是让 SPA 前端路由系统工作所需要做的。

请记住，您可以定制这个 Express 服务器来处理 CSP ( [内容安全策略](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP))或其他带有`[helmet](https://www.npmjs.com/package/helmet)`的 HTTP 头。

## 4.创建 Procfile

`[Procfile](https://dokku.com/docs/deployment/builders/dockerfiles/#procfiles-and-multiple-processes)`允许您指定由 Dokku 执行的运行命令来启动您的应用程序。详细地说，`Procfile`将在部署后从 Dokku 创建的 Docker 映像中提取。然后包含在`Procfile`中的命令将被传递给`[docker run](https://docs.docker.com/engine/reference/run/)`命令来启动您的应用程序。

创建一个`Procfile`文件并把它放在`/app`或者你的`[Dockerfile](https://dokku.com/docs/deployment/builders/dockerfiles/)`中定义为`WORKDIR`的文件夹中。默认情况下，存储库的根文件夹将用作文档根。因此，将`Procfile`放在你的应用程序的根目录下应该是可行的。

然后，将其初始化如下:

该命令将在每个 Dokku 部署结束时使用，以启动 Node.js Express 服务器并为 React SPA 提供服务。

## 5.部署到杜库

现在，您已经准备好在 Dokku 上部署 React 应用程序了。您所要做的就是[提交更改](https://github.com/git-guides/git-commit)并启动以下命令:

```
git push <REMOTE_NAME>
```

用您在步骤 2 中给 Dokku Git 遥控器的名称替换`<REMOTE_NAME>`。在本例中，该命令变为:

```
git push production 
```

等待 Dokku 上的部署过程结束。首先，Dokku 将通过运行`npm run build`构建您的 React 应用程序。然后，它将启动包含在`Procfile`中的命令，并启动您的应用程序。

现在，您应该能够在浏览器中通过服务器的 IP 地址/域名访问 React 应用程序。

恭喜你。您刚刚学习了如何在 Dokku 上部署 React 应用程序！

您可能也有兴趣了解如何在 Apache 服务器上部署和托管 React 应用程序。

[](https://codeburst.io/deploying-and-hosting-a-react-app-on-an-apache-server-b7121dffb07a) [## 在 Apache 服务器上部署和托管 React 应用程序

### 你甚至可以在共享主机上运行 React 应用程序！

codeburst.io](https://codeburst.io/deploying-and-hosting-a-react-app-on-an-apache-server-b7121dffb07a) 

# 结论

在本文中，您了解了在 Dokku 上部署 React 应用程序所需的一切。Dokku 是最受欢迎的 PaaS 之一，它允许您在几分钟内部署一个应用程序。在这里，您看到了如何配置它，以及您需要在 React 应用程序中做些什么来使它可在 Dokku 上部署。

感谢阅读！我希望这篇文章对你有所帮助。请随意留下任何问题、评论或建议。