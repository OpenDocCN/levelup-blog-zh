# 在 JavaScript 运行时使用 Docker 环境变量

> 原文：<https://levelup.gitconnected.com/using-docker-environment-variables-at-javascript-runtime-cd6d77822608>

## *运行时可配置 JavaScript 应用程序的简单模式。*

![](img/1e42964c7384de3e395559c562c90243.png)

照片由[珍妮希尔](https://unsplash.com/@jennyhill?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 为什么？

Docker 提供的最大好处之一就是可移植性。
我们可以获取同一个映像，并以多种不同的方式在不同的地方运行它——支持这种可移植性的特性之一是在启动容器时，即在**运行时**配置环境变量的能力。

虽然像 Webpack 这样的工具允许我们将环境变量注入到我们的 JavaScript 应用程序中，但是 web 浏览器不能直接访问这些环境变量——这意味着这些变量只能在**构建时**注入。

这是一个问题——我们现在已经失去了 Docker 提供给我们的可移植性。所以，让我们把它找回来。

# 怎么会？

我用了两个步骤来实现这个目标。

1.  **将环境变量写入文件**

大多数 JavaScript 应用程序(如 React 应用程序)会在一个文件夹中包含一些静态资产，如徽标或其他媒体，通常称为`public`。因此，我们所做的就是简单地将我们的运行时环境变量添加到一个文件中，该文件将与我们的其他公共资产一起提供服务。

在 docker 运行时执行任务的一个好方法是使用一个[入口点](https://docs.docker.com/engine/reference/builder/#entrypoint)脚本，在本例中，我有一个 shell 脚本，它将我需要的环境变量写到一个 JSON 文件中。

一个简单的例子:

```
*#!/bin/sh*# get some vars from env and write to json
RUNTIME_CONF="{
  \"MY_API\": \"$MY_API\",
  \"PROXY\": \"$PROXY\" 
}"
echo $RUNTIME_CONF > public/config.json*# start my app* nginx -g "daemon off;"
```

**2。使用 JavaScript 从文件**中读取变量

当应用程序在 web 浏览器中初始化时，它可以像加载其他构建资产一样简单地用 JavaScript 获取`config.json`,并使用它来补充应用程序配置。

**奖励积分**

对这些变量进行一些验证也是一个好主意，以确保您的应用程序拥有成功引导所需的所有信息。

例如，如果您的应用程序**必须**知道`$MY_API`变量的值才能运行——您也可以检查它是否已在步骤#1 中定义，如果验证失败，则在运行时中止容器执行——这样就可以在容器编排层检测和处理问题，而不是潜在地部署一个处于不良状态的 JavaScript 应用程序(并在未知环境中运行，例如某人的浏览器)。