# 为什么测试代码很重要

> 原文：<https://levelup.gitconnected.com/why-testing-your-code-is-important-5c13e43a0a6b>

测试您的代码是软件开发过程的重要部分。它是你如何确认你的代码如你所愿的那样运行。它还允许您在 bug 有机会在生产中造成问题之前，尽早发现并修复它们。

![](img/e80b99eca316e6fc4526d2772cc6b1ec.png)

在这篇文章中，我们将回顾为什么测试你的代码是重要的。测试代码有许多不同的方法，我们将讨论最重要的方法——单元测试。

# 总是测试你的代码

在我们讨论*为什么是*之前，让我们先讨论一下*是什么*。什么是代码测试？**确保你的代码按照你想要的方式运行**。就这么简单。

现在，你为什么要测试你的代码呢？假设你有一个只有一个模块的程序。它只对一件事负责。测试它可能看起来有点矫枉过正(但是我建议总是测试您的代码)。现在想象你的程序有五个模块，它们做不同的事情。此外，模块之间可以相互通信。你的程序现在有许多可能的状态，手动测试所有的状态会占用你很多宝贵的时间。

相反，您应该编写测试来确认行为！通过测试你的代码，当你添加新的代码时，你也可以保护自己不被破坏。你想写高质量的代码，对吗？不测试是不可能的。

# 单元测试您的代码

您可以对代码运行各种类型的测试，但单元测试是最重要的。它们允许您单独测试单个代码单元。

让我们来看看这个 JavaScript 函数。

```
const incrementByOne = input => {
  if (input < 0) {
    return 0;
  } return input + 1;
};
```

现在让我们写一些测试。我喜欢从测试“快乐之路”开始。

```
test('should increment by 1', () => { const input = 9; const actual = incrementByOne(input);
  const expected = 10; expect(actual).toBe(expected);
});
```

接下来是有趣的部分。边缘案例！

```
test('should return 0 when negative input', () => { const input = -1; const actual = incrementByOne(input);
  const expected = 0; expect(actual).toBe(expected);
});
```

我们现在已经测试了所有的东西，并且有 100%的测试覆盖率。我们很快乐，还是我们很快乐？

我选择 JavaScript 是因为它是动态类型化的，我们可以将任何类型传递给我们的函数。这是使用[*TypeScript*](https://www.typescriptlang.org/)的好理由。

如果我们通过了一个`string`会发生什么？

```
test('should increment by 1', () => { const input = 'my-string'; const actual = incrementByOne(input);
  const expected = ???; expect(actual).toBe(expected);
});
```

该函数将返回`my-string1`，因为`+`操作符在 JavaScript 中的类型之间工作。这不是我们想要的，所以让我们解决它。

让我们更新函数来处理这种情况。

```
const incrementByOne = input => {
  if (typeof input !== 'number') {
    throw new TypeError('Only number type allowed');
  } if (input < 0) {
    return 0;
  } return input + 1;
};
```

现在让我们在测试套件中添加另一个测试。

```
test('should throw when non-number input', () => { const input = 'my-string'; const expected = new TypeError('Only number type allowed');
  expect(() => incrementbyOne(input)).toThrowError(expected);
});
```

这是一个简单的例子。网站和程序从来都不是微不足道的，我希望你现在能看到测试你的代码的价值。测试代码是交付高质量软件的关键部分。这应该是你软件开发过程中的一个基本部分！

单元测试不是银弹，也不能替代其他形式的测试，比如系统测试和用户验收测试。但是，单元测试是软件测试过程的重要部分，不应该被忽视。

# 结论

对于代码来说，测试是避免错误和确保代码按预期运行的关键。通过花时间测试您的代码，您可以避免代价高昂的错误，并确保您的代码准备就绪。

喜欢这篇文章吗？然后检查出[停止使用无意义的测试值！](https://medium.com/@prplcode/stop-using-meaningless-test-values-9c8f9298ac48)

*在* [*Twitter*](https://twitter.com/prplcode) *，*[*LinkedIn*](https://linkedin.com/in/simeg)*，或者* [*GitHub*](https://github.com/simeg) 上与我联系

*最初发布于*[*prplcode . dev*](https://prplcode.dev/)