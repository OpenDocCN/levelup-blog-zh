# TypeScript，第 3 部分:使用自定义类型

> 原文：<https://levelup.gitconnected.com/typescript-part-3-working-with-custom-types-929ed9a80389>

![](img/79fff1ee82dfe6caa0d7c9ea304aa146.png)

[戴红色帽子的女孩](https://unsplash.com/@girlwithredhat?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/custom?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

> 本文是 TypeScript 系列文章的一部分:
> 
> [打字稿，第一部分:简介](/typescript-part-1-an-introduction-b00ade194c4)
> 
> [TypeScript，第 2 部分:将 TypeScript 添加到您的应用程序中](/typescript-part-2-adding-typescript-to-your-application-cabdc7d70cae)
> 
> **类型脚本，第 3 部分:使用自定义类型**

将 TypeScript 与本机类型结合使用虽然很重要，但在任何现代应用程序中，这种做法很快就会令人感到力不从心。这是因为，尽管在 JavaScript 中，我们可能不会用这些术语来思考它，但我们确实在使用我们自己的类型。

一个应用程序的用户——及其`first_name`、`last_name`、`email`、`phone`——可以被认为是它自己的类型。由`title`、`author`和`body`组成的博客帖子是另一种类型。如果我们能够利用 TypeScript 的所有优势，并将它们应用于我们最独特的需求，那该有多棒？你也许能明白我的意思——你能！

输入自定义类型。自定义类型就像它们听起来的那样。可以创建一个`status`类型，它只是一个字符串，但是可以防止您意外地传递另一个应该传递状态的字符串(如`name`),并将状态`John`呈现给名为 John 的用户。您可以创建`interfaces`，它提供了大量的灵活性，并且是创建 React 道具类型的标准最佳实践。我最喜欢的 TypeScript 部分之一——许多第三方库现在都提供类型信息，如果你曾经使用过需要大量、深度嵌套的道具的依赖项，你就会明白为什么这是一件美好的事情。

# 自定义类型

正如我前面所说的，**自定义类型**是您，开发人员，为您的特定应用程序用例创建的任何类型。通常情况下，它们是对象，允许您将一组特定的键组合在一起，准确地说明应该使用哪些键，哪些键是必需的，哪些键是可选的，以及键值的预期类型。

但是，它们不需要成为对象。你真的可以把任何东西变成自定义类型。如果您正在构建一个写博客的应用程序，您可能希望按类型组织帖子。但是你不希望你的用户只能选择任何一种类型——那是自找麻烦，既有 T0 也有 T1。因此，您创建了一个自定义类型:

```
type Genre = 'JavaScript' | 'Golang' | 'Programming' | 'Business'
```

现在用户可以选择这四个指定的字符串中的任何一个，在任何需要类型`Genre`的地方，TypeScript 将检查不仅传递了一个字符串，而且传递了四个指定字符串中的一个。

## 为什么我们使用自定义类型

还记得我们之前讨论的为什么我们首先要使用类型吗？它让我们的生活更轻松。我们为自己设置了安全措施，这样我们总是确切地知道*我们应该传递给函数什么，确切地知道函数返回什么，我们可以对变量调用什么方法，变量是否可能是`null`或`undefined`，等等。*

创建我们自己的自定义类型允许我们在更复杂的层次上完成所有这些。我们可以更精确地实施什么值应该被允许(这也作为未来开发人员的一种文档)，并允许我们对我们特别需要的类型实施保护。如果我们的应用程序中的有效用户必须有字段`first_name`、`last_name`、`email`和`phone`，那么我们应该*总是一起检查这些字段的类型，*因为有效用户不是一个，而是*所有有效字段*。它还允许我们指定一个`User`是我们想要传递给一个函数或者从一个函数返回的东西——而不仅仅是任何一个旧的对象。

## 何时使用自定义类型

这在某种程度上取决于开发人员的意见和团队的最佳实践。就个人而言，我喜欢在一些情况下创建自定义类型:

1.  应用程序的一个核心对象(即一个`User`、`BlogPost`、`Genre`)，它将在整个应用程序中传递。只要坚持干的原则，你很快就会感觉到这一点。
2.  现有类型的一个狭窄子集(即上面的`Genre`示例，其中允许有`string`类型的一个小子集)。
3.  React props(我们将在后面更全面地探讨这一点)。

但是一般来说，你可以问自己一个好问题:“创建和维护这种类型会使这个应用程序的开发和维护变得更容易、更可靠吗，还是会成为一种负担？”

## 创建自定义类型

创建自定义类型相当简单，您已经在前面看到了语法。`type`关键字的作用类似于`let`或`const`，并将类型名分配给语句的左侧:

```
// Note: the ? after the address field means the field is optional // An object that has all valid fields besides this is still a Usertype User = {
  first_name: string;
  last_name: string;
  email: string;
  phone: string;
  address?: string;
  age: number;
  verified: boolean;
}type Title = string;
```

## 类型检查自定义类型

对于自定义类型，需要注意的一件重要事情是，您不能使用可用于基本类型的标准类型检查。

要检查一个变量实际上是否是一个字符串，您需要做的就是编写:

```
let name = 'Zuma';typeof name === 'string'; // this will evaluate to truetypeof name === 'number'; // this will evaluate to false
```

`typeof`语法适用于基本类型。可以用类似的方式验证数组:

```
let pets = ['Zuma', 'Roxy'];console.log(pets instanceof Array); // this will evaluate to true
```

对于自定义类型，我们也必须创建自己的类型检查方法。我们使用**类型谓词**来做到这一点。

首先，我们来回顾一下上面的`type User`例子。你可能会问自己，为什么我不能只检查`typeof u.first_name === ‘string’ && u.last_name === ‘string’ && u.email === ‘string’`等等？答案是，可以，但是当你完成所有这些类型检查后， ***TypeScript 仍然不知道变量*** `**u**` ***是 User 类型。*** 你不会再有更多的信息来继续前进了。当你试图将变量`u`传入一个函数时，TypeScript 仍然会抛出一个错误，因为它不知道它是什么类型！

为了解决这个问题，我们使用了一个称为类型谓词的特定函数。类型谓词的返回类型是`var is type`。在我们的例子中，应该是`u is User`。我们的函数看起来像这样—

```
// We pass type unknown so that we can check a variable of any typefunction isUser(u: unknown): u is User { // Assert that the unknown value is of type User, so that we can 
  // check the expected fields

  const user = u as User; if (!user.first_name || typeof user.first_name !== 'string') {
    return false;
  } if (!user.last_name || typeof user.last_name !== 'string') {
    return false;
  } if (!user.email || typeof user.email !== 'string') {
    return false;
  } if (!user.phone || typeof user.phone !== 'string') {
    return false;
  } // We use && instead of || here because it is an optional field
  if (user.address && typeof user.address !== 'string') {
    return false;
  } if (!user.age || typeof user.age !== 'number') {
    return false;
  }

  return true;
}
```

使用这个类型谓词，我们现在可以将变量类型缩小到我们特定的定制用户类型。

接下来，我们将讨论使用第三方库时的接口和利用类型。