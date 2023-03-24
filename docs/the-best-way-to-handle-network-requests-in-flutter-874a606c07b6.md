# 在 Flutter 中处理网络请求的最佳方式

> 原文：<https://levelup.gitconnected.com/the-best-way-to-handle-network-requests-in-flutter-874a606c07b6>

在我最近的一篇名为 [***“在 Flutter 中集成 API”***](https://medium.datadriveninvestor.com/integrating-apis-in-flutter-897cc6bf3f73)的文章中，我谈到了我们如何在 Flutter 中集成 API。我使用了著名的 http 包来处理网络请求。但是它有自己的优点和缺点。这篇文章是我上一篇文章的延伸，如果你还没有浏览过它，请在阅读这篇文章之前阅读这里的。

在本文中，我们不使用 http 包，而是使用 Dio 包来处理我们的 REST API，我还将展示编写网络处理代码的最佳实践，其中我们将只创建一个 dart 类 ***(神奇代码)*** ，它可以处理所有的 **HTTP** 请求 **(GET、POST、DELETE、PATCH)** ，并且该类文件可以在您轻松创建的任何类型的项目中使用。但在此之前，http 包有什么问题？

# http 包有什么问题？

Flutter 提供了一个 http 包，在执行基本的网络任务时很好，但是当你开始处理一个大的应用程序，我们需要做一些更高级的任务时，http 包就没有这个功能了。您的应用程序可能会面临许多网络错误处理的问题。在这种情况下，我们需要一些高级的库，具有一些额外的功能，如拦截器、日志、缓存等。这就是 Dio 派上用场的地方。

# Dio 是什么？

Dio 是 dart 的一个强大的 HTTP 客户端，它支持拦截器、全局配置、表单数据、请求取消、文件下载、超时等。通过将 http 包与 Dio 进行比较，Dio 提供了一个直观的 API，可以用最少的努力执行高级网络任务。

# 入门指南

你可以从我的 [Github repo](https://github.com/TheBoy-WhoCode/shopme) 里克隆这个应用。如果你想继续这篇文章，你可以在主分支机构做改变。如果你想要最终的代码，只需将分支从 master 改为 dio。让我们把 dio 加入到我们的项目中。转到 pubsec.yaml，在 dependencies 下，添加 dio 包。

```
dependencies:
  dio: 
```

运行**“flutter pub get”**安装包。现在，转到 ***服务*** 文件夹，其中已经有一个名为***http _ service . dart .***的文件。让我们对这个文件进行修改。

# 神奇的代码

删除我们的***http _ service . dart***文件中的全部代码，添加上面的代码。我来给你一一解释一下代码。我们已经声明了一个名为方法的**枚举**，它包含所有的 **HTTP** 方法(第 5 行)。在 ***HttpService*** 类中，我们有一个名为 ***header()*** 的静态方法，它有一个内容类型的配置。在处理授权时，还可以向该方法添加授权头。在 ***init()*** 方法中，我们用***baseoptions***初始化了 Dio 实例，它接受**基 URL** ，并且有一个名为 **header** 的可选参数。我们把我们之前写的方法 ***header()*** 传递给它*(这也叫函数/方法作为参数)*。我们还有拦截器，帮助我们记录请求、响应和错误。我将在其他文章中更深入地讨论拦截器的概念，但是这里有一篇很棒的 [*文章*](https://medium.com/flutter-community/dio-interceptors-in-flutter-17be4214f363) 来理解什么是拦截器。这里要考虑的主要部分是我们的 ***request()*** 方法，因为那是所有 ***魔法发生的地方。*** 它接受两个必需参数和一个可选参数，分别称为 ***url、方法、*** 和 ***params*** 。代码是不言自明的，我们检查方法的类型并相应地执行请求。我们还将代码包装在一个用于错误处理的 ***try-catch*** 块中，该块根据我们收到的状态代码抛出异常。在第 74 行，我们有 ***DioError*** ，这是一个帮助处理 Dio 错误的类。

耶！就这样，我们完成了我们的魔法代码。您可以在任何 flutter 项目中使用这段代码，并处理任何类型的 ***HTTP 请求。***

# 在我们的项目中进行更改

在编写上面的 ***HttpService*** 类时，您也会遇到***product _ controller . dart***文件中的错误。让我们也对这个文件进行修改，以使我们的应用程序运行。

实例化 ***HttpService*** 类(第 10 行)。在这个文件中，我们必须做的唯一更改是在 ***fetchProducts()*** 方法中。传递需要的参数，即 ***url*** 和 ***方法*** 。正如我们已经在我们的 **HttpService** 类中提到的基本 URL，这里我们只需添加我们必须点击的 **route** 。例如，这里我们已经触及了**【产品】**路线。将**【产品】**传递给 ***url*** 参数。我们正在制作一个**得到**的请求所以传递**到*的方法。获取*** 到一个 ***方法*** 参数(第 23–24 行)。检查结果是否为空，同时检查我们得到的响应是否为 Dio 响应。最后，我们更新了产品列表(第 24–30 行)。

唷！

![](img/528e8cd2b9bf06687a471c780d156011.png)

# 最终输出

![](img/4e6fa67750ea7b6921258b979bb9953b.png)

作者 Gif

# 结论

我们编写了一个神奇代码，帮助我们进行任何类型的 HTTP 请求，我们还动手操作了一个实际的应用程序。**可能是提出网络请求的最佳方式。不是吗？**

**GithubRepo 的链接:**

[](https://github.com/TheBoy-WhoCode/shopme) [## GitHub - TheBoy-WhoCode/shopme

### 在 GitHub 上创建一个帐户，为 Boy-WhoCode/shopme 开发做出贡献。

github.com](https://github.com/TheBoy-WhoCode/shopme) 

**上一篇文章:**

[](https://medium.datadriveninvestor.com/integrating-apis-in-flutter-897cc6bf3f73) [## 在 Flutter 中集成 API

### 将 Flutter 中的 API 与状态管理集成的简单易行的方法

medium.datadriveninvestor.com](https://medium.datadriveninvestor.com/integrating-apis-in-flutter-897cc6bf3f73) 

几乎我写的所有文章...我怀着对❤️的爱写下这些话，并确保每个字对周围的人都有意义...如果你觉得这对你有帮助，那就和你的朋友分享吧..鼓掌吧，Github 明星⭐

有疑惑？让我们连线上[**LinkedIn**](https://www.linkedin.com/in/jiten-patel-jp/)**，**[**Twitter**](https://twitter.com/thejitenpatel)**，**&**insta gram**

**快乐编码:)**