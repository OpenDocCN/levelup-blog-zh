# 如何在 Python 程序中运行 Scrapy spiders

> 原文：<https://levelup.gitconnected.com/how-to-run-scrapy-spiders-in-your-program-7db56792c1f7>

## 学习以编程方式运行 Scrapy 蜘蛛

我们[以前](https://medium.com/codex/how-to-build-a-scraping-project-with-scrapy-and-mongodb-46e78b6549e3)介绍过[如何用 Scrapy 和 MongoDB](https://medium.com/codex/how-to-build-a-scraping-project-with-scrapy-and-mongodb-46e78b6549e3) 构建一个抓取项目。通常你会用`scrapy crawl`命令运行蜘蛛。您不会只运行它们一次，而是会安排它们重复运行，以便不断获取新数据。刮擦作业可以简单地通过 [CRON 作业](https://en.wikipedia.org/wiki/Cron)或更复杂的程序如 [Airflow](https://airflow.apache.org/) 来安排。

但是，有时您可能不希望安排被动运行擦除作业。相反，您可能希望基于某些条件以编程方式触发抓取作业。例如，您可能希望根据用户输入触发刮擦作业。

在本文中，我们将介绍在您的程序中运行 Scrapy spiders 的不同方法，然后您将会很好地理解在您自己的案例中使用哪种方法。

![](img/fddd93d5cf4d7986bb5bd224b5f99df9.png)

图片来自 [Pixabay](https://pixabay.com/illustrations/developer-programmer-technology-3461405/) 。

在本帖的演示中，我们将使用[上一篇文章](https://medium.com/codex/how-to-build-a-scraping-project-with-scrapy-and-mongodb-46e78b6549e3)中介绍的蜘蛛。蜘蛛的代码可以从[克隆到这里](https://github.com/lynnkwong/run-scrapy-spiders-in-program)。或者，你可以按照[上一篇文章](https://medium.com/codex/how-to-build-a-scraping-project-with-scrapy-and-mongodb-46e78b6549e3)中介绍的步骤，自己编写代码，以便更熟悉 Scrapy 框架。

特别是这篇文章中的演示，创建了一个新的蜘蛛`AuthorSpider`，它将用于演示如何使用 Scrapy API 同时运行多个蜘蛛。此外，为了简单起见，抓取的数据没有保存到 MongoDB。如果您愿意，可以为 MongoDB 添加[管道。](https://github.com/lynnkwong/scrapy-quotes-demo/blob/main/scraping_proj/pipelines.py)

使用*子进程*运行蜘蛛程序。

正如本文中[所介绍的，我们可以将`scrapy crawl`命令作为 shell 命令运行。由于*子进程*模块的安全性和其他便利特性，建议](https://medium.com/codex/how-to-execute-shell-commands-properly-in-python-5b90c1a9213f)[使用*子进程*模块而不是`os.system()`函数来运行 shell 命令。](https://medium.com/codex/how-to-execute-shell-commands-properly-in-python-5b90c1a9213f)

如果你想同步运行蜘蛛并等待结果，你可以使用`subprocess.run()`:

因为我们已经将抓取日志重定向到了`/tmp/scrapy.log`，所以作业应该会安静地完成。如果想查看脚本中的日志，可以在`[settings.py](https://github.com/lynnkwong/run-scrapy-spiders-in-program/blob/main/scraping_proj/settings.py)`中注释掉下面一行:

```
LOG_FILE = "/tmp/scrapy.log"
```

因为 Scrapy 将所有日志重定向到标准错误(STDERR ),所以我们只需要管道 STDERR:

如果想异步运行蜘蛛，可以使用`subprocess.Popen()`:

`proc.poll()`如果作业仍在运行，则返回`None`，如果作业已完成，则返回退出代码。关于`subprocess.run()`和`subprocess.Popen()`的更多细节，请参考[这篇文章](https://medium.com/codex/how-to-execute-shell-commands-properly-in-python-5b90c1a9213f)。

**使用** `**CrawlerProcess**` **在同一进程中运行多个蜘蛛**。

上面我们已经介绍了如何使用*子进程*模块在你的程序中运行 Scrapy spiders。使用*子进程*是在你的程序中运行蜘蛛的一种幼稚的方式。当您只想在每个进程中运行一个 spider 时，这种方法很有效。如果你想在每个进程中运行多个蜘蛛，或者想在你的程序中直接获取和使用抓取的项目，你需要使用 Scrapy 的内部 API。

让我们首先展示使用 Scrapy 的内部 API 运行蜘蛛的代码，然后解释有趣的部分:

如果你是 Scrapy 的新手，上面的代码可能很难理解。不要担心，我们现在将解释所有技术细节，这将使您更容易理解:

*   我们使用`get_project_settings()`函数根据`[settings.py](https://github.com/lynnkwong/run-scrapy-spiders-in-program/blob/main/scraping_proj/settings.py)`模块中的设置来获取项目的设置。返回的`settings`是类`scrapy.settings.Settings`的一个实例，其行为非常类似于字典。
*   我们使用`[CrawlerProcess](https://docs.scrapy.org/en/latest/topics/api.html#scrapy.crawler.CrawlerProcess)`类在一个进程中同时运行多个 Scrapy 蜘蛛。我们需要用项目设置创建一个`CrawlerProcess`的实例。
*   如果我们想对蜘蛛进行自定义设置，我们需要为蜘蛛创建一个[爬虫](https://docs.scrapy.org/en/latest/topics/api.html#scrapy.crawler.CrawlerProcess)的实例。爬虫在 Scrapy 中是一个很抽象也很重要的概念。当你想拥有 Scrapy 的高级配置时，你会遇到它。尤其是当你使用`from_crawler()`类方法的时候。Crawler 对象提供了对所有 Scrapy 核心组件的访问，尤其是设置，这是扩展访问它们并将它们的功能与 Scrapy 挂钩的唯一方式。请注意，您需要继承项目的设置，否则项目设置将无效。在本例中，如果不使用`**settings`语法，日志将处于调试模式，并将打印在屏幕上，而不是发送到日志文件`/tmp/scrapy.log`。你可以自己尝试一下。
*   我们可以用`CrawlerProcess`实例的`crawl()`方法运行/抓取蜘蛛的爬虫。特别是，我们可以将键/值对传递给这个方法，它们将表现为自定义属性，就像之前的文章中介绍的那样。对于`quotes`蜘蛛，我们只想刮去带有*爱*标签的引号。
*   我们可以在同一个进程中爬行多个蜘蛛/爬虫，它们直到调用`start()`方法才会开始运行。

使用`CrawlerProcess`在同一个进程中运行多个蜘蛛的代码可以在[这个脚本](https://github.com/lynnkwong/run-scrapy-spiders-in-program/blob/main/scraping_script_with_api.py)中找到。如果您在克隆的项目文件夹中运行这个脚本，您将会看到引文和作者。请注意，您需要等待几秒钟才能完成抓取作业。

**使用信号直接获取**程序中 **的项目。**

通常，我们会将抓取的项目保存在一个数据库中，要么是 MySQL 之类的关系数据库，要么是 MongoDB 之类的 NoSQL 数据库。然而，有时我们会希望直接在程序中访问抓取的项目，并以某种方式处理它们。在这种情况下，我们可以使用爬行实例的[信号](https://docs.scrapy.org/en/latest/topics/signals.html#topics-signals),用于在某些事件发生时发出通知:

注意，信号是通过`Crawler.signals.connect()`方法相加的。在上面的代码中，我们将`quote_cralwer`连接到三个信号，即当蜘蛛启动时(`[signals.spider_opened](https://docs.scrapy.org/en/latest/topics/signals.html#scrapy.signals.spider_opened)`)、当一个项目被刮除时(`[signals.item_scraped](https://docs.scrapy.org/en/latest/topics/signals.html#scrapy.signals.item_scraped)`)和当蜘蛛关闭时(`[signals.spider_closed](https://docs.scrapy.org/en/latest/topics/signals.html#scrapy.signals.spider_closed)`)。每个信号都与一个函数相关联，当信号被触发时，这个函数将被调用。这个函数像 JavaScript 中的[回调函数](https://developer.mozilla.org/en-US/docs/Glossary/Callback_function)一样工作。

这些信号可以为连接的函数提供几个参数。你不需要全部都用，只能用需要的。例如，对于`signals.spider_opened`和`signals.spider_closed`的关联函数，我们只使用`spider`参数，这是蜘蛛发出的信号。您可以查看[信号文档](https://docs.scrapy.org/en/latest/topics/signals.html#spider-signals)以了解每个信号参数的更多详细信息。

在程序中直接抓取项目的代码可以在[这个脚本](https://github.com/lynnkwong/run-scrapy-spiders-in-program/blob/main/scraping_script_with_api_and_signals.py)中找到。如果您在克隆的项目文件夹中运行这个脚本，您将看到打开/关闭蜘蛛的信息，以及所有抓取的项目:

在这篇文章中，我们介绍了在你的程序中运行 Scrapy spiders 的两种方法，即使用*子进程*模块和 Scrapy 内部 API 的`CrawlerProcess`类。使用*子流程*模块运行`scrapy crawl`命令非常简单。但是，您不能在同一个进程中同时运行多个 spiders，并且您不能在您的程序中直接访问抓取的项目。这些高级特性可以通过内部 API 的`CrawlerProcess`类来实现。如果你是 Scrapy 的高级用户，并且想对蜘蛛如何在你的程序中运行有更多的控制，你可以直接使用`[CrawlerRunner](https://docs.scrapy.org/en/latest/topics/api.html#scrapy.crawler.CrawlerRunner)`类，它由幕后的`CrawlerProcess`类使用。

相关文章:

*   [如何用 Scrapy 和 MongoDB 构建一个抓取项目](https://medium.com/codex/how-to-build-a-scraping-project-with-scrapy-and-mongodb-46e78b6549e3?source=your_stories_page----------------------------------------)
*   [如何在 Python 中正确执行 shell 命令](https://medium.com/codex/how-to-execute-shell-commands-properly-in-python-5b90c1a9213f?source=your_stories_page----------------------------------------)