# Axios 入门:一个流行的基于 Promise 的 HTTP 客户端

> 原文：<https://levelup.gitconnected.com/getting-started-with-axios-a-popular-promise-based-http-client-9c4b65a9847a>

## 了解 Axios 提供的关键特性，以及如何在 web 应用程序中使用它们。

![](img/0e2a46deb2077c3d6524288914afd4b6.png)

Axios 是浏览器和 Node.js 的一个流行的基于 promise 的 HTTP 客户端，在服务器端(Node.js)使用原生 Node.js HTTP 模块，而在客户端(浏览器)使用 XMLHttpRequests。

我将详细演示 Axios 提供的关键特性，以便您能够轻松理解它们，并开始在您的项目中使用它们，改善您在 web 应用程序中进行 API 调用的体验。初学者和有经验的开发人员可以从这篇文章中受益。

# 使用 Axios 优于 Fetch API 的优势

现代网络浏览器附带了 Fetch API，但是有各种各样的原因让 Axios 具有优势。主要区别在于两个库处理 HTTP 错误的方式。使用 Fetch API 时，如果服务器返回 4xx 或 5xx 系列错误，则不会触发`catch()`回调。您必须检查响应对象中的状态代码，以确定请求是否成功。另一方面，如果服务器返回这些错误代码中的一个，Axios 将拒绝请求承诺。

第二个区别是，Fetch 在执行请求时不会(默认情况下)将 cookies 发送回服务器。为了包含 cookies，您必须明确提供一个选项。另一方面，Axios 会在你的每个请求中自动发送 cookies。

第三个区别是，Fetch 不提供任何对上传进度的监控支持，如果你处理多个文件上传，这可能会成为你的绊脚石。另一方面，Axios 提供回调函数来监控上传和下载的进度。您可以使用`onUploadProgress()`和`onDownloadProgress()`在您的应用程序 UI 中显示进度。

# 安装 Axios

您可以通过以下方式安装 Axios:

*   使用 NPM
*   使用纱线
*   使用凉亭
*   使用 CDN

## 1.使用 NPM

执行以下命令安装 Axios:

```
npm install axios
```

## 2.使用纱线

执行以下命令安装 Axios:

```
yarn add axios
```

## 3.使用凉亭

执行以下命令安装 Axios:

```
bower install axios
```

## 4.使用 CDN

包含以下脚本以使用 jsDelivr 中的 Axios:

```
<script src="[https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js](https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js)"></script>
```

或者，包含以下脚本来使用 unpkg 中的 Axios:

```
<script src="[https://unpkg.com/axios/dist/axios.min.js](https://unpkg.com/axios/dist/axios.min.js)"></script>
```

# 请求 Axios 提供的方法

下面是 Axios 中提供的用于发出 HTTP 请求的基本函数。

```
axios.request(config)
axios.get(url[, config])
axios.delete(url[, config])
axios.head(url[, config])
axios.options(url[, config])
axios.post(url[, data[, config]])
axios.put(url[, data[, config]])
axios.patch(url[, data[, config]])
```

# 请求 Axios 提供的选项

Axios 提供了大量选项，您可以在发出请求时传递这些选项。以下是最常用选项的列表:

*   baseURL 如果指定，将被添加到您使用的任何相对 URL 的前面。
*   标题—具有键值对的对象。
*   params —随请求一起发送的具有键值对的对象。它应该是一个普通对象或一个 URLSearchParams 对象。
*   responseType —指示服务器将响应的数据类型。有效选项为“arraybuffer”、“document”、“json”、“text”、“stream”。
*   auth —指示应通过提供必要的凭据(用户名和密码)使用 HTTP 基本身份验证来连接到代理。

# Axios 提供的响应对象

当您向远程服务器发送一个 HTTP 请求时，您将得到一个包含特定信息的响应(如果请求被满足)。根据 Axios 文档，收到的响应包含以下信息。

*   数据—服务器提供的响应。
*   状态—来自服务器响应的 HTTP 状态代码。
*   statusText —来自服务器响应的 HTTP 状态消息。
*   头-服务器响应的 HTTP 头。所有标题名称都是小写的，可以使用括号符号来访问。例子:`response.headers['content-type']`。
*   配置—为请求提供给 Axios 的配置。
*   请求—生成此响应的请求。它是 Node.js 中的最后一个 ClientRequest 实例(在重定向中)，也是浏览器中的一个 XMLHttpRequest 实例。

# 正在向服务器发送 GET 请求

Axios 提供了一个 GET 方法来向服务器发送 HTTP 请求。第一个参数接受目标 URL(请求将被发送到该 URL)。第二个参数接受多个键值对的对象(我们称之为选项)。在这个例子中，我们将使用`params`选项向服务器发送一些数据。这个`params`选项也可以包含多个键值对。

请参考下面的代码片段，了解如何向服务器发送 GET 请求、获取一些响应以及捕捉错误。

# 向服务器发送 POST 请求

Axios 提供了一个 POST 方法来向服务器发送 HTTP 请求。第一个参数接受目标 URL(请求将被发送到该 URL)。第二个参数接受多个键值对的对象。第三个参数接受一个对象(我们称之为选项)。在这个例子中，我们将使用第二个参数向服务器发送一些数据。

请参考下面的代码片段，了解如何向服务器发送 POST 请求、获取一些响应以及捕捉错误。

# 使用异步和等待代替承诺

如果您希望使用异步和等待功能而不是承诺(`then()`和`catch()`块)，您可以通过几个简单的步骤来实现。

参考下面的代码片段来理解如何使用 async 和 await 来处理 HTTP 请求。

# 发送多个并发请求

Axios 提供了一个同时向服务器发送多个请求的功能。可以使用`Promise.all()`函数传递一个 HTTP 请求数组。当所有请求都得到满足时，它将生成一个单一的承诺，其中将包含每个请求的响应对象。若要取得每个要求的回应，您可以使用要求阵列的索引。

请参考下面的代码片段，了解如何使用`Promise.all()`函数发送多个并发请求。

# 处理失败的 HTTP 请求

如果提交的 HTTP 请求失败，Axios 将捕获错误，并将其存储在错误对象中。该对象将包含一组详细信息，这些信息将帮助您找到错误的确切位置。

请参考下列程式码片段，瞭解如何使用 error 物件来处理错误。

# 转换请求和响应

默认情况下，Axios 将请求和响应转换为 JSON 格式。但仍然可以覆盖这种行为，并通过`transformRequest()`和`transformResponse()`函数应用自己的转换格式。当某些 API 只支持 XML 或 CSV 格式时，这些就变得很方便了。

> `transformRequest()`功能仅适用于以下请求方式:“PUT”、“POST”、“PATCH”和“DELETE”。

请参考下面的代码片段，以了解如何转换请求和响应。

赞。您已经完成了对 Axios 提供的关键功能的学习。现在，您可以开始在当前或即将开展的项目中集成 Axios。

> 如果你喜欢阅读这篇文章，并且已经学到了一些新的东西，那么请鼓掌，与你的朋友分享它，并且跟随我来获得我即将发表的文章的更新。你可以通过 [LinkedIn](https://www.linkedin.com/in/tara-prasad-routray-b83027145/) 和我联系。