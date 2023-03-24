# 如何给 FastAPI/uvicon 日志添加时间戳

> 原文：<https://levelup.gitconnected.com/how-to-add-timestamp-to-fastapi-uvicorn-logs-221f7f16a2ca>

通过添加时间戳简化调试过程

![](img/16a3b636611837b7049588a5ae801117.png)

维克多·塔拉舒克在 [Unsplash](https://unsplash.com/s/photos/file?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在本教程中，您将学习如何配置 Uvicorn 并向所有访问和调试日志添加时间戳。让我们看看下面的问题状态，以便更清楚地了解日志中有时间戳的好处。

给定 FastAPI 服务器的以下 Python 文件:

```
from fastapi import FastAPIapi = FastAPI()@api.get("/")
def index():
    return {"Hello": "World"}
```

通常的部署命令如下:

```
uvicorn main:app --host 0.0.0.0 --port 8000
```

当访问根端点时，您应该在终端上获得以下输出:

```
INFO:     Started server process [17028]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
INFO:     <IP> - "GET / HTTP/1.1" 200 OK
INFO:     <IP> - "GET / HTTP/1.1" 200 OK
```

如您所见，输出仅包含以下信息:

*   用户的 IP 地址
*   用户调用的端点(类型、http 版本、状态代码)

当您试图调试应用程序时，这可能会很麻烦。如果你能把它配置成包含你需要的东西，那就方便多了。例如，拥有时间戳非常有用，因为您可以使用它来确定某一天发生的问题。

让我们继续下一节，了解更多关于实现的信息。

# 履行

有多种方法可以修改日志的配置。您可以选择:

*   通过`logging`模块将配置指定为字典代码
*   创建一个 json 或 yaml 文件，并使用`--log-config`标志运行 uvicorn

本教程涵盖了使用 yaml 文件的第二个选项。这种方法更加灵活，因为您可以简单地将配置文件移动到您的新项目中。此外，你可以很容易地禁用或启用它通过`--log-config`标志。

## PyYaml

你需要额外安装一个叫 PyYaml 的包来使它工作。按照以下方式安装:

```
pip install pyyaml
```

或者，如果您安装了`uvicorn`的标准版本，这个包将作为依赖项组合在一起:

```
pip install uvicorn[standard]
```

## 配置文件

接下来，在您的工作目录中创建一个名为`log_conf.yml`的新文件。对于除版本 0.18.1 之外的所有`uvicorn`版本，在其中添加以下代码:

对于版本 0.18.1，`logging`模块[已被重命名，以防止与标准库](https://github.com/encode/uvicorn/pull/1426)冲突。

> 开发人员从版本 0.18.2 开始恢复了这个特性。建议将`uvicorn`更新至最新版本，并使用`uvicorn.logging`作为模块名称。

0.18.1 版本使用`_logging`作为模块名。因此，新的配置文件应该如下所示:

该文件包含以下所有相关配置:

*   格式化程序
*   经理人
*   记录器

每个组件将进一步分为默认组件或访问组件。您可以选择设置不同的配置或保留相同的设置。

## 格式化程序

最重要的部分是关于格式化程序。在上面的脚本中，它被设置为:

```
datefmt: "%Y-%m-%dT%H:%M:%S"
format: '[%(asctime)s.%(msecs)03dZ] %(name)s %(levelname)s %(message)s'
```

`datefmt`指的是 [Python 日期格式字符串](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-format-codes)，可以用来定义你的日期时间字符串的格式。

```
%Y-%m-%dT%H:%M:%S
```

在这种情况下，它将显示如下输出:

```
2021-11-29T13:22:40
```

不要被中间的 T 字符吓到，因为它是日期时间的 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准的一部分。示例输出仍然需要以微秒和 Z 字符结尾，以便与标准完全兼容。然而，如下设置将不起作用，因为 Python3 不支持它，尽管它在官方文档中有说明。

但是，您仍然可以通过利用[日志记录属性](https://docs.python.org/3/library/logging.html#logrecord-attributes)来实现它，如下所示:

```
[%(asctime)s.%(msecs)03dZ] %(name)s %(levelname)s %(message)s
```

`asctime`将输出我们之前定义的日期时间字符串

```
.%(msecs)03dZ
```

将在它的末尾附加以下内容:

*   一个点
*   三个字符的毫秒值
*   Z 字符

## 运行服务器

保存文件并运行以下命令来启动 FastAPI 服务器:

```
uvicorn main:app --host 0.0.0.0 --port 8000 --log-config log_conf.yml
```

您应该在终端上得到以下输出:

```
[2021-11-29T08:48:33.632Z] uvicorn.error INFO:     Started server process [31514]
[2021-11-29T08:48:33.633Z] uvicorn.error INFO:     Waiting for application startup.
[2021-11-29T08:48:33.633Z] uvicorn.error INFO:     Application startup complete.
[2021-11-29T08:48:33.633Z] uvicorn.error INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
[2021-11-29T08:48:37.568Z] uvicorn.access INFO:     <IP> - "GET / HTTP/1.1" 200
[2021-11-29T08:48:40.561Z] uvicorn.access INFO:     <IP> - "GET / HTTP/1.1" 200
```

如您所见，新的日志为您提供了用户何时访问端点的更清晰的图像。您可以根据需要进一步定制它，以简化调试体验。

# 结论

让我们回顾一下你今天所学的内容。

本文从一个简单的问题开始，即 uvicorn 日志中缺少时间戳，这在调试过程中可能会产生问题。

接下来，它讲述了如何安装 pyyaml 包并创建相应的配置文件。随后，我们转到 formatters 部分，重点介绍 Python 日期时间字符串和日志记录属性。

最后，提供了一个使用配置文件和示例输出运行 uvicorn 的示例。

感谢你阅读这篇文章。祝你有美好的一天！

# 参考

1.  [StackOverflow —如何给 uvicorn 日志中的每个请求添加时间戳](https://stackoverflow.com/questions/62934384/how-to-add-timestamp-to-each-request-in-uvicorn-logs)
2.  [设置—uv icon](https://www.uvicorn.org/settings/#logging)