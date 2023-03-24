# 带有 Axios 和 TypeScript 的单例用例

> 原文：<https://levelup.gitconnected.com/use-case-of-singleton-with-axios-and-typescript-da564e76296>

## 在 TypeScript 中使用 singleton 的真实用例

![](img/a6f9321622302e8867d77c743a600a1e.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

你好世界！今天我要告诉你关于`axios`和`singleton`的事情。作为一个基础，我会拿我以前的关于这个话题的文章[。因此，如果您遗漏了代码的某个部分，或者有什么不清楚的地方，请检查第一部分](/enhance-your-http-request-with-axios-and-typescript-f52a6c6c2c8e)。

# 介绍

从上一篇文章或者从你的背景中，你可能知道`[axios](https://www.npmjs.com/package/axios)`是最受欢迎的 HTTP 请求库之一。它可以在客户端和服务器端使用。它提供了一些工具来减少代码量，使其清晰易读。创建实例的工具之一是`axios.create`。它可以帮助你定义一些常见的人员:基本的网址，标题等。

# 目前我们知道些什么？

前一篇文章描述了我们如何遵循 DRY 原则并扩展一个标准的`axios-instance`来得到更强大的东西。

长话短说，我们有以下代码:

这是一个抽象类，包含了关于我们的`axios-instance`以及错误和成功响应处理程序的逻辑。

在现实世界中，它可能看起来像:

好了，现在是时候提升我们的档次了。

# 问题是

当前的实现存在问题。如果我们需要在项目的不同地方使用我们的类，我们有几种方法来处理它。

*   第一种(从我的角度来看也是最正确的)是在`main`函数中初始化类，并将其传递给其他类的构造函数，这里可能需要`HttpModule`。这是一个非常明显的解决方案，但是只有当你用面向对象的方法开发服务器时，它才可能起作用。如果你开发一个客户端应用程序，这种方法不能使用。
*   第二种方法是在需要的时候导入我们的类，每次在每个模块/函数中创建一个实例。这种方法同时适用于服务器和客户端应用程序。你可能会问“既然它在任何地方都有效，为什么我们不使用它呢？”。答案在下面。

实际上，当我们使用`axios-instance`时，我们可以防止`axios`在每次发出请求时创建不必要的实例。我们做一次就一直用。但是如果我们创建不同的`HttpModule`实例，这和使用`axios.request`是一样的。唯一的区别是我们的包装。关键的解决方案是`singleton`。

# 一个

`[Singleton](https://en.wikipedia.org/wiki/Singleton_pattern)`是一种只允许使用一个类“实例”的模式。单一实例正是我们要找的，不是吗？实际上，这种模式是一个颇有争议的话题。此外，有人说这种模式是反模式。好吧，随它去吧。用不用完全是你自己的决定，但是我想让你知道有这样一种做法，可以让我们接受。

# 履行

首先，我们必须将`constructor`访问修饰符改为`private`。此步骤将阻止创建实例的能力:

此外，我们需要一个属性来缓存我们的单个实例。应该是`private`(所以我们不能在类外获取)和`static`(不用创建实例就可以使用):

最后但同样重要的是创建方法，该方法返回实例(`static`和`public`):

这是我们的`MainApi`类:

因为我们已经更新了类，所以我们可以在项目的不同模块中使用它，并且不用担心我们会创建不必要的实例:

```
import MainApi from '@/api/main';const mainApi = MainApi.getInstance();
```

# 摘要

我相信你发现了新的东西或者至少巩固了你的知识。我不提倡您使用`singleton`或将其视为反模式。在我看来，`singleton`和`HttpClient`只是你自己“工具箱”里的好东西。