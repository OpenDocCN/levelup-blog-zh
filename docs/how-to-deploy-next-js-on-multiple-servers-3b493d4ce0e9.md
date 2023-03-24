# 如何在多台服务器上部署 Next.js

> 原文：<https://levelup.gitconnected.com/how-to-deploy-next-js-on-multiple-servers-3b493d4ce0e9>

## 处理由没有会话亲缘关系的负载平衡器服务的多个 Next.js 实例

![](img/8aa4bed8146a50c45a300f7d43e57386.png)

由[都铎·巴休](https://unsplash.com/@baciutudor?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Next.js 中的多服务器部署很棘手。如果没有正确的配置，每次您的浏览器由[负载平衡器](https://en.wikipedia.org/wiki/Load_balancing_(computing))提供不同的实例时，都会执行硬刷新。发生这种情况是因为您的浏览器无法将您正在运行的应用程序包含的实例识别为同一应用程序的一部分。

这打破了 Next.js 通常检索数据的方式，并且不允许它正常工作。因此，这不仅会导致糟糕的性能，还会妨碍 Next.js 的独特功能发挥作用。

换句话说，你可能最终会失去 Next.js 带来的几乎所有好处。幸运的是，它可以被配置来避免这一切。因此，让我们看看如何在多服务器环境中部署 Next.js 应用程序时，使其按预期工作。

# 了解 Next.js 构建 ID 逻辑

> Next.js 使用在构建时生成的常量 id 来标识应用程序的哪个版本。当`*next build*`在每台服务器上运行时，这会在多服务器部署中导致问题。为了在构建之间保持一个静态的构建 id，您可以提供您自己的构建 id。— [配置构建 ID](https://nextjs.org/docs/api-reference/next.config.js/configuring-the-build-id)

正如官方文档中所解释的，每当启动`next build`时，Next.js 都会产生一个新的构建 ID，唯一地标识新生成的实例。这意味着前面提到的多服务器部署问题只有在`next build`命令运行多次时才会出现。事实上，启动 build 命令一次，然后在不同的服务器上部署生成的构建包，每个实例将具有相同的构建 ID。

不幸的是，具有负载平衡器的多服务器环境通常在每次需要基于流量创建新实例时启动 build 命令。基本上，当当前服务器负载过大时，部署环境会动态地创建一个新实例。而要做到这一点，就启动了`next build`，导致了不同 build IDs 的问题。

请记住，拥有多个具有不同构建 id 的实例本身并不是问题。只有在同一导航会话中为同一用户提供不同实例时，才会出现问题。这意味着，如果您的多个实例由具有会话关联性的负载平衡器提供服务，最终用户将不会遇到任何问题。这是因为一个用户将总是得到相同实例的服务。

另一方面，用户现在正在使用的实例可能因为低流量而被删除。在这种情况下，负载平衡器将被迫为用户提供另一个实例。因此，您不应该只依赖负载平衡器。

# Next.js 中的多服务器部署

正如这里提到的，Next.js 允许你定义一个定制的构建 ID。具体来说，可以通过使用`[next.config.js](https://nextjs.org/docs/api-reference/next.config.js/introduction)`文件中的`[generateBuildId](https://nextjs.org/docs/api-reference/next.config.js/configuring-the-build-id)`属性来实现，如下所示:

生成一个合适的构建 ID 不是一项简单的任务。例如，避免使用当前时间戳，因为`next build`命令都是在不同的时间启动的。一个好的解决方案是使用最新的 git 提交散列。这将确保所有实例都有相同的构建 ID。

要实现这一点，您可以使用`[next-build-id](https://www.npmjs.com/package/next-build-id)` npm 包。首先，使用以下命令将其添加到项目的依赖项中:

```
npm install next-build-id
```

然后，您可以按如下方式使用它:

`nextBuildId()`函数将从您的本地 git 存储库中返回最新的 git 提交散列。换句话说，这相当于:

```
git rev-parse HEAD
```

`next-build-id`还提供其他选项。在其官方 GitHub 页面上了解更多信息。

为了验证您的 Next.js 实例使用了正确的构建 ID，您可以查找存储在构建目录中的`BUILD_ID`文本文件。默认情况下，它是`.next`目录。否则，您可以从 Next.js 提供的静态资源的 URL 来验证它。您可以从浏览器开发工具的网络部分检索这些 URL。事实上，这是 Next.js 在设置构建 ID 时为静态文件使用的 URL 格式:

```
/_next/static/<build-ID>/<static-file>
```

例如，以下静态资源:

```
[https://www.example.com/_next/static/_buildManifest.js](https://www.example.com/_next/static/_buildManifest.js)
```

会变成:

```
[https://www.example.com/_next/static/c657j0328d606cc59cffafe62c09926eb6b749de/_buildManifest.js](https://www.example.com/_next/static/c657j0328d606cc59cffafe62c09926eb6b749de/_buildManifest.js)
```

如您所见，静态资源 URL 现在包括“c 657j 0328d 606 cc 59 cffafe 62 c 09926 EB 6b 749 de”，它表示用作构建 ID 的 git 提交散列。

`next-build-id`是一个很棒的工具，但请记住，它只有在您有权访问您的`.git`文件夹时才有效。例如，在处理 [AWS 弹性豆茎](https://aws.amazon.com/elasticbeanstalk/)时，情况并非如此。

所以，让我们看看在使用 AWS Elastic Beanstalk 时，如何给 build ID 一个合适的值。

## 在 AWS 弹性豆茎上展开

AWS Elastic Beanstalk 执行部署所需的`[eb deploy](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb3-deploy.html)`命令依赖于`git archive`命令。这不会复制`.git`文件夹，使`next-build-id`无法工作。

幸运的是，GitHub 用户找到了一个变通办法。正如这里的[所解释的](https://github.com/vercel/next.js/issues/786#issuecomment-393303217)，您可以从您的本地 git 存储库中生成一个等于最新 git 提交散列的构建 ID，而不需要`next-build-id`，如下所示:

1.在`next.config.js`中，赋予`generateBuildId`属性以下值:

2.在您的根目录中创建一个`[.gitattributes](https://git-scm.com/docs/gitattributes)`文件，并用下面的代码行初始化它:

```
next.config.js export-subst
```

这个命令将由`git archive`使用，并将负责用当前 git 提交散列替换“$Format:%H$”字符串。

在本地构建项目需要使用`if`语句。这是因为“$Format:%H$”不是有效的内部版本 id。因此，如果没有它，本地启动的`npm run build`命令将会失败。特别是，当应用`next.config.js export-subst`时，两个“$Format:%H$”字符串将被替换为提交散列，并且`if`条件将为假。相反，如果没有`next.config.js export-subst`命令，则`if`语句将为真，将使用“static-build-id”构建 id。

# 结论

在这里，我们研究了如何在多服务器环境中部署 Next.js 应用程序。使用 Next.js `generateBuildId`特性允许您让所有实例共享相同的构建 ID。这就是 Next.js 用来创建与客户端的会话的内容，如果没有这些信息，这是不可能的。`next-build-id`是正确生成它的好解决方案，但是如图所示，使用 AWS 时需要定制逻辑。

感谢阅读！我希望我的故事对你有所帮助。如果有任何问题、意见或建议，请随时联系我。