# 如何用 Scrapy 和 MongoDB 构建一个抓取项目

> 原文：<https://levelup.gitconnected.com/how-to-build-a-scraping-project-with-scrapy-and-mongodb-46e78b6549e3>

## 成为 Python 中真正的“蜘蛛侠”

Scrapy 是一个多功能的强大的网络抓取框架，可以用来抓取网站并从网页中提取结构化数据。适用于需要抓取很多网站或者很多页面的大型抓取项目。

![](img/1122db30a4b238bdc0d40fa9eb52a666.png)

图片来自 [Pixabay](https://pixabay.com/illustrations/spiderman-character-superhero-5561671/) 。

在本文中，我们将演示如何使用 Scrapy 来抓取整个[行情网站](http://quotes.toscrape.com/)并抓取所有作者的所有行情。提取的数据将存储在 MongoDB 中，因为提取的标签是可变长度的字符串列表，这更适合 MongoDB 这样的文档数据库。

在开始之前，我们需要安装本教程所需的软件包。建议[创建一个虚拟环境](https://medium.com/codex/how-to-create-virtual-environments-with-venv-and-conda-in-python-31814c0a8ec2)并在那里安装软件包，这样它们就不会搞乱系统库。为简单起见，我们将使用 [*conda*](https://medium.com/codex/how-to-create-virtual-environments-with-venv-and-conda-in-python-31814c0a8ec2) 来创建虚拟环境。我们需要安装用于网页抓取的 *Scrapy* 包，以及包含 MongoDB 使用工具的库 *pymongo* 。

```
(base) $ **conda create --name scrapy python=3.10**
(base) $ **conda activate scrapy**
(scrapy) $ **pip install -U Scrapy==2.5.1 pymongo==4.0.1**
```

因为我们将把抓取的数据存储在 MongoDB 中，所以我们需要一个可用的 MongoDB 服务器。实际上，你通常会有一个专用的 MongoDB 服务器，比如由 [MongoDB Atlas](https://medium.com/codex/how-to-use-mongodb-atlas-to-manage-your-server-and-data-d97a6e7663c5) 托管的服务器。在这项工作中，我们将在 Docker 容器中启动一个 [MongoDB 服务器:](https://hub.docker.com/_/mongo)

```
$ docker network create mongo-net$ docker run --detach --network mongo-net --name mongo-server \
    --env MONGO_INITDB_ROOT_USERNAME=admin \
    --env MONGO_INITDB_ROOT_PASSWORD=pass \
    --env MONGO_INITDB_ROOT_DATABASE=admin \
    --volume mongo-data:/data/db \
    --publish 27017:27017 \
    mongo:5.0.6
```

MongoDB 服务器已经启动，我们现在可以创建数据库并在其中插入文档。

在本帖中，我们将使用我们的老朋友[quotes.toscrape.com](http://quotes.toscrape.com/)进行演示。我们可以使用其他网站进行演示，但这不会更有趣，因为我们感兴趣的是抓取技术，而不是网站本身。quotes.toscrape.com 非常简单，在我们之前的文章中已经使用过。因此，我们应该更熟悉网站的结构，以及要使用的标签和 XPaths。

对于大的抓取项目，我们应该将蜘蛛和配置文件放在一个 ***项目*** 中，该项目将所有相关模块放在一起。我们可以使用`scrapy`命令行工具来创建一个抓取项目:

```
$ **scrapy startproject scraping_proj**
```

正如控制台中所提示的，我们现在可以进入项目文件夹，然后生成一个蜘蛛:

```
$ **cd scraping_proj**
$ **scrapy genspider quotes quotes.toscrape.com**
```

*   `quotes` —要创建的蜘蛛的名称。
*   `quotes.toscrape.com` —将被蜘蛛抓取的网站。

你可以给你的蜘蛛取任何名字。对于良好的做法，它应该是描述性的，并反映网站和项目，你是刮。

现在让我们用 Linux 中的`tree`命令检查项目文件夹的结构:

```
$ **tree . -I __pycache__**
```

`-I`选项忽略匹配指定模式的文件。这里我们想忽略包含编译后的 Python 字节码的`__pycache__`文件夹。这是项目文件夹的结构:

我们现在不需要接触大部分文件，但是对它们有一个大致的了解是有好处的:

*   `[scrapy.cfg](https://docs.scrapy.org/en/latest/topics/commands.html#configuration-settings)` —项目的配置文件。通常，它只包含设置文件的位置以及如何部署项目的配置。`scrapy.cfg`文件所在的目录被称为项目根目录。
*   `scraping_proj` —在项目根目录下，有一个项目文件夹(这里是`scraping_proj`)，所有与抓取相关的模块都在这个文件夹下。
*   `spiders` —重要的是，项目文件夹里面有一个`spiders`文件夹，所有的蜘蛛都在里面。
*   `items.py` —定义提取数据格式的模块。这不是必须的，但是为要提取的数据定义项目类型是一个很好的做法，这样数据就可以标准化并且不会包含随机字段。
*   `pipelines.py` —定义如何处理报废物品的模块。可以有多个管道以定义的顺序处理项目。
*   `[middlewares.py](https://docs.scrapy.org/en/latest/topics/spider-middleware.html)` —定义钩子框架的模块，可以为零碎的请求和响应添加自定义功能。
*   `settings.py` —项目设置文件。我们可以在这个文件中定义中间件和管道。此外，我们可以定义一些环境或系统变量，这些变量可以被所有的蜘蛛或管道使用。

稍后当我们将抓取的数据写入 MongoDB 时，我们将更新`items.py`、`pipelines.py`和`settings.py`。现在，让我们为`quotes.py`中的第一个蜘蛛添加代码:

quotes.py

如果你不熟悉 Python 中的 [walrus 操作符(:=)](https://medium.com/codex/how-to-use-the-assignment-expression-walrus-operator-in-python-13811859f80f) ，你可能会发现[这篇文章](https://medium.com/codex/how-to-use-the-assignment-expression-walrus-operator-in-python-13811859f80f)很有帮助。此外，如果你想知道更多关于[quotes.toscrape.com](http://quotes.toscrape.com)网站和使用的 HTML 元素和 XPaths 的信息，请查看[这篇文章](https://medium.com/codex/simple-web-scraping-using-requests-beautiful-soup-and-lxml-in-python-4f5903c67db2)。

现在我们可以用`scrapy crawl`爬这只蜘蛛了:

```
$ **scrapy crawl quotes**
```

干杯！数据被成功抓取并打印在控制台中。如果我们想将数据存储在 JSON 文件中，我们可以在命令行上添加`-o`选项:

```
$ **scrapy crawl quotes -o quotes_all.json**
```

抓取的数据现在将存储在`quotes_all.json`中。但是数据还是打印在控制台里，现在挺吵的。如果您仔细检查刮擦日志，您会注意到刮擦数据记录在[记录模块](https://lynn-kwong.medium.com/stop-using-print-in-your-python-code-for-logging-use-the-logging-module-like-a-pro-66fb0427d636)的`DEBUG`模式中。我们可以使用`-L`选项来更改日志记录级别。让我们将日志记录级别更改为`INFO`:

```
$ **scrapy crawl quotes -o quotes_all.json -L INFO**
```

注意`-L`和`INFO`都应该是大写的。这一次，数据不在控制台中打印，但仍存储在`quotes_all.json`中。如果不相信，可以删除这个文件，重新运行上面的命令。

正如你已经注意到的，在`start_requests()`方法中，我们检查蜘蛛是否有标签属性，如果有，我们将只抓取带有特定标签的引号。在命令行上，可以使用`-a`选项将属性传递给蜘蛛。您可以将任何属性传递给蜘蛛。但是，如何使用这些自定义属性取决于您。

让我们刮掉所有带有标签`love`的引文:

```
$ **scrapy crawl quotes -o quotes_love.json -L INFO -a tag=love**
```

自定义属性用`-a NAME=VALUE`格式指定。您可以多次重复`-a NAME=VALUE`来指定多个属性。上面的命令应该只抓取带有`love`标签的引号。

上面我们已经成功地抓取了报价并保存在一个 JSON 文件中。在这一节中，我们将学习如何在 MongoDB 中存储抓取的数据，这是一个非常流行的用于存储文档的 NoSQL 数据库，在 Python 中通常指 JSON 对象或字典。

由于搜集到的报价主要包含文本而非数字数据，因此将它们存储在 NoSQL 数据库中比存储在 MySQL 之类的关系数据库中更方便。此外，`tags`字段对于不同的引号有不同数量的字符串，这使得 MongoDB 成为一个更好的解决方案，因为 MongoDB 擅长存储[非结构化数据](https://www.mongodb.com/unstructured-data)。

让我们首先为报价创建一个`Item`类。如上所述，项目类别在`items.py`模块中定义。定义了一个`Item`类后，哪些字段将被删除就更清楚了:

items.py

然后我们需要更新蜘蛛以产生数据作为`QuoteItem`，而不是现在的字典:

quotes.py

`quotes.py`的整个模块可以在这里找到[。](https://gist.github.com/lynnkwong/564ef4b3123695b0e1a2bc5020aa9d53)

作为一个`Item`类，`QuoteItem`像一个普通的类一样工作，我们需要创建并产生一个它的实例，使用抓取的数据作为属性。

如果您再次运行蜘蛛，您可以看到它完全像以前一样工作。您可以尝试上面演示的所有不同的命令行选项，结果将是相同的。

如果我们想以不同的方式处理和保存抓取的数据，我们应该创建一个定制的项目管道。每一个被抓取的物品都将被发送到物品管道并在那里被处理。在本教程中，我们将只创建一个管道。在后面的教程中，我们将创建多个管道，并了解它们如何按照指定的顺序处理项目。

如上所述，项目管道在`pipelines.py`模块中定义。然而，当定义了多个管道时，我们会想要创建一个`pipelines`文件夹，并将管道定义模块存储在其中，以便更好地组织代码。本教程中的`pipelines.py`模块代码如下:

管道. py

关于 Scrapy 要求的管道的特殊方法的解释，请查看`pipelines.py`中的文档字符串。现在请不要运行蜘蛛，因为我们还没有完成。我们需要添加项目管道到`settings.py`。此外，正如您在上面的代码中看到的`from_crawler()` [类方法](https://medium.com/codex/how-to-use-the-magical-staticmethod-classmethod-and-property-decorators-in-python-e42dd74e51e7)一样，我们也需要在`settings.py`中为 MongoDB 定义一些设置:

settings.py

`settings.py`注意事项:

*   有些字段是 Scrapy 自动生成的，如`BOT_NAME`、`SPIDER_MODULES`等，不需要修改。
*   `settings.py`中有很多注释，为设置配置提供了有用的指导和参考。如果您喜欢更简洁的模块，可以安全地删除它们。
*   我们将创建的项目管道添加到`ITEM_PIPELINES`字典中。键是项目管道类`MongoDBPipeline`的相对路径，值是管道运行的顺序:项目从较低值到较高值的类。因为我们这里只有一个管道，所以你给它什么值并不重要。300 是注释中的默认值，因此保留为默认值。
*   我们还为日志定义了一些全局设置，包括默认的日志级别、日志格式和日志文件。
*   最后，在`settings.py`中还指定了 MongoDB 的一些配置，以便在需要时可以方便地导入。所有实现`from_crawler()` [类方法](https://medium.com/codex/how-to-use-the-magical-staticmethod-classmethod-and-property-decorators-in-python-e42dd74e51e7)的类都可以访问这些配置。

现在让我们再次运行蜘蛛:

```
$ **scrapy crawl quotes**
```

这一次蜘蛛是无声无息地爬行，控制台里没有任何日志。这是因为日志现在保存在一个日志文件中，这就是`/tmp/scrapy.log`。请检查此日志文件，确保其中没有错误。如果有，您可以根据错误相应地更新代码。您可能会发现 [PDB](https://medium.com/codex/how-to-debug-python-scripts-and-api-code-in-the-console-and-in-vs-code-a0b825ad7d41) 对调试蜘蛛很有帮助。

现在让我们检查数据是否保存在 MongoDB 中。在激活`scrapy`环境的情况下，在控制台中运行以下 Python 代码:

干杯！数据已成功保存在 MongoDB 中。

请注意，如果您多次运行蜘蛛。退回的文件数量也会成倍增加。这是因为在这个例子中，我们没有文档的唯一密钥。实际上，通常有一个唯一的源 id，可以用来唯一地标识正在被刮擦的物品。如果没有，您可以[对抓取的数据进行哈希处理](https://medium.com/codex/understand-the-encoding-decoding-of-python-strings-unicode-utf-8-f6f97a909ee0)并为文档生成一个唯一的密钥。然后，您可以使用带有`upsert`选项的`[update_one](https://docs.mongodb.com/manual/reference/method/db.collection.updateOne/)`方法来插入或更新文档。将会有一篇关于如何使用 MongoDB 的文章。

恭喜，你已经走到这一步了。您已经学习了如何使用 Scrapy 创建一个 scraping 项目，以及如何将数据保存到 MongoDB。你现在可以使用 Scrapy 来抓取你感兴趣的网站。请注意，您只能抓取带有普通 HTML 代码的网页。对于那些用 JavaScript 代码动态渲染的，需要使用一些特殊的工具，比如 [ProxyCrawl](https://medium.com/codex/how-to-scrape-javascript-webpages-using-proxycrawl-in-python-a4de7182d996) 或者 [Selenium](https://medium.com/codex/how-to-scrape-javascript-webpages-using-selenium-in-python-21d56731bb1f) 。请查阅相应的文章以供参考。在接下来的文章中，我们将讨论一些更高级的主题，比如使用代理抓取和缓存响应，这对您的应用程序很有帮助。

这篇文章中展示的代码的 GitHub 库可以从[这里](https://github.com/lynnkwong/scrapy-quotes-demo)克隆。

相关文章:

*   [使用 Python 中的请求、漂亮的汤和 lxml 进行简单的 Web 抓取](https://medium.com/codex/simple-web-scraping-using-requests-beautiful-soup-and-lxml-in-python-4f5903c67db2?source=your_stories_page----------------------------------------)