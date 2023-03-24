# 如何用 Python 自动化你的待办事项列表

> 原文：<https://levelup.gitconnected.com/how-to-automate-your-to-do-list-with-python-2da26b0ffc8f>

## 花几分钟写代码，再也不会忘记任务

![](img/7dd14e49dca58e84694fa04944b91d14.png)

照片由[凯瑟琳·拉威利](https://unsplash.com/@cathrynlavery?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

很容易忘记日常生活中的小事。当生活变得忙碌时，像给室内植物浇水、倒垃圾、清理电脑这样的小责任经常被遗忘。

即使作为一个以研究人类记忆为生的人，我仍然不擅长记录这样的事情。背景中有一个全球疫情对我的智力也没有帮助。

如果你像我一样，谢天谢地你可以用一点基本代码来解决这个问题。通过使用一个公共的在线待办事项列表和一个 Python 脚本，可以很容易地根据大量不同的条件设置自定义的重复提醒。只要你定期运行这个脚本，每天早上醒来就会看到一个为你量身定制的任务列表。没问题。

那么你需要做什么呢？

# 1.设置 Todoist

Todoist 是一个非常简洁的数字待办事项列表。它做了你想让待办事项清单做的一切:你可以分配任务优先级，按项目分组任务，设定截止日期，等等。虽然我不再使用 Todoist 作为我的主要组织工具，但它仍然是我最喜欢的独立待办事项应用程序。

首先，你要做一个 Todoist 账户。这很简单，不会花你太多时间。

一旦你这样做了，你就会想去访问 [Todoist API 文档站点](https://developer.todoist.com/sync/v8/?python)。在这里，您将看到使用 API 对您的帐户执行操作所需的所有说明。您可以使用 shell 命令`$ pip install todoist-python`通过 pip 安装 API。

接下来，您需要找到您的 Todoist 帐户的 API 令牌。这是一个独特的字符串，API 使用它来识别您的帐户。你可以登录 Todoist web 应用程序，点击右上角的个人资料图片，然后选择“整合”来找到它；您的 API 令牌在此屏幕上有清晰的标记。

最后，如果您是新手，我建议您尝试一下 Todoist。了解核心特性是如何工作的，可能有助于您了解以后要自动化什么样的任务。至少，创建一个新项目来存储您的自动化任务，并记下它的 ID。

有了这个设置，就该编码了。

# 2.在新脚本中设置 API

要在新脚本中设置 Todoist API，请打开新的 Python 脚本并输入以下代码:

```
from todoist.api import TodoistAPI
from datetime import datetime

api = TodoistAPI("XXXXXX")
api.sync()

print(api.state['projects'])
```

首先，您需要导入 Todoist API。您还需要导入`datetime`库，因为在为任务调度创建条件时，这很方便。

接下来，第三和第四行将创建 Python 脚本和 Todoist 帐户之间的连接。这里需要用上一步中惟一的 API 令牌替换字符串“X”。

一旦 API 与您的帐户同步，最后一行的代码将打印一些关于您的项目的信息。对于您的 Todoist 帐户中的每个项目，将在控制台中打印各种属性。在这些信息中，您需要知道的是您想要关联添加自动化任务的项目的`id`属性。在进行下一步之前，请务必记下这一点。

# 3.添加并提交任务

现在您已经设置了 API，您可以进入项目的核心:添加任务！

以编程方式添加待办事项列表项的目的是自动跟踪某些任务。在许多不同的情况下，您可能希望自动执行任务提醒，但是添加每隔几天重复一次的任务是一个简单的开始。下面，我列举了一个例子，告诉你如何设置每 30 天清理一次电脑的提醒。

```
todaysdate = int(datetime.now().timestamp() / 86400)

if (todaysdate) % 30 == 0:
    cleanpc = api.items.add("Clean your PC", project_id="1234567890", due={"string": "today"})

api.commit()
```

这里，首先在 [Unix 时间](https://en.wikipedia.org/wiki/Unix_time)中定义当前日期。这意味着您可以使用模数运算符(`%`)定义每三十天评估一次为真的条件。如果当前日期能被 30 整除，则条件的计算结果为 true。

在 if 语句中，您可以使用`api.items.add`向您选择的项目添加一个新任务。定义任务的名称(“Clean your PC”)，指定要将任务添加到的项目的 id，这样就完成了。在这个例子中，我还添加了一个截止日期。添加任务时，您还可以自定义其他一些功能；请看一下 [API 文档](https://developer.todoist.com/sync/v8/#add-two-new-tasks)以获得关于这些的更多信息。

最后，使用`api.commit()`提交您的更改，您的任务应该会每三十天成功添加一次。您也可以在提交更改之前添加尽可能多的新任务，因此对于您经常忘记的自动化任务没有限制。

# 4.每天运行 Python 脚本

所以你已经有了一个 Python 脚本来自动化待办事项列表项。现在你需要做的就是经常运行它(最好是每天)，让它不断地创建新任务。您可以每天手动执行此操作，也可以让脚本在启动时自动运行。

启动时运行脚本的过程将取决于您的操作系统，但是有大量的资源描述了这个过程。[这里有一个 Windows 的](https://www.tutorialspoint.com/autorun-a-python-script-on-windows-startup)。

一旦你每天运行这个脚本，就差不多了。如果你愿意，你可以用新任务更新它，但是除此之外，它会继续在后台为你安排任务。

# **让您的自动化更进一步**

我在本文中展示了一个非常简单的例子，但是您可以通过这个自动化工作流做更多的事情。考虑到除了 TodoistAPI 之外还可以使用其他 API 和 Python 特性，这种可能性是无穷无尽的。

例如，您可以使用[Python Open Weather Map](https://pythonhowtoprogram.com/how-to-use-weather-api-to-get-weather-data-in-python-3/)(py owm)添加基于天气的任务。步骤很简单。安装 pyowm，注册一个帐户，然后使用当前或预报天气数据为要添加到 Todoist 的任务创建条件。

这些自动化任务可以防止你在雨中不穿外套，防止你的植物在炎热的天气里干燥，或者确保你在霜冻的早晨给你的汽车除冰。

即使你永远不会忘记做这些事情(如果是的话，你就是机器)，它至少可以为你节省一些时间和精力去思考它们。

我喜欢这个项目，因为它简单、有用，而且还有扩展的空间。如果您对 Python 很熟悉，或者心中有特定的用例，可以尝试扩展我在这里介绍的内容。

至少，你干渴的植物可能会感谢你。

想阅读我所有关于编程、数据科学等方面的文章吗？在[这个链接](https://medium.com/@roryspanton/membership)注册一个媒体会员，就可以完全访问我所有的作品和媒体上的所有其他故事。这也直接帮助了我，因为我从你的会员费中得到一小部分，而不需要你额外付费。

只要我在这里订阅[，你就可以将我所有的新文章直接发送到你的收件箱。感谢阅读！](https://roryspanton.medium.com/subscribe)