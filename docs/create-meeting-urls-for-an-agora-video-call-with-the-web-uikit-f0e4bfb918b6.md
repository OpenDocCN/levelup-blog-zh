# 使用 Web UIKit 为 Agora 视频通话创建会议 URL

> 原文：<https://levelup.gitconnected.com/create-meeting-urls-for-an-agora-video-call-with-the-web-uikit-f0e4bfb918b6>

![](img/8c27b148072a78003a64f1cd275783f4.png)

在您的网站上加入视频通话的最简单方法是分享一个独特的链接。在本教程中，我们将了解如何从 URL 访问查询参数，并在 Web UIKit 中使用它们。我们还将了解如何为您的视频通话或直播生成独特的链接。

想要了解更多关于 Web UIKit 的信息，你可以阅读[发布博客](https://www.agora.io/en/blog/agora-web-uikit-add-video-calling-or-live-streaming-to-your-website-in-minutes/)或者[技术深度探讨](https://www.agora.io/en/blog/agora-web-uikit-add-video-calling-or-live-streaming-to-your-website-in-minutes/)。你可以在 [GitHub](https://github.com/AgoraIO-Community/Web-React-UIKit/) 上找到 Web UIKit。你也可以在这里找到这篇博文[的完整项目。](https://github.com/EkaanshArora/web-uikit-linkdemo)

# 先决条件

如果你的网站是用 React 构建的，我们推荐使用 [React 包](https://www.npmjs.com/package/agora-react-uikit)。通过使用 [web 组件版本](https://github.com/AgoraIO-Community/Web-React-UIKit/releases/tag/v0.0.5)，您还可以将 Web UIKit 与其他框架(或普通 JavaScript)一起使用。

你还需要一个 Agora 开发者账户(这是免费的—[在这里注册](https://sso.agora.io/en/signup?utm_source=medium&utm_medium=blog&utm_campaign=generating-urls-for-an-agora-video-call-using-the-web-uikit)！).可以在 GitHub 上找到已经完成的项目: [React](https://stackedit.io/app) ， [JavaScript](https://stackedit.io/app) 。

## React 的设置

你可以在 GitHub 上获得[例子的代码。我们将在这个例子的基础上进行构建。您现在可以运行`npm i && npm start`来启动 React 服务器，并在`localhost:3000`上访问 hello world 应用程序。](https://github.com/AgoraIO-Community/Web-React-UIKit/tree/main/example)

## JavaScript 的设置

您可以下载[示例项目](https://github.com/AgoraIO-Community/Web-React-UIKit/tree/main/web-component/public)并通过打开 index.html 文件开始。

# 访问查询参数

假设我们的网站托管在`example.com`，我们希望用户加入一个名为`test`的房间。我们可以创建一个链接`example.com/?channel=test`，在这里我们可以向查询参数添加数据(URL 中的`?`符号后面的`key=value`对)。我们可以用`&` : `example.com/?channel=test&token=123`分隔多个参数。

我们创建了一个名为`parseParams`的函数，它使用`URLSearchParams`函数从 URL 返回查询参数。我们将`key=value`对添加到一个对象并返回它。

## 在 React 中使用带有 Web UIKit 的查询

我们可以简单地用`rtcProps`中的 spread ( `…`)操作符调用`parseParams`函数。这种模式让我们在查询参数丢失的情况下有很好的默认值。它让我们覆盖任何一个`rtcProps`。您也可以传入任何其他[支持的道具](https://agoraio-community.github.io/Web-React-UIKit/interfaces/RtcPropsInterface.html)，如`role`、`layout`和`activeSpeaker`。

## 在 Vanilla JS 中使用 Web UIKit 查询

您可以使用`querySelector`选择 web 组件。然后，您可以更新对象属性。在代码片段中，我迭代参数以直接更新 URL 中的所有值(跳过`parseParams`函数)。

> *注意:*最终用户可以轻松修改 URL 中的查询参数。如果你直接把数据传递给你的应用程序，你应该净化用户输入。例如，使用这段代码，角色设置为`“audience”`的用户可以很容易地将其更改为`“host”`以主机身份加入，这对您的应用程序来说可能是不可取的。

# 奖励:生成链接

生成唯一链接的一种快速方法是使用 JavaScript 中的`Date`对象使用当前时间戳。这里有一个创建新房间的简单函数:

# 结论

如果你在关注这篇博文或使用 [Web UIKit](https://github.com/AgoraIO-Community/Web-React-UIKit/) 时有任何问题，我邀请你加入 [Agora 开发者 Slack 社区](https://agora.io/en/join-slack)，在那里你可以在`#web-help-me`频道提问。欢迎提出功能请求或在 [GitHub Repo](https://github.com/AgoraIO-Community/Web-React-UIKit/issues) 上报告错误。