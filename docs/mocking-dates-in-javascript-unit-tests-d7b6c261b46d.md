# JavaScript 单元测试中的模拟日期

> 原文：<https://levelup.gitconnected.com/mocking-dates-in-javascript-unit-tests-d7b6c261b46d>

![](img/42e3c499923d74f3473bba80e949c276.png)

测试我们的应用程序可能是乏味和耗时的，这就是为什么我们编写自动化测试以确保我们的代码工作，而不需要我们自己花费过多的时间来做这件事。然而，我们如何测试在代码执行过程中发生变化的变量呢？比如时间戳？让我们找出答案。

下面的例子将使用`mocha`测试框架和`chai`断言库，以及`mockdate`包来说明。所有这些都是 NPM 上免费提供的软件包。

# **场景**

因此，让我们以一个相当常见的用例为例，假设我们有一个用时间戳更新一些数据的函数，以指示最近一次交易发生的时间。因此，如果我们有一个基本函数，它只创建一个带有附加日期的更新有效负载，它可能看起来像下面这样——作为一个非常简单的例子。

```
function createUpdatedUser(user) {
  const date = new Date().toISOString() return {
    ...user,
    updatedAt: date
  }
}
```

在上面的代码片段中，我们可以看到我们的用户获得了一个新的附加日期，这表明了它的更新时间。在我们的单元测试中，我们希望断言该函数返回的数据是我们期望的数据。

```
describe('#createUpdateUser', () => {
  it('returns the user with an `updatedAt` property', () => {
    const expectedData = {
      ...user,
      updatedAt: "2020-04-13T18:09:12.451Z"
    }

    const updatedUser = createUpdateUser(user)
    return expect(updatedUser).to.equal(expectedData)
  })
})
```

然而，当我们运行这个测试时，我们将得到一个错误，表明函数返回的数据与我们定义的预期数据不匹配。显然，这是因为从我们编写这个测试的时间到我们运行这个测试的时间，日期已经改变了。

# 引入模拟日期

`mockdate`是一个非常有用的库，允许我们在 JavaScript 中模拟日期。值得注意的是，它也有可用于 TypeScript 项目的类型定义。

那么我们如何设置`mockdate`在我们的测试框架内工作呢？嗯，这很简单。首先我们应该导入我们需要的函数。

```
import { set, reset } from 'mockdate'
```

然后，当应用程序请求时间戳时，我们应该`set`在`mockdate`之前返回我们想要的日期。

```
const date = '2020-04-13T18:09:12.451Z'
set(date) // Any request to Date will return this date
```

最后，我们应该调用`reset`函数来阻止`mockdate`返回我们之前定义的时间戳。

```
reset() // We just need to call this function
```

顺便提一下，我通常会将`set`放在`beforeEach`步骤中，将`reset`放在`afterEach`步骤中，以减少测试用例交叉污染的可能性。

# 这一切看起来像什么？

当我们将所有的测试功能组合在一起时，它可能看起来像下面这样。在我们的实际测试中没有任何变化。我们所做的就是在运行测试之前为 JavaScript 的原生`Date`功能设置一个模拟。

```
import { expect } from 'chai'
import { set, reset } from 'mockdate'describe('SomeUserFile', () => {
  const user = {
    firstName: 'Basil',
    lastName: 'Fawlty'
  }
  const date = '2020-04-13T18:09:12.451Z'

  beforeEach(() => {
    set(date)
  }) afterEach(() => {
    reset()
  }) describe('#createUpdateUser', () => {
    it('returns the user with an `updatedAt` property', () => {
      const expectedData = {
        ...user,
        updatedAt: date
      }

      const updatedUser = createUpdateUser(user)
      return expect(updatedUser).to.equal(expectedData)
    })
  })
})
```

现在，如果我们运行测试，它应该会通过，因为当调用`Date`时，`mockdate`会加入并返回我们期望的时间戳。这使得在响应数据中断言非常容易。

额外的好处是——当使用时间库时，比如`momentjs`,这是一个相当强大的时间操作工具。

![](img/a8ff8224b9ce1e1efbae28d45864bbfd.png)

# 那都是乡亲们！

总之，在 JavaScript 中剔除我们的日期是非常简单的。它没有改变我们现有的测试，因为所有的设置都发生在单独的测试用例之外。如果这是您的特定用例，您也可以总是在特定的测试用例中设置日期，但是不要忘记重置，因为您不一定希望模拟的日期泄露到您的下一个测试用例中。祝测试愉快，好运！

这里有一些参考资料，如果您希望了解更多或者开始在自己的测试套件中实现这些库，这些资料可能会激发您的兴趣。

[莫卡吉斯](https://mochajs.org/)
[柴吉斯](https://www.chaijs.com/api/bdd/)

[NPM 摩卡](https://www.npmjs.com/package/mockdate)
[NPM 摩卡](https://www.npmjs.com/package/mocha)
[NPM 柴](https://www.npmjs.com/package/chai)

[MDN 日期](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)

感谢你的阅读，我希望你喜欢并学到了一些东西。如果你碰巧有任何反馈、批评或贡献，请随意写在下面的评论区。

再见韦德森！