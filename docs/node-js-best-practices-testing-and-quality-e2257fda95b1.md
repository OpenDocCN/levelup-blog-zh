# Node.js 最佳实践—测试和质量

> 原文：<https://levelup.gitconnected.com/node-js-best-practices-testing-and-quality-e2257fda95b1>

![](img/128912b5ebc6fb42376027c140daa1ce.png)

本·穆林斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Node.js 是编写应用程序的流行运行时。这些应用程序通常是许多人使用的生产质量应用程序。为了使维护它们变得更容易，我们必须制定一些准则供人们遵循。

在本文中，我们将研究如何测试和维护 Node.js 代码的质量。

# 至少编写 API(组件)测试

我们应该一有时间就写测试。测试通过检查现有功能是否仍在工作来防止回归。这样，我们就不必担心我们的代码更改会破坏任何关键功能。

为我们的应用程序创建测试很容易。我们可以使用 Jest 这样的测试框架和 Superagent 这样的库来测试我们的 API。

此外，我们应该确保代码覆盖率高，这样我们就可以用我们的测试来测试大部分代码。

例如，我们可以通过运行以下命令，使用 Jest 和 Supertest 轻松地向 Express 应用程序添加测试:

```
npm i jest supertest
```

然后我们写下以下内容:

`index.js`:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res, next) => {
  res.json({hello: 'hello'});
});module.exports = app;
```

`index.test.js`:

```
const request = require('supertest');
const app = require('./index');describe('hello test', () => {
  it('/ should return hello response', async () => {
    const res = await request(app)
      .get('/')
    expect(res.statusCode).toEqual(200)
    expect(res.body).toEqual({hello: 'hello'})    
  })
})
```

在代码文件中，我们将`supertest`添加到我们的`index.test.js`测试文件中。然后，我们通过向`/`路由发出请求并检查响应代码和主体来测试我们的路由。

`toEqual`方法检查深度相等，这样我们可以检查任何值。

# 每个测试名称包含 3 个部分

每个测试名称应该包括 3 个部分，这样阅读测试用例的人就知道测试的是什么。测试名称应该包括正在测试的内容、场景以及预期的结果。

# AAA 模式的结构测试

AAA 代表安排、行动和断言。测试的第一部分应该为我们的测试设置数据。然后我们实际运行代码，然后断言返回的结果是我们所期望的。

有了这些类型的测试，我们仅仅通过查看测试就理解了主要代码。

# 检测 Linter 的代码问题

我们应该使用代码处理器来检查我们代码的基本质量问题。间距、格式和语法错误都在它的范围内。它还检查常见的反模式，以确保我们的代码没有它们。带有节点插件的 Linters 也可以检查我们代码的安全问题。

没有棉绒，很容易忽略它们。因此，我们可能会运行质量很差的代码，并且代码中存在安全漏洞。

# 避免全局测试夹具和种子

每个测试都应该有自己的测试夹具和种子，这样它们就可以独立运行，不需要任何其他东西。这很重要，因为我们需要测试不依赖于任何外部事物。它使得测试易于添加和调试。

它减少了很多令人头疼的测试。他们不应该依赖任何外部因素。

![](img/1cd1db5cb0459efdc25104dcbcbf4240.png)

[阿尼·科勒希](https://unsplash.com/@anikolleshi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 检查易受攻击的依赖项

我们可以使用`npm audit`或 snyk.io 来检查易受攻击的依赖项，以便尽快更新这些包。

有了这些自动化工具，我们无需自己动手就可以检查易受攻击的包。

# 标记测试

标记测试让我们可以轻松地搜索它们。我们可以将标签添加到测试名称中，这样我们就可以很容易地找到它们。

# 检查测试覆盖率

测试覆盖让我们检查测试是否运行了足够多的代码部分。代码覆盖工具突出显示了测试运行了代码的哪些部分，没有运行。然后我们可以看看我们需要运行什么样的代码来增加代码的测试覆盖率。

如果测试覆盖率低于某个阈值，我们也可能希望构建失败。

# 检查过期的包装

我们可以使用`npm outdated`和`npm-check-updates`来检查过期的包，以检测过期的包。如果我们有过时的包，我们也可以在 CI 管道中运行它来防止构建成功。

这样，我们就不会在应用程序中使用过时的包。

# 结论

使用节点应用程序添加测试很容易。它让我们不费力地检查回归。当我们划分测试来测试代码的小片段时，添加测试并不费力。对于 Jest 这样的测试框架和 Superagent 这样的测试 HTTP 客户端来说，这尤其容易。

我们还应该检查易受攻击的软件包，并尽快更新它们。此外，还要检查自己的代码是否存在安全漏洞，并尽快修复。