# NodeJS —带有 VHost 的虚拟域

> 原文：<https://levelup.gitconnected.com/nodejs-virtual-domains-with-vhost-ce2d039db040>

## 使用 vhost 中间件创建虚拟域

![](img/d5fbd508c07735ba20157f23671fcc05.png)

# 首先，什么是 VHost？

`vhost`是一个 NodeJS 中间件包，作为一个简单的管理器，根据请求的主机名将流量分段到特定的服务。更常见的是，你会看到在 Express 服务器中使用的`vhost`，因为它通过`Express.vhost`与 **Express** 标准库捆绑在一起。

看 github 上的`vhost` [源码，代码其实挺简单的。它所做的就是读取`req.headers.host`值，然后有一些帮助函数用于对解析后的主机名进行**正则表达式**验证。**在其核心，该包根据指定的 RegEx** 验证请求主机名。](https://github.com/expressjs/vhost/blob/master/index.js)

尽管它的实现很简单，但它允许您的服务器组织和限制不同主机名的 HTTP 流量。

# 应该在什么时候使用？

服务器上`vhost`中间件的一些常见用途:

*   您的服务器需要以不同的方式处理来自不同主机名(包括子域)的请求。
*   您希望限制来自某些路由的传入流量(尽管可以通过其他方式实现)。
*   您可以创建多个主机名来避免浏览器上的“最大主机连接数限制”。
*   您希望根据请求主机名添加/删除/更新特定的请求属性(如自定义标题)..

# 简单的 VHost 示例

这个例子展示了如何通过虚拟主机名(在这个例子中是一个子域)将应用程序分段，并将它们路由到一个特定的服务。

```
const **connect** = require('**connect**')
const **vhost** = require('**vhost**')
const **server** = **connect**()const **emailApp** = **connect**()

const **userApp** = **connect**()

**server**.use(**vhost**('email.local', **emailApp**))

**server**.use(**vhost**('*.user.local', **userApp**))

**server**.listen(3000)
```

这个简单的服务器将只对与`email.local`匹配的主机的请求使用`emailApp`，而`userApp`将只处理来自子域`*.user.local`的请求。

存储库中记录了几个类似的例子:
【https://github.com/expressjs/vhost/blob/master/README.md】T21

**中间件还为请求对象添加了各种** `.vhost` **属性，如下所示。**

```
**app**.use(**vhost**('*.*.example.com', (**req**, res, next) => {
  console.log(req.**vhost**.**host**) // => 'foo.bar.example.com:8080'
  console.log(req.**vhost**.**hostname**) // => 'foo.bar.example.com'
  console.log(req.**vhost**.**length**) // => 2
  console.log(req.**vhost[0]**) // => 'foo'
  console.log(req.**vhost[1]**) // => 'bar'
}))
```

您可以看到`vhost`中间件已经很好地解析了主机，以便在处理程序中更容易使用。

# 结论

希望这篇文章能帮助你认识到`vhost`中间件的简单性。“**虚拟域名**的概念可能听起来令人生畏，但在实践中，我们能够看到它们是多么简单。它提供了一种简单干净的方式来管理不同的主机如何与你的服务器通信。