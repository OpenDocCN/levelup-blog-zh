# 用 Ruby、Rack 和函数式编程编写小型 web 服务

> 原文：<https://levelup.gitconnected.com/writing-a-small-web-service-with-ruby-rack-and-functional-programming-a16f802a19c0>

![](img/ed7dfcc9d785ece06cd92065a0f992cf.png)

**UPD。**请阅读我的[个人博客](https://nondv.wtf/blog/posts/writing-a-small-web-service-with-ruby-rack-and-fp.html)上的帖子

我爱露比。我也喜欢面向对象编程。然而，现在函数式编程越来越受到重视。这并不奇怪，因为函数范式有很多优点。

作为一种多范例语言，Ruby 允许我们以函数式风格编写程序。让我们看看是否可以这样编写一个 web 应用程序。也许我们最终会发明一个网络框架；)

# 基本规则

让我们建立一些基本规则。

1.  兰姆达斯。到处都是兰姆达斯
2.  数组。我们可以使用数组和一些可枚举的方法，比如`map, reduce, find, select, reject`
3.  哈希。我们可以通过`[key]`访问值，并使用`merge`来修改它们(没有突变！)
4.  将外部依赖性(如机架实用功能)保持在最低限度。

# 行李架

> Rack 为用 Ruby 开发 web 应用程序提供了一个最小的、模块化的、适应性强的接口。

Rails 和 Sinatra 使用 Rack。你可以在这里了解更多:[https://github.com/rack/rack](https://github.com/rack/rack)

Rack 期望应用程序是一个具有接受`env`(包含关于请求的所有信息的散列)并返回 3 元素元组的`call`方法的对象:

`[status_code, headers_hash, body_array]`

(主体数组通常是字符串列表)

这很棒，因为它使用了基本的数据结构，而`call`方法表明我们可以将 lambda/proc 用作应用程序。

# 设置

我们先设置一个 app。

## Gemfile

用`bundle init`生成一个 gem 文件，并向其中添加 Rack。

```
source "[https://rubygems.org](https://rubygems.org)"
git_source(:github) {|repo_name| "[https://github.com/#{repo_name](https://github.com/#{repo_name)}" }gem "rack"
```

## 配置. ru

`config.ru`是由 Rack 附带的`rackup`程序运行并启动 web 服务器的脚本。

Rack reloader 将重新加载我们的应用程序，无需重启服务器。

我们还将假设我们的应用程序源代码将使用我们的应用程序 lambda 定义`APP`常量。我们还需要将它包装到另一个 lambda 中，否则 Rack 不会拾取对常量的更改。

## app.rb

现在我们可以用`bundle exec rackup`启动 web 服务器。它应该可以在 [http://localhost:9292](http://localhost:9292) 上找到

# 按指定路线发送

让我们为`/hello`添加一条显示“Hello world”消息的路线。还有，404 页。

## 经理人

让我们把之前的 lambda 从 APP 移到一个 handler 变量。

## 路线

让我们将路由定义为一组条件+处理程序对:

我们也可以从路线中创建一个数据类型。为此，我们应该创建一个函数`route(matcher, handler)`和 getter 函数`route_matcher(route)`和`route_handler(route)`，而不是显式地将它们写成对。这样，代码变得更加灵活，因为它不太了解数据结构的实现。然而，让我们保持简单。

## 路由器

路由器是一个调度功能，它决定一个请求使用哪个处理器。

# 中间件

让我们让用户提供他们的名字来恰当地问候他们。

为此，我们需要一个`params`散列。

## Params 中间件

中间件只是一个处理程序的包装器，用于修改`env`或从其返回的结果。
让我们创建一个中间件，在将 env 交给处理程序之前，将`params` hash 添加到 env 中。

## 负责人

让我们在处理程序中使用新的参数。

现在我们应该可以在这里看到一个问候语:

[http://localhost:9292/hello？name=John+Doe](http://localhost:9292/hello?name=John+Doe)

## 内容类型标题

让我们也添加`text/html`内容类型头。

## 列表中间件

现在，我们正在添加一个中间件，方法是在一个处理程序上直接调用它。如果我们能把它们列出来，以后再申请，那就更好了。

身份功能很棒。将它与另一个函数组合在一起没有任何效果，所以它是 reducing 的一个很好的初始值。

# 结论？

我们已经实现了一个小型的 web 应用程序。虽然它没做多少。然而，有趣的是，仅仅使用 lambdas、数组和散列，我们能走多远。

数据库交互会给我们的代码带来副作用。作为一种想法，它们可以被放入方法中，这样就更容易将它们与纯 lambdas 区分开来。

我们也可以通过把基本的东西移到散列中，并把它们赋给常量，来制作一个框架。举个例子，

你可以在这里找到完整的源代码[。](https://github.com/Nondv/ruby-experiments/tree/master/functional_rack)