# Go 中环境变量的实用指南

> 原文：<https://levelup.gitconnected.com/a-no-nonsense-guide-to-environment-variables-in-go-55d7661f09b0>

环境变量是为软件应用程序设置配置值的最佳方式，因为它们可以在系统级定义，与软件无关。这是[十二因素应用](https://12factor.net/config)方法学的原则之一，它使应用程序的构建具有可移植性。

![](img/bd0392d0c1bea91a84f7998dc15b86d9.png)

# 使用环境变量

在 Go 中使用与环境变量交互所需要的只是标准的`[os](https://golang.org/pkg/os/)`包。下面是访问环境变量`PATH`的方法。

设置环境变量同样简单:

## 从. env 文件加载环境变量

在运行多个项目的开发机器上设置环境变量并不总是可行的。

[godotenv](https://github.com/joho/godotenv) 是 Ruby [dotenv](https://github.com/bkeepers/dotenv) 库的一个 Go 端口。这允许您在一个`.env`文件中定义应用程序的环境变量。

要安装软件包，请运行:

```
$ go get github.com/joho/godotenv
```

将您的配置值添加到项目根目录下的`.env`文件中:

```
GITHUB_USERNAME=craicoverflow
GITHUB_API_KEY=TCtQrZizM1xeo1v92lsVfLOHDsF7TfT5lMvwSno
```

然后，您可以在应用程序中使用这些值:

需要注意的是，如果系统中已经定义了一个环境变量，那么 Go 将使用这个变量而不是`.env`中的值。

# 将环境变量包装在配置包中

直接访问环境变量很好，但是现在不太容易维护，不是吗？每个值都是一个字符串，想象一下当环境键被修改时必须更新每个引用！

为了解决这个问题，让我们创建一个配置包，以更加集中和可维护的方式访问环境变量。

下面是一个简单的`config`包，它将在一个`Config`结构中返回配置值。我们可以选择定义一个缺省值，所以当一个环境变量不存在时，将使用这个缺省值。

1.  接下来，我们应该向“配置结构”添加不同的类型。当前的实现只能处理`string`类型，这对于较大的应用程序来说不太实用。

让我们添加处理`bool`、`slice`和`integer`类型的函数。

用这些环境变量更新您的`.env`文件。

```
GITHUB_USERNAME=craicoverflow
GITHUB_API_KEY=TCtQrZizM1xeo1v92lsVfLOHDsF7TfT5lMvwSno
MAX_USERS=10
USER_ROLES=admin,super_admin,guest
DEBUG_MODE=false
```

现在，您可以从应用程序的其余部分访问这些值:

# 现在你知道了

有几个库声称可以为您的 Go 应用程序提供配置“解决方案”。但是，当你自己也很容易做出一个解决方案时，这真的是一个解决方案吗？

您如何管理 Go 应用程序中的配置？

你喜欢这篇文章吗？请务必在 [https://endaphelan.me](https://endaphelan.me) 查看更多类似内容。

[![](img/ff5028ba5a0041d2d76d2a155f00f05e.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/golang) [## 学习围棋-最佳围棋教程(2019) | gitconnected

### 23 大围棋教程-免费学习围棋。课程由开发者提交和投票，使您能够找到…

gitconnected.com](https://gitconnected.com/learn/golang)