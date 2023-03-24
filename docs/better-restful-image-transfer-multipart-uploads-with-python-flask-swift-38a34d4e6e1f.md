# 更好的 RESTful 图像传输:使用 Python Flask & Swift 进行多部分上传

> 原文：<https://levelup.gitconnected.com/better-restful-image-transfer-multipart-uploads-with-python-flask-swift-38a34d4e6e1f>

![](img/7c6412869e8c01bc351bbdfa13692e63.png)

照片由[霍尔格链接](https://unsplash.com/@photoholgic?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在我的上一篇[博客文章](https://medium.com/swlh/restful-image-transfer-with-base64-encoding-cd9fb4453598)中，我讨论了如何创建一个支持使用 Base64 编码的图像传输的后端，以及 iOS 客户端的前端代码。在本文中，我将记录一种处理 RESTful 图像传输的替代(也可能是更好的)方法。

Base64 编码图像传统上用于网站，作为静态文件加载的替代方法，有助于节省额外的 HTTP 调用来检索图像。在 REST-land 中，如果您想采用纯 JSON 驱动的 API，通过 API 请求发送 base64 是有用的。也就是说，以 base64 格式编码图像会增加大约 33%的大小，使其成为在客户端和服务器之间传输图像的低效方法。

那么，有什么更好的选择呢？

# 1 多部分表单数据

作为 [POST 请求](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/POST)的一部分的多部分表单数据正如它听起来的那样——这是客户机向服务器传输基于表单的数据的标准方法。它适合我们的目的，因为这种数据传输方法支持文件上传。

如果我们一次上传**一个图像(或者最多几个图像)，通过多部分表单发送图像就足够了。**对于视频和批量图像上传，使用某种流式上传更可行。

# 2 基本的 Python REST 服务器

正如在我的上一篇博文中，一个基本的后端服务器可以用 Python 和 flask 一起创建。使用 flask-restful，端点被定义为 Python 类。同样，我们创建一个`post`函数来表明这个端点支持 HTTP POST 操作。

在我们的`RequestParser`中，我们曾经定义了下面的期望参数:`parser.add_argument("image", type=str, location='json')`。现在，我们通过表单上传发送数据，所以我们将预期的参数改为:`parser.add_argument("image", type=werkzeug.datastructures.FileStorage, location='files')`

这与 base64 的情况没有太大的不同。通过以这种方式实现图像传输，从长远来看，我们节省了大量带宽。我们还减少了运输时间，从而缩短了最终用户的等待时间。

# 3 使用 Swift 发送图像(iOS)

和上一篇博文一样，回想一下当您使用 Swift AVFoundation 库拍摄图像时，您会得到类型为`AVCapturePhoto`的输出。我们可以使用`.fileDataRepresentation()`轻松检索照片的数据表示。

同样，我们利用 Alamofire 框架，它可以方便地处理多部分表单 POST 请求。然后我们可以创建一个具有`makeRequest`功能的服务:

如果您有其他想要发送的参数，您可以将它们作为标题附加到请求中，作为参数添加到 URL 中，或者只是向`formData`添加一个额外的条目。