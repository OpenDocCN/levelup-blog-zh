# 使用 Catch2 在 C++中进行测试驱动开发

> 原文：<https://levelup.gitconnected.com/test-driven-development-in-c-with-catch2-96a0bc592c9d>

![](img/f196bb9ed9a648bd0ea8fb1af0b58cfd.png)

塔尔哈·哈桑在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

测试驱动开发(TDD)是一个软件开发过程，其中软件需求首先被分解成一系列单元测试，然后编写软件来满足这些单元测试。例如，如果您正在创建一个 API 或一个返回股票市场数据(基本面和技术面数据)的 API 包装器，那么可以为每个测试预期 API 输出的端点编写测试。在编写完测试之后，API 端点将被实现，直到所有的测试用例都通过。

> 通过[成为推荐媒介会员](https://anthony-a-morast.medium.com/membership)或通过了解更多关于[测试驱动开发](https://amzn.to/3nBYkhC)或[有效 C++编程](https://amzn.to/3umjaoZ)(亚马逊会员链接)来支持我的内容。

像任何软件开发的方法一样，TDD 有它的优点和缺点。TDD 的一些好处是

*   **软件更容易维护**

通过编写单元测试来验证程序的正确性，可以更容易地引入对软件的修改。

*   **同样，代码重构会更加顺利**

同样，通过测试来验证正确性使得大规模生产变得不那么令人头疼了

*   **调试少**

当单元测试或多或少地告诉你去哪里找时，错误更容易被发现。事实上，最近在工作中，我的任务是围绕一些功能编写单元测试，只是为了清除潜在的 bug。

*   **单元测试通过描述代码应该如何工作来提供一些自我文档**

像所有的事情一样，好事也会带来坏事。下面列出了 TDD 的一些缺点。

*   **TDD 慢**

在能够输出任何代码之前，必须创建单元测试，这大大减慢了开发过程。如果你需要一个产品快，只需写代码。

*   **类似地，TDD 的维护也很昂贵且充满挑战**

敏捷项目管理方法告诉我们对产品所有者和客户反馈提供的产品进行迭代变更。使用 TDD，这变得具有挑战性并且昂贵，因为新的单元测试可能需要随着每次迭代占用开发时间而编写。

*   **很难引入 TDD**

如果您有一个由一组工程师维护和开发的现有产品，那么需要一点培训和一种新的思维方式才能转移到 TDD 方法。这可能值得，也可能不值得。

有时，TDD 的好处超过了坏处。当开发新软件或者在现有软件中创建可以作为一个模块进行测试的新特性时，尤其如此。这再加上 C++在 2022 年越来越受欢迎，是这篇文章的灵感来源。在这篇文章中，我们将使用 TDD 在 C++中构建一个简单的应用程序。单元测试框架 Catch2 用于测试财务 API 包装中的一些基本端点，并编写 C++代码来满足这些测试。

# 单元测试

Catch2 是最流行的 C++单元测试框架之一。在 v2.x 中，Catch2 是一个仅包含头文件的库，使得它很容易集成到任何 C++应用程序中并在其中使用。在最近的版本中，CMake 工具集是获取和使用 Catch2 的首选方式。为了简单起见，我将对版本 2.x 使用仅包含[头的实现。另一个项目依赖是](https://github.com/catchorg/Catch2/tree/v2.x) [RapiJSON 库](https://rapidjson.org/)，它允许我们在 C++中使用 JSON 对象。你可以在我之前的文章中读到更多关于[然后，创建三个测试用例来测试获取股票代码的报价、一组代码、所有可用外汇汇率的报价以及所有可用加密货币的报价。类似地，还测试了确定哪些加密货币和外汇汇率可用的方法。](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

[现在运行这些测试显示它们都失败了:](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

[![](img/e19f77fc20ba58d7b09448543c5d5780.png)](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

[然后，下一步是开发 C++类，直到这些测试用例通过。](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

# [C++类](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

[下面的代码片段显示了类声明](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

[这个类的细节对于这篇文章来说并不重要。重要的是我们获取可用的公共函数，实现它们，并确保它们满足上面的单元测试。为此，我们需要实现*_ to command delimited*、 *_downloadAndGetJsonFile* 、 *_generateFilename* 、 *_return* 和 *get** 方法。在实现这些方法之后，可以表明测试用例是令人满意的。](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

# [助手功能](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

[前面提到的前缀为' _ '的函数是帮助函数，负责所有 API 端点使用的公共操作。为了简洁起见，因为类的实现不是本文的重点，所以我不会实现这些函数，但会提供简短的描述(实现将在以后的文章中介绍)。](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

*   [_*to commame delimited*:这个函数获取一个字符串向量，并返回一个包含所有向量元素的字符串，所有向量元素用逗号分隔。](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)
*   [*_ downloadanddgetjsonfile*:解析来自 FMP 云 API 的，通过 cURL 下载，保存为 JSON 文件，读入字符串，然后文件被删除，这个 helper 函数执行这些操作。](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)
*   [*_generateFilename* :生成带时间戳的文件名。](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)
*   [*_return* :返回一个 RapidJSON 文档对象，提供一个包含 JSON 数据的字符串。](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

[有了这些助手函数，API 包装器可以轻松实现。](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

# [满足单元测试](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

[给定单元测试套件、类声明和成员函数定义，下一步是通过单元测试套件验证 API 包装的功能。](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

[重新构建并重新运行单元测试表明所有条件都得到了满足。](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

[![](img/1b3f3dc24b4126d614761ffccd1ed04c.png)](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

# [向前移动](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

[从这里开始，遵循 TDD 实践，开发人员将确定应用程序中还需要什么功能(在这种情况下是 API 包装),并创建更多的 Catch2 测试用例。最初，这些测试会失败，直到将功能添加到应用程序实现中。单元测试将在实现就绪后运行，直到软件的正确性得到验证。](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

[](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)

[*原载于 2022 年 7 月 3 日 https://www.anthonymorast.com**T21*](/parsing-json-in-c-with-rapidjson-6d6d3e505e12#define CATCH_CONFIG_MAIN //这告诉 CATCH 提供一个 main() —只在一个 cpp 文件中这样做</em></p></blockquote><p id=)[。](https://www.anthonymorast.com/blog/2022/07/03/test-driven-development-in-c-with-catch2/)