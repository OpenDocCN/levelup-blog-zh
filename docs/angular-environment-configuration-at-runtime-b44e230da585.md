# 角度—运行时的环境配置

> 原文：<https://levelup.gitconnected.com/angular-environment-configuration-at-runtime-b44e230da585>

## 构建一次应用程序，并在运行时为任何阶段注入环境配置

![](img/d4d5c821c0e5462b828535aef3a3d75d.png)

在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上由 [Jarrod Erbe](https://unsplash.com/@ajonesyyyyy?utm_source=medium&utm_medium=referral) 拍照

今天的主题是关于部署 Angular 应用程序。通常，我们希望将应用程序部署到多个阶段，例如开发、测试和生产环境。一般来说，应该在每个环境中部署相同的代码，但是，某些参数，如后端的 URL 或身份验证变量，可能会有所不同。通常，**环境. ts** 文件用于此目的，这些文件通常已经由 Angular CLI 为新项目自动创建。在本文中，我介绍了加载环境参数的另一种方法以及它提供的好处。

## 为什么不是环境文件？

让我们首先澄清为什么 Angular 暗示的方式可能不是注入环境配置的最佳选择。我想你们大多数人都熟悉 Angular 的**环境. ts** 文件，并像我一样使用它们来配置不同的环境。虽然这种方法并非完全错误，而且确实很有效，但是它也有一个缺点。

为了使用不同的文件，我们需要通过使用某些命令来构建应用程序，用在 **angular.json** 文件中配置的相应文件替换 **environment.ts** 文件。这一切都发生在编译时，这正是这种方法的问题所在。

## 编译时与运行时

因此，对于编译时配置，我们必须在编译期间将环境变量注入到应用程序中。因此，我们需要为每个环境构建一个新的版本。然而，使用运行时配置，我们可以在应用程序启动时读取环境变量。一个巨大的好处是我们可以在任何环境下使用相同的构建。

我们现在已经简要讨论了 **environment.ts** 文件以及编译时和运行时配置之间的区别。接下来，让我们检查一个例子，看看在启动应用程序时如何注入环境参数。

## 运行时方法

过程如下:一旦应用程序启动，我们就使用一个简单的 JSON 文件和一个 HTTP 请求加载所需的环境变量。因此，我们首先创建一个新的服务。以下 Angular CLI 命令对此有所帮助:

```
ng generate service environment-loader
```

我们在服务中实现了两个方法。一个用于加载我们的配置，另一个用作获取器:

`loadEnvConfig`接收`configPath`作为参数，然后执行一个简单的 GET 请求。注意，这里我们使用 RxJs 的`lastValueFrom`函数，它将一个可观察值转换为一个承诺。这很有用，因为应用程序在继续之前会等待承诺得到解决。

如上所述，`getEnvConfig`是一个简单的 getter 函数，可以在整个应用程序中用来检索配置数据。

接下来，我们需要告诉 Angular 在启动时调用我们的`loadEnvConfig`方法。我们可以利用`APP_INITIALIZER`令牌来做到这一点。这个令牌用于调用我们的`EnvironmentLoaderService`的工厂函数。该功能将在应用程序启动过程中执行，配置数据将在启动时可用。一个例子如下:

我们有了自己的`initAppFn`工厂，并将其添加到`AppModule`的 providers 数组中的`APP_INITIALIZER`标记中。当我们在工厂中调用`EnvironmentLoaderService`时，我们将它添加到令牌的依赖项中。

现在我们已经做好了一切准备，可以在启动时加载我们的配置了。在这种情况下，JSON 是从 assets 文件夹中检索的，但是您也可以从任何地方请求它。在您的部署管道中，您需要确保提取正确的配置文件。

查看这个 StackBlitz，获得一个小的工作示例:

## 好处

我已经就运行时方法的优势给出了一些提示，但是让我们总结一下。尽管这两个概念都可行，但我会选择在运行时加载我的配置(至少在任何具有多个阶段的适当构建和部署管道的项目中)。这允许相同的代码(完全相同的二进制文件)用于所有阶段。

除了节省时间，因为需要的构建更少，这还可以避免阶段之间的错误，因为重新编译的代码中可能会有变化。

在 [Medium](https://saackef.com/) 或 [Twitter](https://twitter.com/sw3eks) 上关注我，阅读更多关于 Angular 的内容！

# 分级编码

感谢您成为我们社区的一员！更多内容见[级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在改造理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)