# 使用 Python 连接到 Elasticsearch 快速指南

> 原文：<https://levelup.gitconnected.com/connect-to-elasticsearch-using-python-a-quick-ish-guide-c632c09b382a>

![](img/5f41cb4affda1129c7983365447be48c.png)

想使用 Python 进行 Elasticsearch 吗？不要再看了！

如果你在这里，你可能听说过 Elasticsearch，也许你想知道为什么你更喜欢 Elasticsearch 来检索文档，而不是普通的 SQL 数据库。

Elasticsearch(或简称 ES)很容易扩展以处理大量文档，并提供了广泛的功能，不仅与 SQL 数据库功能相匹配，而且在某些情况下还超过了 SQL 数据库。例如，ES 在基于文本的搜索方面做得非常好，最初是作为搜索引擎开发的。

它也是开源的，所以如果你只是想尝试一下，你就不必担心许可证什么的。如果你想认真对待它，并需要更多的果汁，[elastic.co](https://www.elastic.co/)已经为你提供了他们的麋鹿栈，它还带有 Kibana analytics 等等。

至此，让我们来看一个如何使用 Python 连接到 Elasticsearch 的简单示例！

# 在我们开始之前做一点准备…

因为我的主要工作是 Django，所以我将使用 Django Rest 框架向您展示如何使用 Django 服务器向 ES 索引添加文档，以及运行真正基本的搜索。这对于在典型的 HTTP 服务器中利用 ES 的能力非常方便。

幸运的是，您不必使用 Django 或它的 Rest 框架库！您只需替换 Rest 框架组件来提供一个字典，该字典包含您想要上传到 ES 索引的源数据，但是我们稍后会谈到这一点。

# 而现在…

![](img/304005d0e787d9ee5338cdd554abb289.png)

该编码了。

让我们来看看一个非常简单的实现。注意，您必须将`elasticsearch`包安装到您的虚拟环境中(如果您使用 Docker，则安装到容器中)。

为此，我们将假设接收 Elasticsearch 服务器已经设置好并准备好接收命令。

# 哇，那它是怎么工作的？

我们的 ES helper 类使得索引任何 Django 模型对象变得非常方便，比如:

```
def index(obj):
    es_handler = Es() 
      # make sure your DRF serializer handles the object type.
    es_handler.index_obj(obj)
```

如果您没有使用 Django 或 Rest 框架，您可以这样索引文档:

```
def index(dict_like):
    es_handler = Es() 
    # accepts a dictionary with at least one item 'id'.
    es_handler.index_dict(dict_like)
```

# 那么助手里到底是怎么回事呢？

正如您所料，函数的真正内容在`self.es.index()`中，它需要几个参数:

1.  `index`，索引的名称/别名
2.  `id`，您将要在 Elasticsearch 上存储的文档的主键
3.  `body`，一个包含文档数据的字典(这个东西在 Elasticsearch 上运行，并在搜索结果中返回)。

如果您对 ES 不熟悉，可以把索引想象成 Windows 中的一个文件夹，其中包含一大堆文档。文档包含您通过`body`输入的数据，并根据您给出的`id`命名。再次使用相同的`id`进行索引将会覆盖第一个文档。

# 感谢您的阅读！

在本文中，我们介绍了如何使用 Python/Django 连接到 Elasticsearch，以及如何索引文档。然而，我没有涉及搜索，因为这需要更多的解释。如果有足够的需求，我可能会讨论这个话题，但是现在，谢谢你再次通读这篇文章！