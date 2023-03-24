# 您将在 TypeScript 中错过的 Python 特性

> 原文：<https://levelup.gitconnected.com/python-features-that-you-will-miss-in-typescript-78ecc440b8bc>

## 有时候，当你失去一样东西时，你才会真正珍惜它

![](img/bf1dc078bac556935b07ce0299540b6b.png)

亚历克斯·丘马克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

最近，我从 python 和 django 作为主要的后端语言切换到带有 TypeScript 的 node.js。经过一段时间的学习，我可以说我喜欢这种转变。看到不同的语言和框架如何解决相似的问题总是很有趣。当您理解他们在方法上的一致或不同之处时，您就对编程本身有了更好的理解。

在许多方面，TypeScript/JavaScript 和 python 是相似的。他们都是

*   …拥有现代语法
*   …最好保留为单线程应用程序
*   …最初是动态语言，后来添加了静态类型检查
*   …拥有一个包含许多模块/包的大型生态系统
*   …支持带有承诺和异步/等待的异步编程
*   …相当频繁地发布新版本(对于一种语言)

但是语言之间当然存在一些差异，有时需要一点距离才能完全理解 python 及其生态系统提供的特性。虽然我总体上喜欢用 TypeScript 编码，但有时我会遇到一些问题，我知道这些问题可以用 python 很好地解决。大多数情况下，TypeScript/JavaScript 提供了一个类似的优雅解决方案，但有时我发现自己运气不好，最终我得到了一些我知道用 python 可以容易得多的东西。下面是我列出的一些特性，当你使用 python 时，这些特性很容易被认为是理所当然的，但是当你不得不在没有它们的情况下编码时，这些特性就会被忽略:上下文管理器、对类型的一级支持、数据库框架、pytest fixtures 和字典理解。

# 上下文管理器

python 中的上下文管理器有很多用例，比如打开和关闭文件，用锁保护代码，或者用设置和拆除来定制资源管理。当我在我们的后端工作时，我真的开始想念他们，我们需要数据库事务。在 python 中，这个问题得到了很好的解决。事实上，很多库，比如 [django](https://docs.djangoproject.com/en/3.2/topics/db/transactions/) ，已经为此提供了上下文管理器！

python 中的典型 DB 事务(上下文管理器可能由库提供)

对于 TypeScript，我们使用了 [node-postgres](https://node-postgres.com/features/transactions) ，python 中最接近上下文管理器的解决方案涉及回调。

带有 TypeScript 的典型数据库事务

虽然这不是有史以来最差的实现，但不如 pythons 干净。如果你在 async/await 之前去过 JavaScript 的[回调地狱](https://www.geeksforgeeks.org/what-is-callback-hell-in-node-js/)，可能会触发 PTSD。库不提供这个接口，所以你必须自己实现它，否则你最终会有很多 try/catch 博客来处理你的事务。我至少有一次犯了这样的错误，这导致了一个 bug，幸运的是我在它投入生产之前就发现了它。

# 对类型的一阶支持

在 python 中，我通常使用一个`dataclass`来定义消息的模式，用 TypeScript 声明一个`type`。在运行时，python 仍然允许我检查`dataclass`类及其字段的类型。使用 TypeScript，没有办法做到这一点，因为当它被编译成 JavaScript 时，所有的类型信息都丢失了。

现在，这实际上在哪里有用呢？我非常喜欢基于属性的测试和生成测试用例以获得更好的覆盖率。Python 有一个很好的测试框架，叫做[假设](https://hypothesis.works/)。它允许您从带有类型注释的`dataclass`中创建测试策略，这些策略将生成测试用例。

假设推断字段和类型，并自动生成测试用例

Typescript/Javascript 有自己的框架，[快速检查](https://github.com/dubzzz/fast-check)，对于大部分部分来说，和假设非常相似。它不能做的一件事是生成测试策略。您必须在类型和测试策略中重复您的对象的模式。在测试策略中，必须重复对类型的每一次更改。这不是世界末日，因为如果你忘记更新一个，编译器会提醒你。它还是很烦人，而且不是很干。

快速检查无法推断字段及其类型

# 数据库框架

我不得不多说一点关于数据库包装器的内容。我主要关注 SQL 数据库的库，尤其是 PostgreSQL。大多数情况下，一个简单的 SQL 数据库对于一个项目来说就足够了。Python 有一些非常成熟的解决方案，比如 Django 自带的 SQLAlchemy 或 ORM。TypeScript/JavaScript 紧随其后，TypeORM 是最受欢迎的。这些库允许你随着时间的推移进化你的模式，这种实践被称为[进化数据库设计](https://martinfowler.com/articles/evodb.html)。(SQLAlchemy 的一个更实际的例子是这里的，这里的是一个带有 TypeORM 的例子)。虽然两种语言都有成熟的解决方案，但我还是遇到了一个例子，python 解决方案(在我的例子中是 Django)比我见过的任何 JavaScript 解决方案都功能丰富。

经过一两年的积极开发，您将已经创建了大量的迁移。我曾经参与过一个 Django 项目，在这个项目中，我确信在迁移过程中添加的大多数字段和表在以后的迁移中都被修改或删除了。Django 实际上为压缩迁移提供了一个解决方案，以保持较低的迁移总数。您只能压缩已经应用于所有生产环境的迁移，因此压缩更像是一种清理，以保持您的代码库整洁，并使从头建立开发或测试数据库变得更加容易。我还没有见过任何试图压制迁移的 TypeScript/JavaScript 包，如果没有一些防护，我不会尝试这样做。

# Pytest 夹具

当我编写测试时，我会尽量减少每个测试所需的设置和拆卸量，但有时这是不可避免的，有时您希望在测试之间共享设置。y 测试夹具是我的首选工具。它允许您设置和拆卸对象，无论是针对每个测试，还是跨模块共享，甚至是所有的测试。

pytest 夹具示例

当然，JavaScript 有办法运行测试的设置和拆卸代码。开玩笑的说，这是所有之前的[/](https://jestjs.io/docs/api#beforeallfn-timeout)[之前的](https://jestjs.io/docs/api#beforeeachfn-timeout)做的几乎一样，除了一个例外:你不能把物体从夹具传入测试！常见的解决方法是使用一个在测试和安装/拆卸功能之间共享的变量。

带有安装和拆卸方法的 jest 示例

这是常见的做法，但也令人讨厌。一旦你有几个测试共享相同的设置/拆卸代码，它们都将访问相同的全局可变变量。这是我的警钟开始响起的地方。测试应该是独立的，它们之间没有任何共享状态。因为设置是在每个测试之前运行的，所以它们实际上并不共享同一个对象。只是遗憾的是，这不能用代码恰当地表达出来。

还有其他流行的 TypeScript/JavaScript 测试框架，比如 [Mocha](https://mochajs.org/) ，但是据我所知，它们倾向于提供相同的[before all/before each hooks](https://mochajs.org/#root-hook-plugins)。

# 词典释义

我经常发现自己在代码中有如下操作:我有一个对象集合，我想根据某个对象字段，通常是它们的 ID 来查找它们。Python 有一个非常实用和优雅的语法:理解。

在 JavaScript 中，由于有了 [lodash](https://lodash.com/docs/#keyBy) 模块，您也可以用很少的代码实现这一点。

就其本身而言，这两种解决方案都是干净、实用且快速的。但是与 python 生成器相比，lodash 感觉有点笨拙。它没有给你一个清晰的视觉映射`key: value`。当查找更复杂时，函数会修改键和值，可读性会降低很多。

# 这些差异重要吗？

我已经咆哮过 python 比 TypeScript 更优雅的边缘情况。但是着眼于大局，我仍然喜欢用 TypeScript 编码。TypeScript 在一些特性上优于 python。例如，我发现用 TypeScript 编写更多更精确的类型比用 python 和 MyPy 更容易。

![](img/335d958f017b5bb5e5ec82280f132baf.png)

照片由 [Piret Ilver](https://unsplash.com/@saltsup?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这些差异重要吗？他们显然对我很重要，否则，我不会写这篇文章。写代码意味着简洁明了地表达你希望计算机做什么。它应该尽可能地关注“什么”,而不是在特定技术中“如何”完成。它使代码更容易阅读、编写和维护，最终导致更少的错误。在我给出的例子中，python 比 TypeScript 做得稍好。

# 最后的想法

这是否意味着如果我必须从头开始，我会选择 python 而不是 TypeScript 做后端？肯定不是。最终，两种语言的相似之处多于不同之处。其他因素更重要，比如你团队的专业知识。在我们的例子中，我的团队将同时开发前端和后端，这一事实使得 TypeScript 成为我们的最爱。

如果您正在寻找两种语言之间的更一般的比较，那么感谢您一直阅读本文。Hackernoon 有几篇好文章[在这里](https://medium.com/hackernoon/could-pythons-popularity-outperform-javascript-in-the-next-five-years-abed4e307224)和[在这里](https://medium.com/hackernoon/javascript-vs-python-in-2017-d31efbb641b4)。

## 资源

*   [基于属性测试的 pythons 假设](https://hypothesis.works/)
*   [基于属性测试的 JavaScripts 快速检查](https://github.com/dubzzz/fast-check)
*   [用于高效列表和对象操作的 JavaScripts lodash 模块](https://lodash.com/)
*   [pytest 夹具](https://docs.pytest.org/en/6.2.x/fixture.html)
*   [jest JavaScript 测试框架](https://jestjs.io/)
*   [进化数据库设计](https://martinfowler.com/articles/evodb.html)
*   [使用 SQAlchemy 进行进化数据库设计的工作示例](https://benchling.engineering/move-fast-and-migrate-things-how-we-automated-migrations-in-postgres-d60aba0fc3d4)
*   [使用类型表进行进化数据库设计的工作示例](https://betterprogramming.pub/typeorm-migrations-explained-fdb4f27cb1b3)
*   与姜戈一起镇压移民
*   [python vs javascript 文章#1](https://medium.com/hackernoon/could-pythons-popularity-outperform-javascript-in-the-next-five-years-abed4e307224)
*   [python vs JavaScript 文章#2](https://medium.com/hackernoon/javascript-vs-python-in-2017-d31efbb641b4)