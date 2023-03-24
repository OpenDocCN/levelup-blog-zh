# TypeScript 花絮—泛型类型参数

> 原文：<https://levelup.gitconnected.com/typescript-tidbits-generic-type-parameter-82859ffb2582>

偶尔你可能会发现自己编写了一个函数，它可以采用许多不同的类型。Typescript 允许您为此创建一个泛型类型参数。

![](img/c5ab1afaea126d3b8afc7c64678679cc.png)

泛型类型参数是一个占位符，我们可以用它在多个地方强制实施类型级约束。如果我们浏览一个例子，这是最简单的。

让我们看看泛型的“Hello world”——identity 函数。identity 函数只是返回传入的内容。在普通的 JavaScript 中，它看起来像这样:

```
function identity(arg) {
  return arg
}
identity(2) // returns 2
identity("Hello") // returns "Hello"
```

在 Typescript 中编写这些内容时，我们可能会尝试使用`any`类型来涵盖我们可以传入的所有特定情况:

```
function identity(arg: any): any {
  return arg
}
```

尽管这样做是可行的，但是我们丢失了很多关于函数返回类型的信息，所以我们在这里使用 TypeScript 没有得到任何东西。

我们可以重载这个函数，赋予它多种类型:

```
type Identity = [
  (arg: string): string
  (arg: number): number
]
function identity: Identity (arg) {
  return arg
}
```

请注意，在这个例子中，我已经将类型签名分开，以使事情更整洁。这个方法*可以*工作，但是它过于冗长，并且当你试图包含所有需要的类型时会变得混乱。

幸运的是，TypeScript 允许您定义“泛型类型参数”。我们可以重写我们的示例来利用这一点:

```
function identity<T>(arg: T): T {
  return arg
}
```

注意这里搞笑的尖括号(`<T>`)。这就是我们如何定义一个类型变量`T`。TypeScript 将把传递给`identity`的类型存储在`T`变量中，这允许我们在类型签名中引用它。

我们现在可以用两种方式来称呼它。

1.  我们可以明确我们传入的类型:
    `let result = identity<string>(“Hello”) // typeof result === ‘string’`
2.  我们可以让编译器推断出类型:
    `let result = identity(42) // typeof result === ‘number’`

我们的解决方案现在非常通用，因为它可以与一系列类型一起工作，但是它比使用`any`更精确，因为我们不会丢失任何信息。

一个旁注——当像这样对泛型使用类型别名时:

```
type Identity<T> = {
  (arg: T): T
}
function identity: Identity (arg) {
  return arg
}
```

当我们调用函数时，我们用**和**来定义我们的类型，否则我们会得到一个错误:

```
let valid = identity<string>("Hello") 
// valid === "Hello"let invalid = identity("Hello") 
// Error TS2314: Generic type 'Identity' requires 1 type argument(s)
```

# 约束我们的泛型类型参数

假设我们想让我们的身份函数也记录参数的长度。我们可能会忍不住这样写:

```
function identity<T>(arg: T): T {
  console.log(arg.length)
  return arg
}
```

然而，我们的编辑应该在这里强调一个错误:

```
Property 'length' does not exist on type 'T'.
```

这是有意义的，因为我们还没有定义具有长度属性的类型。如果我们只对用于数组的函数感兴趣，我们可以将代码更改为以下代码并消除错误:

```
function identity<T>(arg: Array<T>): Array<T> {
  console.log(arg.length)
  return arg
}
```

这将适用于任何类型的数组。如果我们希望它也能处理简单的字符串，我们可以创建一个接口:

```
interface HasLength {
  length: number
}

function identity<T extends HasLength>(arg: T): T {
  console.log(arg.length)
  return arg
}
```

现在我们可以用数组或字符串来调用它，它会像预期的那样工作，但是如果我们试图用一个数字来调用它，我们会得到下面的错误消息:

```
Argument of type ‘number’ is not assignable to parameter of type ‘HasLength’.
```

## 脚注:

1.  我们可以为我们的类型变量(`T`)使用任何我们喜欢的名字，但是通常使用单个大写字符。
2.  允许编译器尽可能推断类型通常更好，但是每个人都有自己的偏好。

[*获得无限制的介质*](https://richard-t-bell90.medium.com/membership)

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)