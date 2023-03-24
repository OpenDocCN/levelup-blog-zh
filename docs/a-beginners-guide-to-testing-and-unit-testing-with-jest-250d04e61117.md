# Jest 测试和单元测试初学者指南

> 原文：<https://levelup.gitconnected.com/a-beginners-guide-to-testing-and-unit-testing-with-jest-250d04e61117>

## 对测试感兴趣却不知道从何下手？让我们从理解测试开始，然后用 Jest 仔细看看单元测试。

![](img/05a5277ccbde300679e98bee391de6a9.png)

[JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 目录

1.  [简介](#40ab)
2.  [快速回顾](#cc8d)
    2a。[奖金！什么是测试用例 vs 测试套件？](#33e7)
3.  [测试的利弊](#3e4c)
4.  [入门](#4590)4a。[奖金！调试 Jest 测试](#3119)
5.  [跟我练](#be3a)
6.  [结论](#f364)
7.  想要更多吗？查看其他资源！

# 简介 **🥼**

一旦你对编程语言和/或框架有了一些实践和基本的理解，你就会开始意识到测试和测试。自然，作为一个渴望了解更多的初学者，一些问题可能会浮现在脑海中。对我自己来说，我早期的一些问题是:

*   写测试是什么意思？
*   我们什么时候写测试？
*   我们为什么要测试？
*   我们如何测试？

在高层次上，测试是评估代码执行的过程。测试的作用是检查是否有任何错误，并确保我们的代码如预期或期望的那样执行(也就是“成功”)。本文的其余部分将回答并详细介绍我上面概述的其他问题，包括用 Jest 进行单元测试的演示。也就是说，你可以随意跳来跳去，我们在另一边见！

# **快速回顾⚡**

开始之前，请记住以下几点:

*   测试驱动开发(TDD) :一般来说，在开发周期中没有具体的时间来编写测试，但是有些人喜欢做“测试驱动开发”或 TDD。这意味着在您编写任何开发代码之前，您需要考虑一些情况。
*   *单元测试*:这些测试是低级的，有助于验证代码库的每一部分都如预期的那样执行，因此更容易将各部分相互隔离。就像它的名字一样，它们测试单个的单元，比如方法、函数或者对象。
*   *集成测试*:正如 [Atlassian](https://www.atlassian.com/continuous-delivery/software-testing/types-of-software-testing) 所描述的，这些测试“验证你的应用程序所使用的不同模块或服务能够很好地协同工作”。更具体地说，集成测试主要关注模块之间的接口和数据/信息流，而单元测试则关注特定的模块。如果正确的属性被传递和显示，或者当用户点击一个按钮时，如果正确的信息被显示，一些示例测试用例可能是从你的数据库获取信息，API 调用。
*   *端到端测试*:本质上，这些测试复制了用户对整个软件的行为，以验证软件本身是否正常运行。例如，对于 todo 应用程序，这可以包括当用户成功登录时，检查他们是否被引导到具有正确信息的正确页面，是否能够在他们的 todo 列表中添加和删除，以及是否注销。
*   *组件测试*:类似于单元测试，都是测试一个单独的单元/组件。也就是说，两者之间存在一些差异；主要的一点是，单元测试是由开发人员完成的，而组件测试是由测试人员完成的。
*   *快照测试*:摘自官方 [Jest 文档](https://jestjs.io/docs/snapshot-testing)，“只要你想确保你的 UI 不会发生意外变化，快照测试就是一个非常有用的工具”。更具体地说，这些测试检查 UI 上的某些内容是否发生了变化，如果发生了变化，就突出显示变化，而不是检查组件的行为是否正确。

## **奖金！什么是测试用例 vs 测试套件？🤔**

IBM 的一个测试用例回答了这个问题:我要测试什么？您开发测试用例来定义您必须验证的东西，以确保系统正确运行并以高质量水平构建。测试套件是为测试执行目的而分组的测试用例的集合。换句话说，你可以认为这是一个一对多的关系，其中一个测试套件有许多测试用例。因此，您的测试用例将是您需要考虑的场景的不同部分，而测试套件是将它们分组的实际场景。

# **测试⚖️的利弊**

有很多关于测试和 TDD 的观点，主要是关于为什么我们应该关心测试。虽然有很多技术原因，虽然我是测试的初学者，但我想与您分享我能想到的主要优点和缺点，以鼓励您开始测试。

首先，虽然数量不多，但让我们看看测试的缺点:

*   测试维护:当您继续扩展代码库时，有时您也需要更新测试，尤其是当环境发生变化时。
*   调试测试:有时不是代码有问题，而是实际的测试脚本不允许测试通过。当这种情况发生时，通常很难理解为什么/在测试中哪里出现了问题。
*   花费的时间:以上两个因素都与此有关，那就是需要花费大量的时间、精力，当然还有更多的代码来整合测试。测试还需要一定的质量技能和理解来创建有意义的测试，因此可能需要更多的时间来添加测试。

尽管有这些缺点，测试的优点还是站在他们自己的立场上来解释为什么我们应该测试:

*   动态文档:每次添加测试时，它都会解释代码是如何工作的，以便技术人员和非技术人员理解。另一方面，测试是“活的”,因为它们将帮助您识别某些东西是否不再工作。因此，不要担心过去的问题，你可以专注于当前的任务。
*   成本效益:虽然我提到的一个缺点是测试可能会花费大量的时间和精力，但从长远来看，测试是有成本效益的。这是因为你越早发现错误，你就能越早(有时更快)修复它们。
*   质量:包含测试可以确保您正在构建一个高质量的产品，它是高性能的，并且不容易出错。总的来说，它验证了它对您和您的用户都起到了预期的作用。

# **🧪入门**

因为我们对测试有了更好的理解，我们可以开始用 Jest 进行测试的基本设置了！也就是说，需要注意的是每个框架/语言都有自己的测试库和框架。例如，这个演示使用 Jest，这是一个由脸书构建的 JavaScript 测试框架。

1.  要将 Jest 保存为依赖项(本地而非全局)，您可以运行以下命令之一:

```
npm install --save-dev jest
npm install i -D jest
```

2.在`package.json`中，添加到脚本:`“test”: “jest”`。另外，因为 Jest 要求你指定执行环境是 Node，所以在`package.json`的末尾或者通过一个单独的`jest.config.js`文件，添加`“jest”: { “testEnvironment”: “node” }`。

如果你愿意，你可以添加`“jest -- verbose”`或者`“jest --coverage”`，而不是简单地为你的脚本添加`“jest”`。`--verbose`意味着用测试套件层次结构来显示单独的测试结果，而`--coverage`表明测试覆盖信息应该被收集并在输出中报告。如果您添加了 jest 配置，您也可以在那里添加`“verbose”: true`来代替。

可选地，要在开发时运行测试，添加到脚本:`“testwatch”: “jest --watchAll”`，然后使用`npm run testwatch`进行开发。`--watchAll`表示当文件发生变化时，重新运行所有测试。

3.对于您想要测试的每个文件，测试文件应该以`.test.js`命名

4.否则，运行测试:`npm test`

## **奖金！调试 Jest 测试🐛**

正如我在利与弊部分提到的，编写测试的一个缺点是，有时不是你的代码错了，而是测试本身错了。为了有所帮助，我们可以使用 VSCode 或 Chrome 开发工具进行调试。如果您感兴趣并且有大约 20 分钟的空闲时间，请查看来自 Salesforce 开发人员的这两个视频以了解更多信息:

# 跟我一起练习👩‍🔬

现在我们知道了早期的步骤，让我们使用 Jest 来完成一个样本问题和我们自己的[单元测试](#c27d)！为了专注于用 Jest 测试而不是解决问题，我们将致力于经典的“FizzBuzz”问题的变体。如果你不熟悉 FizzBuzz，这里有一个简短的描述:

编写一个程序，按照以下规则在控制台记录从 1 到 n(包括 1 和 n)的数字:

*   对于三的倍数，打印“嘶嘶”而不是数字
*   对于五的倍数，打印“嗡嗡声”而不是数字
*   对于 3 和 5 的倍数，打印“fizzbuzz”

需要记住的重要一点是，打印到控制台是一种副作用，因此很难获取和测试。一个变通办法是创建一个定制的打印函数，该函数带有一个测试的模拟版本，用于验证定制函数是否已被调用。相反，我们的函数将返回一个数组，我们将测试返回的数组，看它是否符合规则。

以下是运行的基本命令以及您的`package.json`文件的外观，而不是解释设置步骤(详见上方的[):](#4590)

```
// commands 
npm init -y
npm i -D jest
```

接下来，使用以下内容创建一个`fizzbuzz.js`文件:

现在我们可以设置我们的测试了！关于使用“匹配器”编写测试的细节，Jest 使用它让你以不同的方式测试值，查看官方文档[和](https://jestjs.io/docs/using-matchers)[完整列表](https://jestjs.io/docs/expect)。首先，使用以下内容创建一个测试文件`fizzbuzz.test.js`:

为了进一步解释上面测试的内容:

*   第一个测试主要测试`fizzbuzz`是一个函数
*   其余的我们正在测试的结果有和/或没有的东西，包括`.not`
*   `.toEqual`的用途是递归比较(也称为“深度相等”)对象实例的所有属性，如果它们匹配的话
*   在第二个测试中，`.arrayContaining`比较期望的数组是否是接收到的数组的子集，是否可以用来代替`.toEqual`中的文字值
*   对于剩下的两个测试，`.toContain`是类似的，除了它专门检查 iterable 的数组是否包含特定的项

如果你想继续自己解决我们的 Fizzbuzz 问题，你会得到加分，因为你好 [TDD](#a901) ！否则，你可以在 [CodeSandbox](https://codesandbox.io/s/jolly-haslett-oyc07?file=/src/index.js) 或更低版本上使用我的解决方案。

# **结论🎆**

在我让一些人解释并真正分解如何做之前，我认为测试是一件非常可怕的事情。这主要是因为我认为通过测试，你是在故意让你的工作失败。作为一名一直在与完美主义倾向作斗争的舞者，想到如何打破自己的辛勤工作有点可怕。也就是说，虽然有一定数量的“突破”，但也有很多增长。毕竟，测试帮助你保持和稳定你以前做过的事情，因此允许你考虑和接受未来的可能性。

祝贺你坚持到最后，非常感谢你的阅读；我希望这有助于介绍和鼓励您开始测试您的工作。如果你喜欢这篇文章，请看看我的其他作品！可以从“ [JavaScript Promises 101](https://medium.com/geekculture/javascript-promises-101-14f28158e5b9) ”、“[Everything JavaScript Arrays&Array Methods](https://medium.com/weekly-webtips/everything-javascript-arrays-array-methods-5e5809ffa4ad)”或者“[如何用 Node.js 和 Express 建立一个简单的 API](https://javascript.plainenglish.io/how-to-set-up-a-simple-api-with-node-js-and-express-1d7c13afc7cc)

# **想要更多？查看其他资源！💾**

## Jest 测试简介

📖[https://www.youtube.com/watch?v=7r4xVDI2vho](https://www.youtube.com/watch?v=7r4xVDI2vho)

## 用 Jest 测试 React 应用

📖[https://www . smashingmagazine . com/2020/06/practical-guide-testing-react-applications-jest/](https://www.smashingmagazine.com/2020/06/practical-guide-testing-react-applications-jest/)
📖[https://fullstackopen.com/en/part5/testing_react_apps](https://fullstackopen.com/en/part5/testing_react_apps)

## 测试驱动开发

📖[https://www . freecodecamp . org/news/test-driven-development-what-it-is and-what-it-is-not-41 fa 6b ca 02 a 2/](https://www.freecodecamp.org/news/test-driven-development-what-it-is-and-what-it-is-not-41fa6bca02a2/)

## 测试用例与测试套件

📖[https://www.ibm.com/docs/en/elm/7.0.1?主题=脚本-测试用例-测试套件-概述](https://www.ibm.com/docs/en/elm/7.0.1?topic=scripts-test-case-test-suite-overview)

## 不同类型的测试

📖[https://www . atlassian . com/continuous-delivery/software-testing/types-of-software-testing](https://www.atlassian.com/continuous-delivery/software-testing/types-of-software-testing)
📖[https://kentcdodds . com/blog/unit-vs-integration-vs-e2e-tests](https://kentcdodds.com/blog/unit-vs-integration-vs-e2e-tests)📖[https://www.guru99.com/unit-testing-guide.html](https://www.guru99.com/unit-testing-guide.html)📖[https://www.guru99.com/integration-testing.html](https://www.guru99.com/integration-testing.html)📖[https://www.guru99.com/end-to-end-testing.html](https://www.guru99.com/end-to-end-testing.html)📖[https://www.testim.io/blog/e2e-testing/](https://www.testim.io/blog/e2e-testing/)📖[https://www.geeksforgeeks.org/component-software-testing/](https://www.geeksforgeeks.org/component-software-testing/)

## 单元测试和组件测试的区别

📖[https://www . geeks forgeeks . org/difference-between-component-and-unit-testing/](https://www.geeksforgeeks.org/difference-between-component-and-unit-testing/)
📖[https://www.guru99.com/component-testing.html](https://www.guru99.com/component-testing.html)

## 快照测试

📖[https://www . site pen . com/blog/snapshot-testing-利弊](https://www.sitepen.com/blog/snapshot-testing-benefits-and-drawbacks)
📖[https://kentcdodds.com/blog/effective-snapshot-testing](https://kentcdodds.com/blog/effective-snapshot-testing)

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)