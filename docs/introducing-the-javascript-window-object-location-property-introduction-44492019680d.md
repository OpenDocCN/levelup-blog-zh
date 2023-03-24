# JavaScript 中 document.location 属性和 location 对象的完整指南

> 原文：<https://levelup.gitconnected.com/introducing-the-javascript-window-object-location-property-introduction-44492019680d>

![](img/33cb4b6952e5a6fb779fd89152bfd481.png)

[Ales Krivec](https://unsplash.com/@aleskrivec?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

`window`对象是一个全局对象，它具有与当前 DOM 文档相关的属性，这些属性在浏览器的选项卡中。

在本文中，我们将查看`window.document`对象的属性，包括`window.document.location`对象的属性。

# 窗口.文档.位置

`document.location`是一个只读属性，返回一个`Location`对象，它是关于当前文档的 URL 的信息，并为我们提供了改变和加载 URL 的方法。

即使它是一个只读的`Location`对象，如果我们给它分配一个字符串，它将在字符串中加载 URL。

例如，如果我们想要加载`'https://medium.com'`，我们可以直接将它赋给`document.location`属性，如下面的代码所示:

```
document.location = '[https://medium.com'](https://medium.com');
```

这与将相同的 URL 分配给`document.location.href`属性是一样的:

```
document.location.href = '[https://medium.com'](https://medium.com');
```

两段代码都将加载 https://medium.com。`Location`对象具有以下属性:

*   `Location.href`
*   `Location.protocol`
*   `Location.host`
*   `Location.hostname`
*   `Location.port`
*   `Location.pathname`
*   `Location.search`
*   `Location.hash`
*   `Location.username`
*   `Location.password`
*   `Location.origin`

## `Location.href`

`location.href`属性是一个包含完整 URL 的字符串。我们都可以用它来获取当前页面的 URL，并设置 URL 以便我们可以转到下一个页面。例如，如果我们有:

```
console.log(location.href);
```

然后我们得到完整的 URL，大概是这样的:

```
[https://fiddle.jshell.net/_display/](https://fiddle.jshell.net/_display/)
```

我们也可以用它来转到不同的页面。例如，我们可以写:

```
document.location.href = '[https://medium.com'](https://medium.com');
```

去媒体网站。它的作用与以下内容相同:

```
document.location = '[https://medium.com'](https://medium.com');
```

如果当前文档不在浏览上下文中，那么这个属性的值是`null`。

## `Location.protocol`

我们可以使用`protocol`属性来获取 URL 的协议部分，这是 URL 在第一个冒号(`:`)之前的第一部分。例如，我们可以在下面的代码中使用它:

```
console.log(document.location.protocol);
```

然后我们得到 HTTPS 网页的`https:`和 HTTP 网页的`http:`。我们也可以像下面的代码一样设置协议:

```
document.location.protocol = 'http';
```

如果运行上面的代码，浏览器将尝试转到当前页面的 HTTP 版本。

## 位置.主机

`host`属性包含主机名的字符串。如果有带主机名的端口，也将包括在内。例如，我们可以像下面这样检索主机名:

```
console.log(document.location.host);
```

然后我们从`console.log`语句中得到类似于`fiddle.jshell.net`的东西。我们也可以设置`host`属性。如果我们编写类似下面的代码:

```
document.location.host = 'medium.com';
```

然后，浏览器会将当前页面的主机名切换为新的主机名，并尝试加载带有新主机名的 URL。

## 位置.主机名

`hostname`属性是一个字符串属性，包含 URL 的域。例如，我们可以通过运行以下命令来获取域:

```
console.log(document.location.hostname);
```

然后我们从`console.log`语句中得到类似`fiddle.jshell.net`的东西。我们也可以设置`host`属性。如果我们编写类似下面的代码:

```
document.location.hostname = 'medium.com';
```

然后，浏览器将使用新的域名切换当前页面的域名，并尝试使用新的主机名加载 URL。

![](img/56dcbe06f8641d373909499a959e61f4.png)

[丘特尔斯纳普](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

## 位置.港口

属性让我们获取并设置 URL 的端口。它是一个字符串属性。如果 URL 没有端口号，那么它将被设置为空字符串。例如，如果我们有:

```
console.log(document.location.port);
```

如果端口是 3000，我们会得到类似于`“3000”`的结果。我们也可以设置`host`属性。如果我们编写类似下面的代码:

```
document.location.port = '3001';
```

然后，浏览器会将当前页面的端口切换为新的端口，并尝试加载带有新端口号的 URL。

## 位置.路径名

`pathname`属性是一个字符串属性，它的斜杠后面是 URL 的路径，即域之后的所有内容。如果没有路径，该值将是一个空字符串。例如，如果我们有:

```
console.log(document.location.pathname);
```

然后我们得到类似于`“/_display/”`的东西。我们还可以设置`pathname`属性。如果我们编写类似下面的代码:

```
document.location.pathname = '3001';
```

然后，浏览器会将当前页面的路径切换为新的路径，并尝试加载新路径的 URL。

## 位置.搜索

`search`属性是一个字符串属性，它为我们获取查询字符串。查询字符串是 URL 中位于`?`之后的部分。例如，我们可以通过运行以下命令来获取当前加载页面的 URL 的查询字符串部分:

```
console.log(document.location.search);
```

然后我们会得到这样的结果:

```
"?key=value"
```

如果我们有一个像 [http://localhost:3000/？key=value](http://localhost:3000/?key=value) 。为了解析和操作查询字符串，我们可以将它转换成一个`URLSearchParams`对象。如果我们想使用不同的查询字符串访问一个 URL，我们可以像下面的代码一样给`document.location.search`属性分配一个新的查询字符串:

```
document.location.search = '?newKey=newValue';
```

那么我们的浏览器选项卡的新 URL 将是 [http://localhost:3000/？newKey=newValue](http://localhost:3000/?newKey=newValue) 。

## 位置.哈希

属性让我们获取并设置井号(`#`)之后的 URL 部分。哈希没有被完全解码。如果没有散列片段，那么这个值将是一个空字符串。例如，我们可以通过运行以下命令来获取当前加载页面的 URL 的查询字符串部分:

```
console.log(document.location.hash);
```

然后我们会得到这样的结果:

```
"#hash"
```

如果我们有一个像 [http://localhost:3000/？key=value](http://localhost:3000/#hash) 。如果我们想使用不同的查询字符串访问一个 URL，我们可以像下面的代码一样给`document.location.hash`属性分配一个新的查询字符串:

```
document.location.hash= '#newHash';
```

那么我们的浏览器选项卡的新 URL 将是 [http://localhost:3000/？newKey=newValue](http://localhost:3000/#newHash) 。

## 位置.用户名

`username`属性获取作为字符串返回的 URL 的用户名部分，这是协议和冒号之间的部分。举个例子，如果我们去了[http://username:password @ localhost:3000/](http://username:password@localhost:3000/)，那么`document.location.username`就会得到我们`'username'`。如果我们像 wit 一样给它赋值如下代码:

```
document.location.username = 'newUsername'
```

那么新页面就会是[http://](http://username:password@localhost:3000/)new username[:password @ localhost:3000/](http://username:password@localhost:3000/)。

## 位置.密码

`password`属性获取作为字符串返回的 URL 的用户名部分，这是协议和冒号之间的部分。举个例子，如果我们去了[http://username:password @ localhost:3000/](http://username:password@localhost:3000/)，那么`document.location.password`就会给我们带来`'password'`。如果我们像 wit 一样给它赋值如下代码:

```
document.location.password= 'newPassword'
```

那么新页面就会是 [http://](http://username:password@localhost:3000/) 用户名[:new password @ localhost:3000/](http://username:password@localhost:3000/)。

## 位置.来源

`origin`属性是一个字符串只读属性，它具有所表示的 URL 的来源。

对于带有`http`或`https`的 URL，那么它将包括协议，后跟`://`，后跟域，后跟冒号，如果明确指定的话，再后跟端口。

对于具有`file:`方案的 URL，该值取决于浏览器。对于`blob`URL，那么 URL 的原点将是跟随`blob:`的部分。例如，我们可以像下面的代码一样记录`origin`属性:

```
console.log(document.location.origin);
```

得到类似于:

```
[https://fiddle.jshell.net](https://fiddle.jshell.net)
```

`window.document.location`对象是一个拥有当前页面 URL 的对象。

`location`对象让我们知道当前页面的 URL 的各个部分，并对它们进行设置，以便浏览器切换出由属性名指定的部分，然后转到具有新 URL 的页面。

还有一些方法可以对当前加载的页面做各种事情，请继续关注本系列的下一部分。