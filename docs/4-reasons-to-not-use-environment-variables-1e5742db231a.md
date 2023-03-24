# 不使用环境变量的 4 个理由

> 原文：<https://levelup.gitconnected.com/4-reasons-to-not-use-environment-variables-1e5742db231a>

环境变量是配置软件的一种简单方法。它们允许您在 shell 中设置一个变量，这样您运行的任何进程都可以使用提供的值。

这对 CLI 工具非常有帮助，但不应该用于管理复杂软件的配置。

为了理解为什么，我们需要看看使用环境变量进行配置的缺点。

![](img/729da96c7a8c7664e55e0f28e274fc5f.png)

环境与文件

# 1.所有类型都必须从字符串中解析

环境变量总是被设置为字符串。这意味着任何需要在软件中设置为另一种类型的值都需要从字符串中解析出来。

例如，如果我想从 python 中的环境变量“FOO”中得到一个整数，我需要运行下面的代码:

```
import os
foo = int(os.environ['FOO'])
```

![](img/0e651e156e5c0e3271187c3736d7f897.png)

解析类型

这需要理解编程语言如何将字符串解析成适当的数据类型，并且需要比直接使用原生数据结构更多的测试。这个问题还扩展到包括文件。

如果您想配置 ssh 密钥或 ssl 证书之类的文件，那么使用环境变量会因为换行和格式化而变得非常混乱。

# 2.没有嵌套或内置结构

环境变量是全局值，这意味着它们可能会有命名冲突。例如，如果您需要为多个下游服务设置“URL ”,则需要在它们前面加上前缀，以防止像这样的冲突:

```
FOO_URL=x.com
BAR_URL=y.com
```

这种缺乏结构的情况会导致长名称，如果不小心的话，可能会无意中与其他服务或功能重叠。

它还防止配置值的封装和分组。相反，使用像 JSON 或 YAML 这样的结构化数据可以让您传递多组键，而无需担心配置的父结构。

# 3.流程内的全局访问

好的软件工程尽量减少全局状态的使用，这样代码就容易测试和跟踪。环境变量没有——它们积极鼓励使用全局状态，并使从任何地方获取值变得非常容易。代码越深入，注入配置和运行测试就越困难。

```
import os def a(): 
  foo = int(os.environ['FOO']) 
  return foo * 42 def b(foo): 
  return foo * 42
```

在上面的 python 例子中，函数“a”比函数“b”更难测试，因为我们已经将配置管理从纯功能中分离出来。这不会因为避免环境变量而消失，但以我的经验来看，这是一个促成因素。

# 4.需要在运行时设置

在运行应用程序之前，需要在流程中设置环境变量。这增加了启动和管理流程的复杂性。如果您执行从文件中提取配置的 python 应用程序，则不需要额外的 shell 命令:

```
python app.py
```

但是，如果您需要设置环境变量，则需要将其编写到环境的特定外壳中:

```
FOO=BAR && python3 app.py
```

# 摘要

环境变量是开始管理软件配置的一个很好的方法，但是任何复杂的软件都应该用基于文件的配置来代替环境变量的使用，比如 JSON 或 YAML。

这使得管理配置更加简单，并且支持类型、测试和流程管理的更成熟的实现。

关于如何实现配置文件的例子，请参考:[如何使用特征标志](http://torvo.com.au/articles/how-to-use-feature-flags)。

如需更多类似内容，请关注或联系我:

*   **推特:** [@BenTorvo](https://twitter.com/BenTorvo)
*   **邮箱:**[ben@torvo.com.au](http://torvo.com.au/)
*   **网址:**[torvo.com.au](http://torvo.com.au/)

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)