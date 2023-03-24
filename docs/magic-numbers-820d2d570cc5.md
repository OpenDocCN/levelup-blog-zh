# 幻数以及如何在代码中修正它们

> 原文：<https://levelup.gitconnected.com/magic-numbers-820d2d570cc5>

![](img/faafbe75a9bfa716efb2875bdcabe5b6.png)

> *这是关于提高代码可读性的一系列短文的一部分*

在这篇短文中，我想讨论幻数的用法，它们是什么，以及不使用它们如何使阅读代码变得更容易！

首先，你可能会问一个神奇的数字是什么？它通常是一个直接使用的数字，不存储在变量中。

一个简单的人为例子是:

```
const users = [{
  name: 'Matt',
  age: 30
}, {
  name: 'Austin',
  age: 20
}].filter(user => user.age >= 21)
```

这看起来没那么糟糕，但是说这个`21`数字被用在许多地方，并且在将来需要更新时，你需要在多个文件中找到所有需要更新的地方。将它存储为变量/常量，并在需要时导入，这使得更新更加容易。它还提高了可读性，因为变量名应该提供一些关于数字含义的上下文。

这可以重写为:

```
const { isOverDrinkingAge } = require('./constants')

const users = [{
  name: 'Matt',
  age: 30
}, {
  name: 'Austin',
  age: 20
}].filter(user => user.age >= isOverDrinkingAge)
```

现在看看这个，您可以看到用户的数组现在只包含超过饮酒年龄的用户。

为了帮助你的项目保持一致性，我建议使用类似于 [eslint](https://eslint.org/docs/rules/no-magic-numbers) 的东西，确保你启用了`no-magic-numbers`规则。

感谢阅读！

*最初发布于*[*https://mattchaffe . uk*](https://mattchaffe.uk/posts/readable-code--magic-numbers)*。*