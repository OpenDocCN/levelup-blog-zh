# 无错代码终究不是梦| StandardJS 16.0

> 原文：<https://levelup.gitconnected.com/error-free-code-is-not-just-a-dream-after-all-standardjs-16-0-bf66e09aec1d>

![](img/0f7c07027a5d5fef99d0be5ba3acc299.png)

[Max 陈](https://unsplash.com/@maxchen2k?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

在本文中，我们将学习编写完美的代码。格式完美的代码，没有任何语法或语义错误。那不是梦吗？是的，确实如此，现在让我们看看如何使用一个简单的 javascript 库 Standard.js(也称为标准样式)来实现这个美好的梦想。

# 为什么需要标准样式？

在我们深入研究如何使用标准样式的细节之前，让我先解释一下为什么我们应该使用它！你们中的一些人可能已经在使用一个 *linter* 和一个 *formatter、*的组合，比如 ESLint(linter)和 prettle(formatter)，并且肯定想知道是否值得改用 Standard.js。

> 棉绒抓虫子。它主要是在你的代码中寻找程序员的错误。
> 
> JSLint 是 2002 年发布的第一个原始 javascript linter。然后，2010 年推出的 JSHint 提供了更多的规则和额外的可配置性。现在，大多数人使用的是 2013 年推出的 ESLint，它有更好的 ES 支持和额外的样式规则，以迎合您的代码应该看起来如何。这也有一个很好的插件支持，允许开发人员创建自己的规则，因此，目前，它在技术行业作为 javascript 的 linter 广泛流行。

所以，回到为什么你需要标准风格。简单来说，**节省你的时间！**

这是一个集 **javascript 风格指南、** **linter、**和**格式化器**于一体的工具。这个模块拯救了你(和其他人！)时间有三种方式:

*   **无配置:**在项目中实施代码质量的最简单方法。不用做决定。没有要管理的`.eslintrc`文件。它只是工作。
*   **自动格式化代码:**只需运行`standard --fix`，告别杂乱或不一致的代码。
*   **尽早发现风格问题和程序员错误:**通过消除评审者和贡献者之间的来回沟通，节省宝贵的代码评审时间。

*来源:JavaScript 标准样式官方文档*

# StandardJS 可以捕捉的错误类型？

让我们看几个 StandardJS 可以快速捕捉的错误类型的例子。

## 示例 1:

```
const processUserData = 
(username, age, firstname, lastname, email,    age, gender, phone, city) => { 
    console.log('accessing age: ', age); 
}
```

**解释:**

参数'*年龄'*出现两次，分别在第二位和第六位。这可能是复制粘贴出错的结果，或者可能是新开发人员试图重构代码。对我来说重要的是，在 Javascript 中，函数中有重复的参数是完全允许的，除非你使用的是严格模式。在严格模式下，javascript 解释器会将其标记为错误。讨论这对您的代码有多大的欺骗性；当函数中有多个同名的参数时，最后一个出现的参数会覆盖第一个出现的参数。例如，在上面提到的例子中，当我们试图访问' *age'* 变量时，我们将获取最后一个' *age'* (在第 6 个位置)的值。现在的问题是，你不一定知道这段代码有问题，除非你花上几个小时调试整段代码，因为这并不简单。

## 示例 2:

```
if ( user.preference = "mango" ) {
   //do something 
}
```

解释:这个问题很微妙，但是很容易犯，而且很常见。在条件语句中，很容易键入错误的比较运算符，例如，*严格相等运算符(===)* 或*宽松相等运算符*又名*类型强制运算符(==)* ，用于*赋值运算符(=)。*现在，这个`if`语句将总是被评估为真，因为程序员错误地将值*‘mango’*赋给了*‘user’*对象的*‘preference’*字段。现在，这个`if`块将一直执行。虽然，人类的眼睛很难发现这些概念上的错误，但是，对于一个过敏者来说，发现它们是很容易的。

# 谁使用 JavaScript 标准样式？

几乎所有科技行业的巨头都使用这个库。

![](img/be09a8bd39af0060328a2487a042d6b6.png)

*来源:JavaScript 标准样式官方文档*

# 装置

将库作为开发依赖项安装。

```
npm install standard --save-dev
```

# 使用标准

运行 standard 来检查您的代码库中有哪些错误。

```
npx standard
```

修复格式和其他语义错误。

```
npx standard --fix
```

这将使 StandardJS 自动修复您的代码问题。它不能解决的只是一些需要手动修复的模糊错误。

# 足够聪明去做这件事

您可以在 *package.json* 文件中向您的测试脚本添加标准样式。现在，每次在构建代码之前运行`npm test`，标准的健全性检查都是隐式自动运行的，每次对代码做一些修改时，你不必在命令行上手动运行任何标准命令。

```
{ 
    "name": "your-package-name", 
    "devDependencies": { 
           "standard": "*" 
    }, 
    "scripts": { 
           "test": "standard && node your-test.js" 
    } 
}
```

此外，您可以搜索市场来查找特定于您的代码编辑器的可用 StandardJS 插件。有关这方面的更多信息，请查看 JavaScript 标准风格官方文档。

所以，这就是这个有用的 Javascript 库。我希望本文提供的信息能够为您提供价值，并帮助您轻松地编写和维护代码。

有用的资源:

*   [JavaScript 标准样式官方文档](https://standardjs.com/)

【https://www.theimmigrantprogrammers.com】最初发表于[](https://www.theimmigrantprogrammers.com/p/error-free-code-is-not-just-a-dream)**。**