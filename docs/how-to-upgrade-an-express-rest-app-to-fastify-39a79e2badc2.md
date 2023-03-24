# 如何将 Express REST 应用程序升级到 Fastify

> 原文：<https://levelup.gitconnected.com/how-to-upgrade-an-express-rest-app-to-fastify-39a79e2badc2>

![](img/6e77ac87cd745d68485cf5ac18e0d2a4.png)

David Fekke 的图片说明

*原发布于*[*https://fek . io*](https://fek.io/blog/how-to-upgrade-an-express-rest-app-to-fastify/)*。*

不久前，我为 NOAA 的航空气象服务写了一份快速而肮脏的航空气象代理。现有的天气服务以 XML 格式返回数据。因此，我创建了一个 express 应用程序，代理来自该服务的两个 web 方法，这样它们将返回 JSON 而不是 XML。

我写的原始代理服务是一个简单的 [express](https://expressjs.com/) app。去年，在疫情启动期间，我开始寻找替代框架。近几年来，快餐变得越来越受欢迎。

![](img/77fe516a950b3d959d53272cf2aa0e1c.png)

Fastify 也是一个更现代的编写 web 应用程序的框架。它有一些独特的特性，比如用于请求和响应的 JSON 模式。它也有自己的 JSON 解析器。express 使用的是 V8 中使用的默认解析器。

下面是托管这两种方法的原始 express 应用程序的样子；

从这个例子中可以看出，我使用`[axios](https://axios-http.com/)`作为我的 HTTP 客户端。我更喜欢 axios 而不是 node-fetch，因为 axios 有一些可用的选项，而且它不需要解析两个承诺。

# 升级到 Fastify

我想做的第一个改变是在 commonjs `require`方法上使用新的模块导入语法来导入我的模块。在 node.js 14 版本和更高版本中，您可以通过将以下键添加到您的`package.json`中来默认打开这个功能。

```
"type": "module"
```

如果您没有在`package.json`中指定类型，Node.js 仍然默认为‘common js’。

# 添加 Fastify 模块

现在你可以使用 NPM 或纱添加 Fastify 模块。我还使用了 CORS，所以我们可以通过运行下面的命令来添加它并同时更新这些依赖项的 package.json 文件；

```
> npm i fastify fastify-cors --save
```

这里我使用了`i`的快捷方式用于`install`，并且我使用了 NPM 的`--save`标志将两个模块保存到`package.json`文件中。您的`package.json`文件应该在`dependency`部分包含以下模块。

```
 "axios": "^0.18.0",
    "cors": "^2.8.5",
    "express": "^4.16.3",
    "fast-xml-parser": "^3.12.5",
    "fastify": "^3.15.0",
    "fastify-cors": "^5.2.0"
```

# 向我们的应用程序添加导入语句

将以下导入语句添加到我们的应用程序的开头。

此时，我们可以创建我们的`Fastify`对象，并注册我们的应用程序中需要的任何插件。之前我用的是 CORS，所以一旦对象为`Fastify`实例化了，我就会注册那个插件。

通常，在大多数 Node.js web 应用程序中，路由是在它们自己的模块或插件中与主应用程序文件分开配置的。这将使应用程序更松散地耦合，更容易测试。对于这个例子，我将在主应用程序所在的同一个文件中配置这两个方法。

这两个路由现在都使用`async`函数处理程序，所以我可以使用 async/await 语法。我也可以使用`return`，而不是必须使用`reply.send(response)`。

# 启动应用程序

一个启动 fastify 托管应用程序的常见例子是，我们设置了一个异步的启动方法。Fastify 在他们的文档中也有这个。

完成 express 应用程序的更新后，最终的应用程序应该是这样的。

# 结论

从这个例子中可以看出，Fastify 应用程序的结构与 Express 应用程序没有什么不同。对于这样一个简单的应用程序，这是一个可以升级到 Fastify 的应用程序的好例子。