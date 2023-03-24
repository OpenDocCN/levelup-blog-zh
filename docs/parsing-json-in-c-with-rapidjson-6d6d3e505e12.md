# 用 RapidJSON 解析 C++中的 JSON

> 原文：<https://levelup.gitconnected.com/parsing-json-in-c-with-rapidjson-6d6d3e505e12>

![](img/0cc619bc7b6c134bc25583bab18bf7b5.png)

费伦茨·阿尔马西在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

今年早些时候，在做一些咨询工作时，我被要求用 C++解析从一个 URL 下载的 JSON 文件。在大多数语言中，这是一个容易完成的任务，许多语言都有内置的库来处理 JSON 文件的读写(例如 Python 中的 *json* 包)。然而，在 C++中，就像 C++中的大多数东西一样，没有内置的功能来处理 JSON 文件或 JSON 格式的字符串。有一个流行的库，很多人都在使用它，但是我发现与另一个库 [*RapidJSON*](https://rapidjson.org/index.html) *，*相比，它实际上非常慢，当处理大型 JSON 文件时(例如，在 JsonCpp 中读取大约 9 年的 OHLC 股票数据需要大约 20 分钟，而使用 RapidJSON 只需要几秒钟)。

尽管 RapidJSON 的[文档非常好，但我认为看一个用 C++处理 JSON 文件的编程示例会有所帮助。在这篇文章中，我将介绍一种将 JSON 文件(或字符串)读入 RapidJSON 文档对象的一般方法，并从我目前从事的项目中提供一个这样做的例子。](https://rapidjson.org/md_doc_tutorial.html)

# 拉皮德森

RapidJSON 是一个专注于在 C++中提供快速解析和生成 JSON 文件的库。该库可以通过两种方式包含在 C++项目中:只包含头文件的实现可以复制到项目的源目录中，或者可以使用 CMAKE 来安装项目，然后可以通过 *-lrapidjson 与 g++链接。*两种方法的结果都一样，只是项目的构建和库的包含会发生变化。更多信息可以在[项目的 GitHub](https://github.com/Tencent/rapidjson/) 上找到。

RapidJSON 中的 Document 类以适当的结构保存 JSON 文件内容，这允许解析 JSON 数据。该类包括如下内容

从这里开始，读取 JSON 文件的内容非常简单。下面的 *JsonParser* 类可用于通过 *getJsonDocument(…)* 函数解析 JSON 文件，该函数返回一个 rapidjson::Document 对象。

我将使用我目前正在从事的一个项目中的一个 JSON 文件作为例子。这是一个非常简单的 JSON 文件，没有数组，但是提供了一个基本的用法示例。

上述 JSON 是用于流体动力学的格子 Boltzmann 解算器的一些参数。这可以通过扩展上面的 JsonParser 类来解析。

# 数组

因为大多数 JSON 文件都有嵌套的数组结构，所以对 JSON 文件的另一个重要操作是迭代 JSON 数组。虽然我目前没有在我上面提到的项目中这样做(我很快会这样做)，但我已经在帖子前面提到的咨询项目中这样做了。下面是代码库中的一个片段，演示了 RapidJSON 中 JSON 数组的迭代。

如上面的代码片段所示，RapidJSON 文档对象有一个 *GetArray()* 方法，该方法将返回一个保存 JSON 数据的 RapidJSON 值对象。为了清楚起见，RapidJSON 中所有解析的 JSON 都存储在一个值对象中。Document 对象是一个表示 DOM 并保存 DOM 树的根值的对象。

# 结论

在这篇文章中，介绍了 RapidJSON JSON 解析 API。展示并解释了一些示例用法，作为我参与的两个不同项目的代码片段。对于那些需要更深入了解 RapidJSON 的人来说，这个项目有一个非常有用的 [GitHub 库](https://github.com/Tencent/rapidjson/)和更多[有用的文档](https://rapidjson.org/index.html)。这篇文章的目的是向那些不知道这个项目存在的人(我想大多数人都坚持使用 JsonCpp)介绍这个项目，并提供一些非常原始的用法，以便您可以开始使用这个项目。