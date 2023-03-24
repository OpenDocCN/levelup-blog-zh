# 疯狂、强大的类型脚本元组类型

> 原文：<https://levelup.gitconnected.com/crazy-powerful-typescript-tuple-types-9b121e0a690c>

为了庆祝 TypeScript 4.2 最近的发布和该语言的持续发展，让我们来看看元组类型和我们可以用它们做的一些高级类型操作。

![](img/47d5b1811ddde86b9877f8b665dc1309.png)

# 基本原则

一个[元组](https://www.typescriptlang.org/docs/handbook/basic-types.html#tuple)(与‘夫妇’押韵，而不是‘学生’)是一个简单的数据容器。元组对象具有固定的大小，每个元素的类型是已知的，但是类型不需要相同。

一个基本元组定义是:

这个电影元组正好有 3 个元素，它们必须按顺序是`string`、`number`和`Date`。这些结构可以很容易地分解成它们的组成部分:

这里很棒的是`title`、`rating`、`releaseDate`的类型都是正确的(`string`、`number`、`Date`)。但是要注意如何定义它——我们不能使用类型推断，因为元组文字的语法和数组文字的语法是一样的。

上面，电影的类型是`(string | number | Date)[]`，所以每个析构变量的类型是联合`string | number | Date`。此外，要意识到在运行时，在类型脚本代码被转换成 JavaScript 之后，元组(和类型化数组)被实现为普通的 JavaScript 数组。像所有与 TypeScript 相关的事情一样，元组的安全性和约束只存在于编译时。

最后，我们还可以在元组中使用 [rest 元素](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-0.html#rest-elements-in-tuple-types)。这些允许我们创建可变元组。例如:

注意，rest 元素的类型本身必须是元组或数组类型——在本例中是`number[]`。

# 利益

我们当然可以将一部电影表现为一个类或接口，我们将永远拥有这个选项。但是，元组在以下情况下特别有用:

*   从函数中返回多个值
*   析构为具有任意名称的变量
*   表示一个函数的参数(稍后会详细介绍)

前两点的一个很好的例子就是 React 的`[useState](https://reactjs.org/docs/hooks-state.html)`函数。当您调用这个函数时，它返回一个状态的当前值和一个更新该状态的函数。

在一个元组中同时返回两者是有用的，因为您可以立即析构并使用任意名称。为了充分看到优势，想象一下如果在一个通用的`State`对象中返回相同的数据。您需要使用更详细的析构别名或使用对象引用。

# 元组形式的函数参数

把参数表示成元组怎么样？你可能已经见过 [rest parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters) ，将'`...`'应用到函数的最后一个参数来创建变量函数。

在调用点，我们可以使用可变数量的参数。这些被聚集到`sum`函数中的一个数组中。

在 TypeScript 中，我们可以将聚集的参数表示为数组或元组。这允许用几种方式定义函数签名。考虑一个带 3 个参数的正态函数:

我们可以使用类型化为元组的 rest 参数进行重写:

或者使用参数析构:

这很有趣，但是有用吗？它*是*有用的，因为元组是表示多个参数的单个实体。这在与泛型结合时特别有用，在泛型中，我们直到调用时才指定类型。让我们看一个实际的例子。

# 正在检查`Promise.all`

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)`类型包装了异步操作，并有一些有用的静态方法来组合多个承诺。

`[Promise.all](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)`方法接受一个承诺数组，并返回一个新的承诺，当所有输入承诺解析时，该承诺解析所有结果。我们可以随后使用`then`方法或`async await`来消费结果。例如:

或者通过解构:

这里有趣的是`name`、`age`和`startDate`都有预期的类型。输入参数类型`Promise<string>`、`Promise<number>`和`Promise<Date>`与结果的类型相关联，即一个类型为`[string, number, Date]`的元组。让我们看看这是如何实现的(我已经简化了定义):

如您所见，该定义使用了一系列重载，泛型参数的数量不断增加，最多可达 10 个。这些通用参数将输入**承诺元组**关联到元组的输出**承诺。**

我说代码简化为`Promise.all`实际上支持接受承诺对象和普通对象。此外，第一个重载实际上支持任意数量的相同类型的输入。所以实际的定义是:

这里的缺点是，它需要多个重载定义(冗余)，并且最多有 10 个参数。如果我添加第 11 个参数，我会得到一个编译器错误。看看能不能改进。

# 改进`Promise.all`

![](img/04839cdb23a78a5491bf40534b1f9875.png)

我们可以创建一个函数，将元组类型捕获为单个泛型参数。

我们稍后会看到`RemapPromises`,但是现在要意识到它会将一个**承诺元组**类型转换为元组类型的**承诺。**

在这个函数中，通用参数`T`被约束为任何元素的元组。类型是从用法中推断出来的，例如:

我们也可以使用一个 rest 参数来定义我们的函数，使其可变，并从调用位置删除“`[]`”。

最大的优势是我们有一个单一的定义，我们可以有超过 10 个论点。

# 重新映射类型

其中一个复杂的位是`RemapPromises`型。幸运的是，在上面，我们把它藏在一个别名中，这样我们直到现在才需要考虑它。这个问题的解决方案使用了我之前的一篇文章中讨论的一些概念，[疯狂、强大的 TypeScript 4.1 特性](https://medium.com/swlh/crazy-powerful-typescript-4-1-features-26036f4de6bc)。

首先，我们定义一个类型来获取一个`Promise<T> | T`并返回`T`。

这使用了一个[条件类型](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)和类型推断来计算输入承诺类型的内部类型。如果输入类型`T`不是承诺类型，则直接使用。

接下来，我们可以使用递归条件类型对 tuple 中的所有元素执行此操作，将`[Promise<T1>, Promise<T2>, ..., Promise<TN>]`转换为`[T1, T2, ..., TN]`。

这里我们再次使用条件类型和推理来提取输入元组的`Head`和`Tail`。然后，我们可以使用之前定义的`UnwrapPromise`来展开`Head`，并递归调用`UnwrapPromises`(注意“s”)来展开尾部。这些被组合成一个结果元组。

最后，我们可以使用这个“未包装”承诺的元组来创建我们的最终承诺元组。

# 为什么不这样实施？

标准的`Promise.all`定义可能最终会变成这样，但是可能会有一些我没有考虑到的问题。我只是把它作为一个实际的例子来演示，来说明你可以用元组类型、递归条件类型等做什么。您可以在自己的代码中考虑其他用例。

当对一门语言的标准库进行修改时，定义需要支持现存的所有类型脚本代码，你需要比我在这里做的更严格一些。对向后兼容性、极限情况、编译器性能等的考虑会影响任何最终的定义。我很乐意在评论中听到你对这个问题的想法。

# TypeScript 4.2 增加了什么？

TypeScript 4.2 为元组类型带来了一个特性，那就是在前导或中间元素上扩展的能力。例如，这里我们有一个元组，其中中间的元素是一系列可变长度的`number`:

当与 rest 参数结合使用来创建前导或中间参数的可变函数时，这可能会很有用。考虑一个断言函数来测试是否所有的值都相等，如果不相等就抛出一个错误和一条特定的消息。

这个函数必须用两个或更多的数字来调用，后面跟一个字符串，我们不能用普通的 rest 参数来调用。

我不确定你会经常创建这种形状的函数。没有办法轻松地析构元组，因为 rest 析构必须总是最后一个元素。所以这里我们使用切片和索引以及显式类型断言。我认为将多个函数、参数作为对象、重载或尾随 rest 参数通常是正确的答案。

这对于安全地键入使用这种形状的 JavaScript 函数可能是有用的。

# 结论

小的、基本的元组本身非常有用，但是当你深入研究并与泛型、递归条件类型和 rest 元素相结合时，你可以获得一些有趣的结果。

请务必查看我的 [TypeScript 4.1 帖子](https://medium.com/swlh/crazy-powerful-typescript-4-1-features-26036f4de6bc)，在那里我讨论了递归条件。另外，要查看与`useState`一起使用的元组的例子，看看我的[反应教程](https://medium.com/swlh/crazy-powerful-typescript-4-1-features-26036f4de6bc)。

我希望你觉得这很有趣。请务必查看我们的[打字稿](https://instil.co/courses/typescript-introduction/)课程。我们也很乐意使用 TypeScript 提供 [React 培训](https://instil.co/courses/react-with-typescript/)。我们几乎为世界各地的公司提供服务，并且很乐意根据您团队的水平和具体需求定制我们的课程。

原贴[此处](https://instil.co/blog/crazy-powerful-typescript-tuple-types/)。