# 如何应对海关？Next.js 中的 env 文件

> 原文：<https://levelup.gitconnected.com/how-to-deal-with-custom-env-files-in-next-js-98d8c5b7899a>

## 向 Create-Next-App 应用程序添加自定义的`.env.staging`文件

![](img/9b2c502468103e80248abdaae2b91613.png)

照片由 [Tai Bui](https://unsplash.com/@agforl24?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在开发 Next.js 网站时，一个常见的场景是拥有三个[部署环境](https://en.wikipedia.org/wiki/Deployment_environment):

*   本地:用于本地发展。
*   [Staging](https://en.wikipedia.org/wiki/Deployment_environment#Staging) :制作网站的在线镜像。
*   [制作](https://en.wikipedia.org/wiki/Deployment_environment#Production):为最终用户服务的直播网站。

您的架构还可能有其他部署环境，比如测试、开发或生产前环境。

因此，您的 Next.js 应用程序可能需要至少三个不同的`.env`文件。然而，一个 [Create-Next-App](https://nextjs.org/docs/api-reference/create-next-app) 应用程序只支持一组有限的预定义的`.env`文件。

现在让我们学习如何为您的 Next.js 应用程序设置多个定制的`.env`文件。注意，您可以[配置自定义](/how-to-deal-with-custom-env-files-in-react-148afda1ffb) `[.env](/how-to-deal-with-custom-env-files-in-react-148afda1ffb)` [文件也在 React](/how-to-deal-with-custom-env-files-in-react-148afda1ffb) 中。

[](/how-to-deal-with-custom-env-files-in-react-148afda1ffb) [## 如何应对海关？React 中的环境文件

### 向 Create-React-App 应用程序添加自定义. env.staging 文件

levelup.gitconnected.com](/how-to-deal-with-custom-env-files-in-react-148afda1ffb) 

# 配置多个自定义。创建下一个应用程序应用程序中的 env 文件

让我们学习如何在您的 Next.js 应用程序中设置一个定制的`.env.staging`文件。您将看到的方法可以很容易地扩展到任何其他定制的`.env`文件。另外，这种方法允许您拥有多个定制的`.env`文件。

## 1.初始配置

使用`package.json`文件中的以下`scripts`部分初始化 Create-Next-App 应用程序:

```
"scripts": {
  "dev": "next dev",
  "build": "next build",
  "start": "next start"
}
```

`[next](https://nextjs.org/docs/basic-features/environment-variables)` [只支持一组特定的](https://nextjs.org/docs/basic-features/environment-variables) `[.env](https://nextjs.org/docs/basic-features/environment-variables)` [文件](https://nextjs.org/docs/basic-features/environment-variables)。这种`.env`文件在构建时部署 Next.js 应用程序时使用。

因此，如果您希望您的带有定制`.env`文件的 Create-Next-App 应用程序正确构建，您必须更改`build`命令。具体来说，您需要用一个定制的构建脚本来替换它。通过这种方式，您可以克服前面提到的与`.env`文件相关的限制。

## 2.定义自定义构建脚本

首先，在项目的根目录下创建一个`build.sh`文件，如下所示:

这里，当`ENV`环境变量等于“staging”时，`.env.local`被删除，`.env.production`被替换为`.env.staging`。这样，`next`会将自定义的`.env.staging`文件视为`.env.production`，并在构建应用程序时使用它。

请记住，您可以通过重复`if ... fi`逻辑轻松地将该解决方案扩展到多个环境变量，并相应地修改测试条件。

现在，您需要更新`package.json`，使其调用`build`上的自定义`build.sh`脚本。您可以通过以下方式实现这一点:

```
"scripts": {
  // ...
  "build": "bash build.sh",
  // ...
}
```

运行`npm run build`时，会执行`build.sh`文件。`buld.sh`包含了`next build`命令，应用程序将像以前一样构建。但是具有在`next build`命令之前定义的`.env`文件管理逻辑。

## 3.在部署服务器中设置 ENV 环境变量

现在，您必须在您的临时部署服务器中设置`ENV`环境变量。在 Linux 上，您可以使用下面的命令来完成:

```
export ENV=staging
```

恭喜你。您刚刚学习了如何在 Create-Next-App Next.js 应用程序中使用定制的`.env`文件。

# 结论

在本文中，您了解了如何设置 Next.js 应用程序来支持多个定制的`.env`文件。当涉及到`.env`文件时，Create-Next-App 应用程序有一些限制。因此，如果您有更多的部署环境，而不是`next`所期望的，您必须定义定制逻辑来支持定制的`.env`文件。在这里，你有机会看到如何去做。

感谢阅读！我希望这篇文章对你有所帮助。请随意留下任何问题、评论或建议。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)