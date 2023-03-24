# TypeScript 最佳实践—分号和空格

> 原文：<https://levelup.gitconnected.com/typescript-best-practices-semicolons-and-spacing-5be9c5963604>

![](img/dbf2bb410338e6fe928ba3e0b376f7e8.png)

Andrew Teoh 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

TypeScript 是一个简单易学的 JavaScript 扩展。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的类型脚本代码。

在本文中，我们将研究使用 TypeScript 编写代码时要遵循的最佳实践，包括删除多余的分号。

此外，我们还会了解一些间距约定，以及为什么要在我们的类型脚本代码中使用分号。

# 删除无用的分号

我们应该删除代码中无用的分号。

例如，不写:

```
let x = 5;;
```

我们写道:

```
let x = 5;
```

我们只需要在语句的末尾有一个分号。

# 用命名常数替换幻数

如果我们有一些数字被反复用作常数，但没有被赋值给一个常数，那么我们应该把它赋值给一个常数。

这样一改，价值就处处体现了。

此外，命名常数告诉我们数字的含义。

因此，与其写:

```
enum Foo { bar = 1 }
```

我们写道:

```
const NUMBER_OF_BARS = 1;
enum Foo { bar = NUMBER_OF_BARS }
```

# 没有未使用的表达式

我们的代码中不应该有未使用的表达式。

如果我们有，我们应该删除它们。

# 没有未使用的变量

同样，如果我们有未使用的变量，那么我们应该删除它们。

# 在定义变量之前不要使用它们

对于用`var`声明的变量，我们可以在它们被定义之前引用它们，但是值将是`undefined`。

这是因为变量被提升了。

`let`和`const`不吊就解决了这个问题。

因此，我们应该使用`let`或`const`变量。

这样，如果我们引用这些变量，我们会得到一个错误。

# 没有无用的构造函数

我们不应该有无用的构造函数。

它们包括:

```
class A {
  constructor () {
  }
}
```

或者:

```
class B extends A {
  constructor (value) {
    super(value);
  }
}
```

它们都是多余的，所以应该删除。

# 坚持使用反斜线、双引号或单引号

我们应该以一致的方式使用反斜杠或引号来声明字符串。

更好的是，我们应该使用反勾号，因为它们会创建更灵活的模板字符串。

它们允许表达式嵌入其中。

# 如果在函数内部没有使用 await，就不要使用 async

只有当我们不得不使用函数内部的东西时，我们才应该使用函数。

例如，如果我们有这样的东西:

```
const foo = async () => "bar";
```

那么我们应该使用一个正常的函数。

# 一致地返回期待值

我们应该让`return`和`await`在同一条线上，因为承诺可能还没有解决。

相反，将它们放在单独的行上。

唯一的例外是，我们可以将`return`和`await`放在同一个`try`块中，以捕捉另一个基于承诺的函数的错误。

例如，不写:

```
async function foo() {
  return await bar();
}
```

我们写道:

```
async function foo() {
  const val = await bar();
  return val;
}
```

或者:

```
async function foo() {
  try {
    return await bar();
  } catch (error) {}
}
```

# 需要分号，而不是自动插入分号

与其让 Javascript 解释器为我们放入分号，不如我们自己放进去。

因此，我们应该写:

```
return {
    name: "foo"
};
```

而不是:

```
return 
{
    name: "foo"
};
```

这与以下内容相同:

```
return;
{
    name: "foo"
};
```

![](img/22fa7021921cdca8fb70e5171c46ce84.png)

照片由[盒装水更好](https://unsplash.com/@boxedwater?utm_source=medium&utm_medium=referral)上 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 函数括号前有一致的间距

我们应该在函数括号前加上一致的间距。

例如，我们通常写:

```
function foo(x) {
  // ...
}
```

在 JavaScript 代码中。

# 结论

为了更好的可读性，我们应该在我们的类型脚本代码中保持一致的间距。

此外，我们应该在代码中添加分号，而不是让 JavaScript 解释器在意想不到的地方为我们添加分号。

应该删除重复的分号。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)