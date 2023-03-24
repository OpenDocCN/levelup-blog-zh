# 编写更健壮代码的 JavaScript 最佳实践——干净的代码

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-for-writing-more-robust-code-clean-code-f1730db3441d>

![](img/d4aac5dd40de5e831fa783fae45c8d0e.png)

[疾控中心](https://unsplash.com/@cdc?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的 JavaScript 代码。

在本文中，我们将研究一些保持 JavaScript 代码整洁易读的方法，这样修改代码就变得轻而易举了。

# 保持代码有条理

我们的 JavaScript 代码应该组织得很好，这样它们就很容易理解。组织良好的代码不要重复任何东西。

里面的函数和类都只做一件事，仅此而已。它们基本上是相互独立的，它们只向外部世界公开需要的东西，这是避免紧密耦合的最低要求。

如果我们从一开始就干净利落地组织事情，这很容易做到。例如，我们可以编写以下代码来保持整洁:

`fruit.js`

```
export class Fruit {
  constructor(name) {
    this.name = name;
  }
}
```

`person.js`

```
export class Person {
  constructor(name) {
    this.name = name;
  }
}
```

`index.js`

```
import { Fruit } from "./fruit";
import { Person } from "./person";
const sentence = `${new Person("foo").name} likes ${new Fruit("apple").name}`;
console.log(sentence);
```

在上面的代码中，我们有 3 个模块。前 2、`person.js`和`fruit.js`都有一个类。

他们每个人都有一个类，而这个类只代表一件事。`Person`类代表一个人，`Fruit`类代表一种水果。

然后在`index.js`中，我们将它们都导入，并在字符串中引用它们。

按照我们组织代码的方式，这些类不会相互引用。每个类只做一件事，我们只在必要的时候引用这些类。

# 不要写聪明的代码

聪明的代码是不好的，因为它们通常难以阅读，即使它们通常更短。

简单的解决方案比巧妙的代码更易于维护。聪明但难读的代码不好，因为它们很难理解。

例如，下面的代码是一个聪明但难以阅读的代码的例子:

```
const foo = bar ? bar ? qux ? 0 : 1 : 2 : 3;
```

在上面的代码中，我们在一行中使用了一系列嵌套的三元运算符，这比直接写出每个`if`语句要短，但很难读懂。

很多人可能不明白它在做什么。他们必须在脑子里写出括号，这样他们才能知道每个三元运算的开始和结束。

因此，我们不应该像那样使用嵌套的三元运算符。相反，我们应该编写 use `if`语句，或者只使用三元运算符来为一个条件返回这样或那样的结果。

调试比编程更难，我们经常要重新访问旧代码进行调试和更改。因此，我们不应该通过编写难以阅读的聪明代码来使我们的工作变得更加困难。

![](img/4b7c215f2443850c7e704fd19faeca11.png)

照片由 [Charis Gegelman](https://unsplash.com/@wildfernstudio?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 以易于测试的方式拆分代码

我们的代码应该以一种易于测试的方式来组织。这意味着它们应该向外部公开，以便可以用单元测试对它们进行测试。

我们还应该致力于编写纯函数。对于给定的一组输入，纯函数总是返回相同的输出。这意味着它们很容易被测试。

函数中的副作用应该被最小化，这样我们在测试时就不必检查函数之外的代码的正确性。

例如，我们可以编写如下代码和测试:

`math.js`

```
const add = (a, b) => a + b;
const subtract = (a, b) => a - b;
module.exports = { add, subtract };
```

`math.test.js`

```
const { add, subtract } = require('./math');test('adds 1 + 2 to equal 3', () => {
  expect(add(1, 2)).toBe(3);
});test('adds 1 - 2 to equal -1', () => {
  expect(subtract(1, 2)).toBe(-1);
});
```

在上面的代码中，我们有`math.js`和我们想要测试的函数。它们都是纯函数，所以对于给定的一组输入，它们总是返回相同的输出。

然后在`math.test.js`中，我们用 Jest 运行 2 个测试来测试我们从`math.js`模块导入的 2 个函数。

我们调用了`add`和`subtract`来测试它们，并用 Jests 内置的`expect`和`toBe`方法检查它们的值。

我们希望以这种方式组织我们的代码，这样我们就可以轻松地公开它们进行测试。

此外，它们没有副作用，因此可以自行测试，而无需检查外部代码的结果。

# 结论

代码组织对于编写健壮的 JavaScript 很重要，因为很难破坏干净的代码。它们不应该紧密耦合。还有，副作用要尽可能的消除，以便于测试和推理。

最后，聪明的代码应该避免，因为它们通常难以阅读。