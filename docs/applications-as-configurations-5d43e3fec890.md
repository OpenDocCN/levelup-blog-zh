# 应用程序作为配置

> 原文：<https://levelup.gitconnected.com/applications-as-configurations-5d43e3fec890>

我有个想法:

> “大多数软件应用程序在不同的配置中包含相同的基本功能”。

我们来玩玩这个。想象一下，一种超高级语言可以简洁地描述一个软件应用程序，但仍然能捕捉到应用程序的有用性。

## ✏️我们的应用

```
**Example 1**
App: Twitter
  Allow user to: login
  Allow user to: send a tweet
  Allow user to: follow another user
  Allow user to: view a timeline of tweets[(further examples here)](https://gist.github.com/aido179/1734bb23182d801f4db3b3e694d54a63)
```

*   我们的应用程序需要一个名字，我们将以 Twitter 为例。
    *我已经举过其他例子* [*这里*](https://gist.github.com/aido179/1734bb23182d801f4db3b3e694d54a63) *也是如此。*
*   非常不言自明，而且如此普遍，以至于我们可以假设它已经融入了语言之中。`login`变成了`library.login`,表示我们将使用提供的库行为，而不是自定义实现。
*   `send a tweet`是一个非常常见的动作的独特实例:获取一些用户输入(数据:文本、图像、视频、音频等)并将其存储在某个地方(写入)，以便稍后可以返回(读取)。我们可以稍后更详细地说明所有这些。现在，让我们想象`send a tweet`只是引用了其他地方的进一步配置，变成了`custom.tweet`
*   `follow another user`同样，一个常见动作的独特名称:在两个事物之间建立链接、联系或关系，在这种情况下，是我们应用程序的用户。在 twitter 的例子中，关注者与被关注者的关系是多对一。脸书朋友对朋友的关系是一对一的。在 reddit 上，用户到子编辑的订阅是一对多的。
    `follow another user` 变成了`custom.follow`
*   `view a timeline of tweets`是一个读动作。它将得到一些东西，以某种方式排列它们，并显示给用户。它可能会有嵌套的动作，比如*喜欢*、*转发*和*回复*，但是我们会在后面处理。它变成了`custom.timeline`

好了，我们迭代一下。

```
**Example 2**
App: Twitter
  Allow user to: library.login
  Allow user to: custom.tweet
  Allow user to: custom.follow
  Allow user to: custom.timeline
```

每个`allow user to`行对应一个 app 的功能，但可能包含多个 UI 组件。`Library.login`显然需要一个登录页面，一个注销按钮和其他元素来更新密码和其他信息。`custom.follow`可能会出现在整个应用程序的多个地方。

## 🌇结构和视图

单独定义应用程序的结构可能是有意义的，但是要参考前面定义的动作。

```
**Example 3**
App: Twitter
  Actions:
    #Allow the user do these...
    - library.login
    - custom.tweet
    - custom.follow
    - custom.timeline
  Views:
    # Allow the user to view these 
    - library.landingPage
    - custom.home
    - custom.profile
```

在这里，我将应用程序分成了`Actions`和`Views`。我们将列表中的第一个视图(library.landingPage)视为默认视图或索引。同样，使用库意味着它是内置的。允许我们使用这样的默认样板文件应该有助于加速开发。我们可以随时回来使用自定义视图。

## 🔗观点和行动

将动作映射到应用程序结构的各个部分并不简单。有些页面会有特定的动作，有些动作会在许多视图中重用。每个视图都应该准确定义它需要的动作。

```
**Example 4**
App: Twitter
  Actions:
    #Allow the user do these...
    - library.login
    - custom.tweet
    - custom.follow
    - custom.timeline
  Views:
    # Allow the user to view these 
    - library.landingPage
        - library.login
    - custom.home
        - library.login.isUserloggedIn
        - custom.tweet
        - custom.timeline
    - custom.profile
        - library.login.isUserloggedIn
        - custom.follow
```

现在视图有了具体的动作。我们现在可以拥有功能性，额外的好处是每个视图中发生的事情一目了然。

注意`library.login`是如何在每个视图中使用的。`library.login`动作就像一个包含许多嵌套函数的包。我们应该能够使用像这样的动作的特定部分——我们不需要让用户在每个页面上登录，而只需要检查他们是否登录。

# 🗣️讨论

可以想象，这样的配置可以用来为应用程序生成所有必要的样板文件。此时，应用程序也可以在任何平台上生成。

这些都不一定是新的。 [Meteor](https://www.meteor.com/) 提供默认的应用程序生成、样板登录功能和客户端-服务器通信。 [React](https://reactjs.org/) 有 [create-react-app](https://github.com/facebook/create-react-app) ，它…为你创建一个 React app。 [Vue CLI](https://cli.vuejs.org/guide/creating-a-project.html) *create* 命令将搭建你的应用。事实上，像 [Yeoman](https://yeoman.io/) 这样的工具实际上允许你定义一个“生成器”并生成一个搭建好的应用程序。此外， [React Native](https://facebook.github.io/react-native/) 采用一个 JavaScript 项目(基本上是一个高度特色的配置)并生成本地应用，类似于 [Cordova](https://cordova.apache.org/) 。

代码生成并不新鲜。那么这里有什么不同呢？

在使用*约曼*、*创建-反应-应用*和 VUE *创建*命令的情况下，一旦生成了脚手架，一切都取决于您。剩下的是标准的应用程序代码，开发照常继续。我在这里提议的是一个紧密耦合，其中配置变成了应用程序，而不仅仅是一个蓝图。也就是说，如果您在任何时候更改配置，代码将会更新以反映该更改。编写与配置冲突的代码，就会出现错误。

开发人员应该能够查看配置并知道应用程序严格符合该定义。

在 Meteor 的例子中，通过其高度功能化的登录样板和客户端-服务器通信(pub/sub)(顺便说一下，我认为这很棒)这更进了一步。用户身份验证和登录并不是一个碰巧工作得很好的方便的包——它应该嵌入到系统的核心。使用 Meteor，开发人员仍然需要编写基于用户登录状态显示/隐藏内容或路线的模板。这太傻了。充其量，这是不方便的，并导致模板开销。在最坏的情况下，当开发人员由于意外或缺乏经验而没有正确处理身份验证时，这是一个安全风险。

**创建不安全的 app 应该是不可能的！**

当视图被这样定义时:

```
...
Views:
    - custom.home
        - library.login.isUserloggedIn
        ...
```

应该没别的事可做了。如果用户没有被正确鉴定，它是不可访问的。就是这样。没有摆弄客户端路由器，入口事件，或应用程序范围的模板或其他任何东西。配置就是上帝。配置获胜。

最后，(就目前而言)在部署和维护方面有巨大的好处。确定性配置应该提供可靠的部署，并使依赖性管理变得容易。想象一下你 5 年前使用 library 构建的应用程序。登录:你应该能够相信新的部署将包括最新的身份验证代码——因为你的应用程序将使用标准配置生成，它总是向后兼容的。即使需要进行大规模的高层修改，只要使用`library.login.isUserloggedIn`，你的视图也会被生成的 app 代码在更高的层次上保护。

## 🎬结论

我知道我现在非常理想化。我敢肯定，有边缘情况和并发症，使这完全不切实际的一些目的。但这不应该是万灵药。它应该适用于 90%的需要通过用户认证保护 CRUD 操作的应用程序。

但是做梦很有趣。

![](img/6225eacab78cd9832db6b54567c60956.png)

这是一条鱼。

我叫艾丹·布林，在爱尔兰都柏林经营一家 T2 软件咨询公司。如果你喜欢这篇文章，可以考虑在[推特](https://twitter.com/aidanbreen)上关注我，或者注册我的[个人邮件列表](https://apbsoftwareandhardware.us3.list-manage.com/subscribe?u=bd4ace12c9503b0fb9dde0282&id=6081e1a8d6)，不要一个月更新一次。

[![](img/9914c5dd23ac08b70eea6f4f9ba6fed2.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn) [## 了解如何编码-查找编码教程| gitconnected

### 使用我们完整的编码资源列表学习任何编程语言或框架。我们分享、汇总和排名…

gitconnected.com](https://gitconnected.com/learn)