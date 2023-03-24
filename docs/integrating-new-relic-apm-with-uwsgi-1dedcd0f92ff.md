# 集成新遗迹 APM 和 UWSGI

> 原文：<https://levelup.gitconnected.com/integrating-new-relic-apm-with-uwsgi-1dedcd0f92ff>

![](img/a9b1bebc0a7c4e9a88ac9d5160c3667c.png)

信用——scnsoft.com

APM(应用程序性能管理)用于监控和管理软件应用程序的性能和可用性。

您可以使用它来计算诸如 API 花费的时间、运行数据库查询花费的时间、运行函数花费的时间等等。

要提高系统的性能，首先需要以某种方式测量性能，而 APM 正好可以用来做这件事。

市场上有不同的 APM，但在这里我们将讨论如何为您的 Django/Flask 应用程序集成 New Relic APM 和 UWSGI，所以让我们开始吧。

## 安装新的遗迹 python 库

```
pip install newrelic
```

## **创建一个新的遗迹配置文件**

您可以使用以下命令来生成一个新的 Relic 配置命令，只需用您帐户的许可证密钥替换您的-LICENSE-KEY。

`newrelic-admin generate-config YOUR-LICENSE-KEY newrelic.ini`

## 定义环境

现在，是时候定义您想要监控的不同环境了。例如，生产应用服务器、生产芹菜服务器、暂存服务器等。

为此，打开`newrelic.ini`文件并滚动到底部。您应该会看到一些虚拟环境，如开发和测试。

在这里，您可以这样定义您的环境:

```
[newrelic:production-app]
app_name = myapp (production)
monitor_mode = true[newrelic:production-celery]
app_name = myapp (celery)
monitor_mode = true
```

这里，`production-app`是您将作为环境变量`NEW_RELIC_ENVIRONMENT`传递给 UWSGI 配置的内容

`app_name`是您将在 New Relic APM 仪表板中看到的应用程序的名称，而`monitor_mode`表示 New Relic 是否会将有关您的应用程序的数据发送到 New Relic 收集器。

现在您已经完成了新的 Relic 配置，是时候对您的 UWSGI 配置进行更改了。在这里，我将介绍运行 UWSGI 的两种流行方式的集成。

# 使用 systemd

您需要在 systemctl 配置中进行两项更改。

systemd 的 uwsgi.service 文件

在**环境**选项中，您需要定义两个环境变量，如上面的配置文件所示:

1.  **新遗留配置文件** —新遗留配置文件的绝对路径。
2.  **NEW _ RELIC _ ENVIRONMENT**—配置文件中指定的环境名称。

在 **ExecStart** 选项中，您只需要在 UWSGI 命令前面加上`newrelic-admin run-program`

现在，因为这是一个 systemctl 配置，所以您需要给出`newrelic-admin`的绝对路径(一旦您用 pip 安装了 New Relic，New Relic admin 就可以作为二进制文件获得)。

现在，使用以下命令重新加载配置，并重新启动 uwsgi 服务:

`sudo systemctl daemon-reload
sudo systemctl restart uwsgi`

# 使用主管

像这里的 systemctl 一样，您也需要做两处更改。

主管的 uwsgi.conf 文件

在**环境**选项中，您需要像上面的配置文件一样定义两个环境变量— **NEW_RELIC_CONFIG_FILE** 和 **NEW_RELIC_ENVIRONMENT**

在**命令**选项中，您只需要在 UWSGI 命令前面加上`newrelic-admin run-program`(指定绝对路径)

现在，使用以下命令重新启动 supervisor:

`sudo systemctl restart supervisor`

您也可以使用 **supervisorctl** 命令。

可能需要 15-20 分钟才能看到 New Relic APM 仪表板中的数据。如果没有收到任何数据，请检查 UWSGI 日志进行调试。

如果您使用管理程序运行 **celery** ,那么您可以遵循管理程序配置的相同步骤(定义环境变量和前置 New Relic Admin ),它应该可以很好地工作。

我希望你能够配置新遗物，而不是像我一样花两个小时挣扎:P
如果你正面临一些问题，请在评论中告诉我！

非常感谢你阅读这篇文章。如果你喜欢它，请给它一些掌声，让更多的人从中受益！