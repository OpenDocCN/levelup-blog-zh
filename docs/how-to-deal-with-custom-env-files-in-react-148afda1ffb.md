# 如何应对海关？React 中的环境文件

> 原文：<https://levelup.gitconnected.com/how-to-deal-with-custom-env-files-in-react-148afda1ffb>

## 向 Create-React-App 应用程序添加定制的`.env.staging`文件

![](img/d8aa46d4ea820d17d83de09f6ad0aa40.png)

照片由[劳塔罗·安德烈亚尼](https://unsplash.com/@lautaroandreani?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在开发 React 应用程序时，一个常见的场景是拥有三个[部署环境](https://en.wikipedia.org/wiki/Deployment_environment):

*   本地:用于本地发展。
*   [Staging](https://en.wikipedia.org/wiki/Deployment_environment#Staging) :生产环境的在线镜像。
*   [生产](https://en.wikipedia.org/wiki/Deployment_environment#Production):为最终用户服务的实时应用程序。

您的架构还可能有其他部署环境，比如测试、开发或生产前环境。

这意味着您的应用程序可能需要至少三个不同的`.env`文件。然而， [Create-React-App](https://create-react-app.dev/docs/getting-started) 应用程序只支持一组有限的预定义的`.env`文件。

现在让我们学习如何为您的 React 应用程序设置多个定制的`.env`文件。

# 设置多个自定义。创建-反应-应用程序中的 env 文件

创建-反应-应用程序使用`[react-scripts](https://www.npmjs.com/package/react-scripts)`。如果你不熟悉`.evn`文件如何在`[react-scripts](https://www.npmjs.com/package/react-scripts)`中工作，看看[“什么其他的。env 文件都可以用？”](https://create-react-app.dev/docs/adding-custom-environment-variables/#what-other-env-files-can-be-used)创建 React 应用程序文档的一节。在那里，您可以了解到`.env`文件`react-scripts`支持什么以及如何使用它们。

现在，让我们学习如何在 React 应用程序中设置一个定制的`.env.staging`文件。注意，下面的方法可以很容易地扩展到任何其他定制的`.env`文件。此外，这个解决方案允许你有多个自定义的`.env`文件。

## 1.入门指南

Create-React-App 应用程序用`package.json`文件中的以下`scripts`部分初始化:

```
"scripts": {
  "start": "react-scripts start",
  "build": "react-scripts build",
  "test": "react-scripts test",
  "eject": "react-scripts eject"
}
```

`react-scripts`仅支持一组特定的`.env`文件。这些`.env`文件是在构建时部署应用程序时使用的。

因此，如果您希望您的带有定制`.env`文件的 Create-React-App 应用程序正确构建，您需要定制`build`命令。具体来说，您需要用一个定制的构建脚本来替换它。这样，当涉及到`.env`文件时，您可以克服`react-scripts`的限制。

## 2.创建自定义构建脚本

首先，在项目的根目录下创建一个`build.sh`文件。按如下方式初始化它:

这里，当`ENV`环境变量等于“staging”时，删除`.env.local`并用`.env.staging`代替`.env.production`。这样，`react-scripts build`会将自定义的`.env.staging`文件视为`.env.production`，并在构建应用程序时使用它。

注意，您可以简单地通过重复`if ... fi`逻辑并改变测试条件，将这种方法扩展到几个环境变量。

现在，你必须更新`package.json`，让它调用`build`上的自定义`build.sh`脚本。您可以通过以下方式实现这一点:

```
"scripts": {
  // ...
  "build": "bash build.sh",
  // ...
}
```

当您运行`npm run build`时，将会启动`build.sh`文件。由于`buld.sh`包含`react-script build`命令，应用程序将像以前一样构建。但是具有在`react-script`命令之前定义的`.env`文件管理逻辑。

## 3.在部署服务器中设置 ENV 环境变量

此时，您所要做的就是在您的临时部署服务器中设置`ENV`环境变量。在 Linux 上，您可以使用以下命令来实现:

```
export ENV=staging
```

瞧啊！您刚刚学习了如何在 Create-React-App React 应用程序中使用定制的`.env`文件。请注意，您还可以在 Next.js 中[配置自定义](/how-to-deal-with-custom-env-files-in-next-js-98d8c5b7899a) `[.env](/how-to-deal-with-custom-env-files-in-next-js-98d8c5b7899a)` [文件。](/how-to-deal-with-custom-env-files-in-next-js-98d8c5b7899a)

[](/how-to-deal-with-custom-env-files-in-next-js-98d8c5b7899a) [## 如何应对海关？Next.js 中的 env 文件

### 将自定义. env.staging 文件添加到 Create-Next-App 应用程序中

levelup.gitconnected.com](/how-to-deal-with-custom-env-files-in-next-js-98d8c5b7899a) 

# 结论

在本文中，您了解了如何配置 React 应用程序来支持多个定制的`.env`文件。当涉及到`.env`文件时，Create-React-App 应用程序有一些限制。因此，如果您有更多的部署环境，而不是那些由`react-scripts`所期望的，那么您需要实现定制逻辑来启用对定制`.env`文件的支持。在这里，您学会了如何做到这一点。

感谢阅读！我希望这篇文章对你有所帮助。请随意留下任何问题、评论或建议。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)