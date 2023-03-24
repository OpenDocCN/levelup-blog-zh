# Golang 中的 XML 到 JSON 去神秘化

> 原文：<https://levelup.gitconnected.com/xml-to-json-in-golang-demystified-6639fb090c31>

![](img/b256f5fe967f199d0a4de81db97842ca.png)

在每个开发人员的生活中，XML 数据都是不可避免的。RSS 提要试图让这变得更容易，但每个人都喜欢好的旧 JSON，除非你是一个虐待狂。

在 Golang 中，我们可以用多种方式解决这个问题，比如使用一个 [XML 到 JSON 库](https://github.com/basgys/goxml2json)，但是为什么不使用 Go 标准库并节省一些销售问题呢？

# 构建我们自己的 XML 到 JSON 函数

我最近在做一件需要发布到网站上的中等 RSS 提要的事情，我想为什么不使用运行在 [OpenFaaS](https://www.openfaas.com/) 上的 GoLang 函数来完成这件事。

## 创建新功能

要创建我们的新函数，我们需要使用 OpenFaaS CLI

```
$ faas new medium2json --lang=go
```

现在我们为 OpenFaaS 提供了一个超级简单的 Golang 处理程序。

## 导入我们的库

我们的功能需要几个库。所以还是导入吧。打开*函数/handler.go* 并在包下添加导入

进口！！

你会注意到我添加了 net/http，所以让我们利用它。

***获取*我们的 RSS 源**

普通媒体用户将会知道在[https://medium.com/feed/@affix](https://medium.com/feed/@affix)为每个用户公开了一个 RSS 提要

我们可以利用这个提要，通过 Go 中的 [net/http](https://golang.org/pkg/net/http/) 包使用一个简单的 HTTP GET 来获取我们需要的内容。让我们将它添加到我们的`Handle`函数中:

上面的代码非常简单，但是我总是被问到关于`defer`语句的问题。

> 如果你不推迟，会发生什么？

答案很简单。如果不推迟`Close()`，就会造成资源泄漏。当应用程序运行时，用于创建请求的资源永远不会关闭，并且[文件描述符](https://en.wikipedia.org/wiki/File_descriptor)不会被释放。

> 我需要关闭请求吗？

如果主体没有同时读取到 EOF 和 closed，客户端的底层往返器(通常是传输)可能无法为后续的“保持活动”请求重用到服务器的持久 TCP 连接。

## 解析 XML

现在我们有了一些数据，我们应该解析它。但在此之前，我们需要一个结构来[解组数据。](https://golang.org/pkg/encoding/xml/#Unmarshal)

在上面的 handler.go 文件中，函数让我们创建一个结构来匹配我们需要的数据。GoLang 解组函数的美妙之处在于，它允许我们在结构中只指定我们需要的字段。

如果您查看 Medium Feed 返回的 XML，您会注意到我省略了许多不必要的字段，如`Channel.Title`和`Channel.Image`，因此`xml.Unmarshal`函数不会尝试将这些字段添加到结构中。

你可能还注意到我有一个`Posts []struct`没有出现在 RSS 提要上。这来自 XML 文档的 items 元素，该元素在结构的末尾用`xml:”item”` 指定。解组函数将获取所有的 item 元素，并将它们解析到`Posts []struct`中。

因此，让我们将数据解析成`Handle`函数中的结构:

解组函数解析 XML 编码的数据，并将结果存储在由`v`指向的值中，该值必须是任意的结构、片或字符串。不适合`v`的格式良好的数据被丢弃。

因为解组使用反射包，所以它只能分配给导出的字段。解组使用区分大小写的比较来将 XML 元素名与标记值和结构字段名进行匹配。

**转换为 JSON 并返回**

现在我们有了我们的结构，我们可以[将它编组](https://golang.org/pkg/encoding/json/#Marshal)到 JSON。

Marshal 返回输入接口的 JSON 编码。

Marshal 递归遍历值`v`。如果遇到的值实现了封送拆收器接口并且不是 nil 指针，则 Marshal 调用其 MarshalJSON 方法来产生 JSON。

由于 Marshal 函数产生了一个`[]byte`，当我们使用`string()`从`Handle`函数返回时，我们需要将它转换成一个字符串。

## **部署到 OpenFaaS**

现在，我们可以将我们的功能部署到 OpenFaaS 集群:

```
$ faas up -f medium2json.yml
```

> $ FAAS up-f medium2json . yml
> 【0】>建筑 medium 2 JSON。
> 清除临时构建文件夹:。/build/medium2json/
> 准备。/medium2json/。/build/medium 2 JSON//function
> build:sticking xx/medium 2 JSON:最新带 go 模板。请稍候..
> 将构建上下文发送到 Docker 守护程序 7.68kB

一旦您部署了您的函数，您就可以使用 curl 来测试它:

```
curl -X POST -d “affix” [https://gateway.faas.kfj.io/function/medium2json](https://gateway.faas.kfj.io/function/medium2json)
```

> curl -X POST -d "词缀"[https://gateway.faas.kfj.io/function/medium2json](https://gateway.faas.kfj.io/function/medium2json)
> [{ " Title ":" # FAAS Friday—构建无服务器微博…

以上 URL 目前已部署，请随意查看使用结果。

# TLDR；完整的功能代码