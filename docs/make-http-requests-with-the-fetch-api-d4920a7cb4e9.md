# 使用 Fetch API 发出 HTTP 请求

> 原文：<https://levelup.gitconnected.com/make-http-requests-with-the-fetch-api-d4920a7cb4e9>

![](img/92c62624a1fb5ce4a1c32d561c0395ed.png)

杰瑞米·帕金斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在 Fetch API 之前，HTTP 请求是用`XmlHttpRequest`对象发出的。它更难使用，而且它不是基于承诺的，因为它是在 JavaScript 内置承诺之前添加的。

现在，我们可以使用 Fetch API 更容易地发出 HTTP 请求。

有了它，我们就有了一个关于`Request`和`Response`对象以及其他网络请求的通用定义。这使得它们可以在未来需要时随时使用。

它还提供了相关概念的定义，如 CORS 和 HTTP 源头语义，取代了它们在其他地方的现有定义。

在本文中，我们将研究如何使用 Fetch API 发出客户端 HTTP 请求。

# 发出 HTTP 请求

发出 HTTP 请求从使用`fetch`方法开始。它有一个强制参数，即我们要获取的资源的路径。

无论成功与否，它都返回一个承诺，该承诺决定了对该请求的`Response`。我们可以选择传入一个`init` options 对象作为参数。

一旦检索到了`Response`,就有许多方法来定义什么是主体内容以及应该如何处理它。

`fetch`返回的承诺即使响应是 404 或者 500 也不会拒绝 HTTP 错误状态。当`ok`状态设置为`false`时，它将正常解决。

`fetch`不会收到跨站 cookies。无法使用`fetch`建立跨站点会话。

`fetch`除非我们设置了凭据初始化选项，否则不会发送 cookies。

我们可以如下调用`fetch`方法:

```
(async () => {
  const response = await fetch('[https://jsonplaceholder.typicode.com/todos/1'](https://jsonplaceholder.typicode.com/todos/1'))
  const json = await response.json();
  console.log(json);
})();
```

上面的代码用`fetch`发出一个 GET 请求，然后用`Response`对象的`json()`方法将其转换成一个 JavaScript 对象。然后我们可以将它记录在`console.log`中。

对于 HTTP 请求来说，这是最简单的情况。我们还可以在第二个参数中添加更多选项。我们可以设置以下选项:

*   `method` —请求方式
*   `headers` —请求我们想要添加的标题
*   `body` —请求正文。可以是`Blob`、`BufferSource`、`FormData`、`URLSearchParams`、`USVString`或`ReadableStream`对象。GET 或 HEAD 请求不能有正文。
*   `mode` —请求的模式。可以是`cors`、`no-cors`或`same-origin`
*   `credentials` —我们希望用于请求的请求凭据。可能的值有`omit`、`same-origin`或`include`。必须提供此选项才能自动发送当前域的 cookies。从 Chrome 50 开始，这个属性也需要一个`FederatedCredential`实例或者一个`PasswordCredential`实例。
*   `cache` —我们希望用于请求的缓存模式
*   `redirect` —使用重定向模式。将此项设置为`follow`表示自动跟踪重定向，`error`表示重定向发生时出错中止，或者`manual`表示手动处理重定向
*   `referrer` —指定`no-referrer`、`client`或 URL 的字符串。默认值为`client`
*   `referrerPolicy` —指定引用 HTTP 头的值。可以是`no-referrer`、`no-referrer-when-downgrade`、`origin`、`origin-when-cross-origin`、`unsafe-url`中的一种
*   `integrity` —请求的子资源完整性值
*   `keepalive` —设置此选项以允许请求在页面上存活
*   `signal`—`AbortSignal`对象实例，让我们与获取请求通信，并通过`AbortController`中止它。

例如，我们可以编写一个基本的 POST 请求:

```
(async () => {
  const response = await fetch('[https://jsonplaceholder.typicode.com/posts'](https://jsonplaceholder.typicode.com/posts'), {
    method: 'POST',
    body: JSON.stringify({
      title: 'title',
      body: 'body',
      userId: 1
    }),
    headers: {
      "Content-type": "application/json; charset=UTF-8"
    }
  })
  const json = await response.json();
  console.log(json);
})();
```

我们在第二个参数的对象中设置所有选项，包括主体和头。

要上传文件，我们可以从文件输入中获取文件。假设我们在 HTML 中有它:

```
<input type='file' id='file-input'>
```

然后，我们可以编写以下代码来观察文件输入值的变化，并将文件上传到服务器:

```
const upload = async (file) => {
  const response = await   fetch('[https://localhost/'](https://jsonplaceholder.typicode.com/posts'), {
    method: 'POST',
    body: file,
    headers: {
      'Content-Type': 'multipart/form-data'
    }
  })
  const json = await response.json();
  console.log(json);
};const input = document.getElementById('file-input');
input.addEventListener('change', () => {
  upload(input.files[0]);
}, false);
```

请注意，标题可能会根据我们使用的服务器而变化。总的想法是，我们从输入中获取文件，然后在请求体中发送它。

![](img/cb2f9de29e8c54d4bf9ace48a2b1d446.png)

照片由[粘土堤](https://unsplash.com/@claybanks?utm_source=medium&utm_medium=referral)在[防溅板](https://unsplash.com?utm_source=medium&utm_medium=referral)上拍摄

# 操纵响应

`Response`有一些获取数据和操作数据的属性。我们可以使用`error`方法得到错误，`redirect`方法用不同的 URL 创建一个新的响应。

`Response`对象实现的`Body`对象具有用于读取`FormData`响应的`formData`方法。同样，还有读取 JSON 响应的`json`方法和读取纯文本的`text`方法。它们都解析为具有相应对象的承诺。`arrayBuffer`方法将读取二进制数据并解析为`ArrayBuffer`。`blob`方法读取二进制数据并将其解析为`Blob`。

可能对我们有用的值属性包括获取响应头的`headers`，检查请求是否成功的`ok`，检查重定向是否发生的`redirect`。`status`是响应状态代码，`statusText`具有对应于状态代码的文本。`url`有响应 URL，`body`有响应正文。

对于发出 HTTP 请求，Fetch API 比`XmlHttpRequest`好得多。它允许我们提出大多数类型的请求，并且它是基于承诺的，因此它们可以与其他承诺一起按顺序运行。

它支持文本和二进制体。现在我们不需要第三方 HTTP 客户端来进行客户端 HTTP 请求。

`Request`和`Response`对象也被标准化，这样它们就可以和其他 API 一起使用。