# Node.js 中的 Deephaven OpenAPI 入门

> 原文：<https://levelup.gitconnected.com/getting-started-with-deephaven-openapi-in-node-js-41f7dffe31e>

## 从其他应用程序访问您的 Deephaven 部署的工具

马修·鲁尼恩

![](img/6bd4b436c04d2e226c1a0db46097437a.png)

由[猎人哈利](https://unsplash.com/@hnhmarketing?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 什么是 Deephaven OpenAPI？

Deephaven 是一个强大的数据分析工具，可以快速查询和更新数十亿行的实时数据。Node.js 是一个健壮的服务器端 JavaScript 引擎，可以充当几种不同工具之间的兼容层。Deephaven OpenAPI 通过为您的 Deephaven 实例提供一个访问层，让您轻松地将两者结合起来。OpenAPI 目前支持 JavaScript(其他语言也在计划中)，但是使用 Node.js 需要一些小的调整。在本文中，我们将介绍如何在 Node.js 中设置一个基本的 Deephaven OpenAPI 应用程序。

在 GitHub 上查看我们的 [OpenApi 节点启动器](https://github.com/deephaven/openapi-node-starter) repo。

# 聚合填充一些缺失的部分

由于 OpenAPI 主要是考虑到浏览器的使用而设计的，所以 Node 中缺少一些必须填充的东西。

*   首先，我们需要一个 websocket 包。我们将使用“ws”。
*   然后，我们需要添加一些事件和其他全局浏览器对象，这些对象由 API 使用，但在 Node 中却没有。

以下代码显示了 polyfill，我们称之为“openapiPolyfill.js”。请注意，定义的位置应该指向您的服务器。

# 与服务器保持同步

OpenAPI 在 Deephaven 发布文件中提供了一些不错的助手类。这些助手类都位于服务器上一个名为“irisapi.nocache.js”的文件中。此文件已被版本化，因此服务器更新时必须更新。

为了简化这一点，我们将在启动应用程序之前创建一个函数来异步包含我们的 API 文件。

下面的代码将:

*   从您的服务器 URL 中检索所需的文件，
*   把它写到磁盘上，
*   需要文件，
*   最后解析一个承诺，表明 API 已经可以使用了。

我们将把这个文件称为“openapiAsyncInclude.js”。

# 连接到深水港

现在我们已经有了一些节点细节，我们可以使用 OpenAPI 连接到 Deephaven。我们将使用上一节中的函数来包含来自服务器的最新 API 文件，一旦完成，我们就可以使用全局公开的“iris”对象上的客户端类进行连接。

客户端类有几个在连接状态改变时触发的事件:

*   **‘connect’**事件让我们知道我们最初已连接并可以登录。登录后，我们就可以完全使用 OpenAPI 了。
*   **‘reconnect’**事件让我们知道我们的客户端已经重新连接，但是仍然通过了身份验证。Client 类的一个优点是它为您管理重新认证。如果由于某种原因它无法重新进行身份验证，那么您会得到“reconnectauthfailed”事件。监听这个事件并尝试重新启动我们的应用程序可能是有用的。可能是服务器更新导致我们的文件不同步，从而导致认证失败。幸运的是，您可以在 Node 中的任何位置需要文件，这使得它成为从服务器下载最新 API 文件并重新启动的逻辑位置。
*   感兴趣的最后一个事件是**【断开】**。这个事件确切地表明了它所说的:我们的客户机与服务器断开了连接。你可以添加一个延迟，然后尝试重新启动应用程序。您还可以发送某种通知，以便知道发生了问题。

下面的代码是一个例子，展示了如何把所有的东西放在一起，这样你就可以开始使用 Deephaven OpenAPI 了:

# 摘要

在本文中，我们介绍了如何将 Deephaven OpenAPI 与 Node.js 一起使用，包括如何填充 Node 缺少的内容，如何轻松地将 OpenAPI 文件与服务器保持同步，以及如何建立连接和进行身份验证。完成这个简单的开始后，Node.js 可以用来轻松地将 Deephaven 与其他服务集成。