# 如何在 JavaScript 中对私有(非导出)函数进行单元测试

> 原文：<https://levelup.gitconnected.com/how-to-unit-test-a-private-non-exported-function-in-javascript-14acc9d83216>

![](img/74fcda31272aefa87e2783811b8b28fb.png)

*原载于 2020 年 11 月 19 日*[*【https://www.wisdomgeek.com】*](https://www.wisdomgeek.com/development/web-development/javascript/how-to-unit-test-private-non-exported-function-in-javascript/)*。*

在为 JavaScript 模块编写单元测试时，我们经常会遇到一个两难的问题，即模块有一些私有函数没有被导出。测试一个已经被导出的函数是很容易的，因为它可以被导入到单元测试框架中，并且功能可以被测试。但是如何对私有(非导出)函数进行单元测试呢？

# 测试导出的函数

在讨论私有函数之前，让我们先熟悉一下如何在 JavaScript 中测试导出的函数。我们将使用 [Jest](https://www.wisdomgeek.com/development/web-development/how-to-setup-jest-typescript-babel-webpack-project/) 来编写这个单元测试。让我们假设函数 foo 为:

```
// foo.js
export function foo() {
  return 'bar';
}
```

然后，我们将编写相应的单元测试用例来测试导出函数的功能，如下所示:

```
// foo.test.js
import { foo } from './foo.js'

describe('testing foo', () => {
  expect(foo()).toBe('bar');
});
```

现在我们已经熟悉了导出的函数，让我们进入下一部分。让我们先看看问题是什么。

# 非导出(私有)函数

非导出函数类似于此文件中的 secret 函数:

```
// foo.js
let secret = () => '🤫'
export function foo() {
  return secret();
}
```

现在，如果我们要在单元测试中测试 baz，

```
// foo.test.js
import secret from './foo.js'

// secret is undefined
```

**注意:**我们一般不要单独测试私有函数。它们是实现细节的一部分。如果一个私有函数行为不正确，那么使用它的公开函数也会行为不正确。这样，通过测试公共接口，我们也在测试私有函数。但是有些情况下，比如私有函数太复杂，可能需要这样做。另一个场景可能是在一个公共函数中一个接一个地调用多个私有函数，我们需要单独测试它们。

既然我们知道问题是什么，让我们想想可能的解决办法。

最明显的一个方法是不要将“secret”作为私有函数，而是从模块 foo 中导出它。虽然这是解决这个问题的快捷方法，但不是正确的方法。为了对我们的模块进行单元测试，我们公开了很多功能，这可能会带来很多安全风险。

# Rewire 简介

用其他语言编写过单元测试之后，我知道应该有更好的解决方案。我在寻找一些东西，让我将私有函数作为模块的私有成员，但仍然使它们可测试。而 [Rewire](https://www.npmjs.com/package/rewire) 给我提供了这个功能。

虽然 Rewire 是作为单元测试的猴子补丁解决方案引入的，但官方描述称:

Rewire 为模块添加了一个特殊的 setter 和 getter，这样你就可以修改它们的行为来进行更好的单元测试。您可以:

*   像流程一样为其他模块或全局注入模拟
*   检查私有变量
*   覆盖模块中的变量。"

第二点是我们需要什么来解决我们的问题！

# Rewire 和 ES6

rewire 的常见 js 实现不支持 ES6。由于我们在项目中使用 ES6 导入，我们希望使用一个包将概念转移到 ES6。这就是 rewire 的 babel 插件发挥作用的地方。

babel-plugin-rewire 的灵感来自 rewire.js，并使用 babel 将概念转移到 ES6。因此，我们将使用 npm/yarn 在我们的项目中安装它:

```
npm install babel-plugin-rewire --save-dev

or

yarn add --dev babel-plugin-rewire
```

在 babel 配置文件中，我们需要导入它:

```
// babel.config.js
module.exports = {
  plugins: ['babel-plugin-rewire'],
  ...
};
```

# 对私有函数进行单元测试

现在我们已经使用 babel-plugin 进行了 Rewire，我们可以使用 __get__ 方法访问私有/非导出函数。模块上的这个方法充当了一个 getter，允许我们提取私有函数:

```
// foo.test.js
import foo from './foo.js'

describe('testing foo', () => {
  const secret = foo.__get__('secret'); // rewire magic happens here
  expect(secret()).toBe('🤫');
});
```

这样，我们就可以鱼和熊掌兼得了。我们能够神奇地调用私有函数，而不用从模块中导出它们。我们不再需要求助于黑客来获取对未导出的 JavaScript 函数的引用，并且可以确保函数保持私有。

我们希望这篇文章能帮助你理解如何使用 Rewire 对 JavaScript 中的私有(非导出)函数进行单元测试。上面讨论的方法不仅适用于 Jest，也适用于其他单元测试框架。我们不需要做任何代码重构或者仅仅为了测试而导出我们的私有函数。如果您正在为 JavaScript 编写单元测试用例，您可能也会对我们在 Jasmine 博客文章中的[运行特定的单元测试用例感兴趣。](https://www.wisdomgeek.com/development/web-development/javascript/running-specific-test-cases-in-jasmine/)

如果你有什么想补充的，或者你认为我们接下来应该报道的，请在下面留言告诉我们。