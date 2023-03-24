# 如何处理 Express 和 Node.js 应用程序中的错误

> 原文：<https://levelup.gitconnected.com/how-to-handle-errors-in-an-express-and-node-js-app-cb4fe2907ed9>

![](img/bf1766a1b578b79f323a34dde80aa504.png)

图片由[穆罕默德·哈桑](https://pixabay.com/users/mohamed_hassan-5229782/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=3085712)从[皮克斯拜](https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=3085712)拍摄

当我们用 Express 创建 API 时，我们定义了`routes`和它们的`handlers`。在理想的情况下，我们的 API 的消费者将只对我们定义的路由发出请求，并且我们的路由将正确无误地工作。但是如果你注意到了，我们并不是生活在一个理想的世界里。Express 知道这一点，并使处理我们的 API 中的错误变得轻而易举。

在这篇文章中，我将解释如何在 Express 中处理错误。要继续操作，请克隆这个[库](https://github.com/Olusamimaths/Express-Error-Handling-Tutorial)。克隆回购后记得要`npm install`。

存储库有一个 JavaScript 文件，`index.js`，包含以下内容:

如果你不想克隆回购，创建一个新的文件夹，`npm init -y`，然后`npm i --save express`。在这个文件夹中创建`index.js`并将代码粘贴到其中。

# 误差来源

在 Express app 中有两种基本的出错方式。

一种方式是向没有定义路由处理器的`path`发出请求。例如，`index.js`定义了两条`get`路线(一条到`/`和`/about`)。我使用了`get`路线，这样我们可以在浏览器中轻松测试路线。请注意，路由定义了一个`path`和一个中间件函数，当向该路径发出请求时，将调用该函数:

```
app.HTTPMethod(path, middleware)
// HTTPMethod = get, post, put, delete …
```

错误的另一个来源是当我们的路由处理器或代码中的其他地方出错时。例如，将“index.js”中的第一条路线更新如下:

```
…
app.get(‘/’, (req, res, next) => {
 // mimic an error by throwing an error to break the app!
 throw new Error(‘Something went wrong’);
 res.send(‘Welcome to main route!’)
})
…
```

重启服务器并访问`localhost:3000`，你会看到一个错误和一个堆栈跟踪。

# 通过路由排序处理路由错误

删除`index.js`中抛出错误的语句。启动服务器并在浏览器中访问`localhost:3000`,您应该会看到消息:

```
Welcome to the main route!
```

来访`localhost:3000/about`:

```
This is the about route!
```

# Express 如何查找路线？

Express 创建了一个被称为**的路由表**，它按照代码中定义的顺序放置路由。当请求进入 web 服务器时，URI 会在路由表中运行，并使用路由表中的第一个匹配项，即使匹配项不止一个。

如果没有找到匹配，那么 Express 会显示一个错误。要查看实际效果，请访问`localhost:3000/contact`，浏览器显示:

```
Cannot GET /contact
```

在检查路由表后，Express 没有找到与`/contact`匹配的条目，因此它以一个错误响应。

# 集中式错误处理:如何利用路由顺序

因为当在路由表中找不到给定 URI 的匹配时，Express 会显示错误消息，这意味着我们通过确保该路由是路由表中的最后一条来定义处理错误的路由。错误路由应该匹配哪条路径？

因为我们不知道用户将请求的不存在的路径，所以我们不能将路径硬编码到这个错误路由中。我们也不知道请求可能使用哪种 HTTP 方法，因此我们将使用`app.use()`而不是`app.get`。

在`app.listen()`之前，通过将以下路线放在路线声明的末尾来更新`index.js`:

```
…
// this matches all routes and all methods i.e a centralized error handler
app.use((req, res, next) => {
 res.status(404).send({
 status: 404,
 error: ‘Not found’
 })
})app.listen(port …
```

重启服务器并访问未定义的路径，例如`localhost:3000/blog`

现在，我们有一个自定义的错误响应:

```
{“status”:404,”error”:”Not found”}
```

请记住，路线的顺序对这一工作非常重要。如果这个错误处理路由位于路由声明的顶部，那么每个路径(有效的和无效的)都将与之匹配。我们不希望这样，所以错误处理路径必须在最后定义。

# 处理路由处理程序中的错误

如前所述，路由处理程序只是 JavaScript 函数(如果我们正在编写 OOP，它们也可以是类方法)，就像任何其他函数一样，我们可以使用`try catch`状态来处理错误。

`try catch`语句标记了一个要尝试的语句块(在`try`块中),然后指定了一个响应(在`catch`块中),以防出现异常。JavaScript 中的异常在语法上等同于错误。

通过更新中间件功能来更新`index.js`:

如果您在中间函数中执行任何异步操作，

# 处理任何类型的错误

如果我们只想处理对不存在的路径的请求的错误，那么上一节的解决方案是可行的。但是它不处理我们的应用程序中可能发生的其他错误，这是一种不完整的处理错误的方法。它只解决了问题的一半。

更新`index.js`以在第一个 get 路径中抛出错误:

```
…
app.get(‘/’, (req, res, next) => {
 throw new Error(‘Something went wrong!’);
 res.send(‘Welcome to main route!’)
})
…
```

如果你访问`localhost:3000`，你仍然会看到默认错误处理程序的响应。

# 定义错误处理中间件

错误处理中间件函数的声明方式与其他中间件函数相同，只是它们有四个参数，而不是三个。例如:

```
// error handler middleware
app.use((error, req, res, next) => {
 console.error(error.stack);
 res.status(500).send(‘Something Broke!’);
})
```

将这段代码放在`index.js`中`app.listen`前`app.use`后的路由声明之后，重启服务器后访问 localhost:3000。现在的回应是:

```
Something Broke!
```

现在，我们正在处理这两种类型的错误。啊哈！

这是可行的，但是我们能改进它吗？。是的。怎么会？

当您将一个参数传递给`next()`时，Express 会认为这是一个错误，它会跳过所有其他路由，并将传递给`next()`的内容发送给已定义的错误处理中间件。

更新`index.js`:

```
…
app.use((req, res, next) => {
 const error = new Error(“Not found”);
 error.status = 404;
 next(error);
});// error handler middleware
app.use((error, req, res, next) => {
  res.status(error.status || 500).send({
   error: {
   status: error.status || 500,
   message: error.message || ‘Internal Server Error’,
  },
 });
});
…
```

处理错误请求的中间件功能现在移交给错误处理器中间件。`next(error)`暗示:‘嘿，错误处理者先生，我有一个错误，处理它！’。

为了确保您与我在同一页上，行`error.status || 500` 暗示如果错误对象没有状态属性，我们使用 500 作为状态代码。`index.js`的完整内容是:

如果您提供的是静态页面，而不是发送 JSON 响应，逻辑还是一样的。您只需要改变错误处理程序中发生的事情。例如:

```
app.use((error, req, res, next) => {
 console.error(error); // log an error
 res.render(‘errorPage’) // Renders an error page to user!
});
```

如果你觉得这篇文章很有帮助，请与你的朋友和追随者分享，并查看我的其他帖子。

你可以在推特上关注我

黑客快乐！

*如果你点击拍手图标 50 次，你认为会发生什么？试试吧！*

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)