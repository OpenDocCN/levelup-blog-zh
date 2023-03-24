# 编写更健壮代码的 JavaScript 最佳实践—工具和测试

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-for-writing-more-robust-code-tools-and-tests-6aafd8e3578>

![](img/2f3114720b7ce15e2fad982506994b97.png)

由[谷仓图片](https://unsplash.com/@barnimages?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的 JavaScript 代码。

在本文中，我们将研究一些用于编写健壮的 JavaScript 代码的工具和测试最佳实践。

# 使用类型脚本

TypeScript 是一种语言，它是 JavaScript 的扩展，为 JavaScript 代码提供灵活和可选的类型检查。

它将代码构建到 JavaScript 中，以便可以在浏览器或 Node.js 环境中运行。

在将代码构建到最终工件之前，会检查代码的类型错误。这意味着防止了数据类型错误，如果没有被 TypeScript 检查，这将是运行时错误。

这是一大类被消除的错误。类型检查并不严格。它提供了许多方法来注释对象的类型和函数的返回类型。

例如，我们可以创建接口来注释对象类型，如下所示:

```
interface Pet {
    name: string;
    walk(): void;
}
```

在上面的代码中，我们创建了一个接口来指示任何具有类型`Pet`的东西都必须具有`name`字符串属性和一个不返回任何内容的`walk`方法(因此有了`void`返回类型)。

那么我们可以如下使用它:

```
const dog: Pet = {
    name: 'joe',
    walk() {
        console.log('walk');
    }
}
```

在上面的代码中，我们在`dog`对象中有了`name`字符串属性和`walk`方法，满足了接口的要求。

因此，代码将由 TypeScript 编译器生成并运行。如果任何东西的类型与接口不同，我们就会得到一个错误。

TypeScript 的另一个好特性是类型检查的灵活性。类型不必是固定的，它们也可以是灵活的和动态的。

例如，我们可以如下创建可空类型:

```
interface Pet {
    name: string;
    walk?(): void;
}
```

在上面的代码中，`walk`方法是可选的，方法名后面有`?`。现在我们可以在任何类型为`Pet`的对象中省略它。

我们还可以创建多种类型的联合类型，如下所示:

```
interface Pet {
    name: string;
    walk(): void;
}interface Animal {
    breed: string;
}const dog: Pet | Animal = {
    name: 'joe',
    breed: 'labrador',
    walk(){}
}
```

在上面的代码中，我们有`Pet | Animal`类型，它由组合在一起的`Pet`和`Animal`类型的成员组成。

因此，我们的`dog`对象可以包含每个接口的任何成员。

此外，我们不必直接指定对象的类型。如果我们的代码中已经有一个对象，并且我们希望另一个对象具有与那个对象相同的数据类型，我们可以使用如下的`typeof`操作符来指定类型:

```
const cat = {
    name: 'james'
}const dog: typeof cat = {
    name: 'joe'
}
```

在上面的代码中，我们用`typeof dog`表达式指定了`dog`对象的数据类型与`cat`的数据类型相同。这样，我们就不必直接指定任何类型。

TypeScript 有更多的特性，可以帮助我们编写更可靠的 JavaScript 代码，防止在运行时被捕获的数据类型错误。

这对于大型应用程序来说尤其重要，因为它们更有可能出现这些错误。

因此，我们应该考虑使用 TypeScript 来编写更健壮的 JavaScript 代码。

![](img/264d6fcfcdccb7d4a22b98393c8bd72f.png)

[Jerry Wang](https://unsplash.com/@jerry_318?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 编写测试

我们应该编写自动化测试来检查我们代码中的回归，这样我们在修改代码时就可以安心了，所有的自动化测试都通过了。

使用 Jest test runner 很容易编写自动化测试，它有许多特性让我们可以为前端和后端 JavaScript 应用程序编写和运行测试。

例如，我们可以用单元测试编写一个简单的函数，如下所示:

`math.js`:

```
const multiply = (a, b) => {
  return a * b;
}
module.exports = {
  multiply
};
```

`math.test.js`:

```
const math = require('./math');test('multiplies 1 * 2 to equal 2', () => {
  expect(math.multiply(1, 2)).toBe(2);
});
```

在上面的代码中，`math.js`模块有我们想要测试的函数`multiply`。

然后在`math.test.js`中，我们添加了测试由`math.js`导出的`multiply`函数的测试。

文件名是 Jest 测试的约定，其中产品代码文件的名称与测试文件的名称相对应。

然后当我们运行 Jest 时，这个测试将会运行并且应该通过，因为 1 乘以 2 等于 2。

如果我们为应用程序的每个部分都编写单元测试，那么就很容易知道我们的应用程序是否仍然正常工作。

# 结论

TypeScript 可能是 JavaScript 最好的类型检查器。向我们的 JavaScript 代码添加类型注释和检查是 JavaScript 的自然扩展。

编写自动化测试总是一个好主意，这样我们可以在修改代码后检查代码是否正常工作。