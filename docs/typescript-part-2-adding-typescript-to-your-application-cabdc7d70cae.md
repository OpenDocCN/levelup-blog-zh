# TypeScript，第 2 部分:向应用程序添加 TypeScript

> 原文：<https://levelup.gitconnected.com/typescript-part-2-adding-typescript-to-your-application-cabdc7d70cae>

![](img/4bd5553770b33db8fe09313ffd7dc5ea.png)

在 [Unsplash](https://unsplash.com/s/photos/mixing-bowl?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Toa Heftiba](https://unsplash.com/@heftiba?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

> 本文是 TypeScript 系列文章的一部分:
> 
> [打字稿，第一部分:简介](/typescript-part-1-an-introduction-b00ade194c4)
> 
> **TypeScript，第 2 部分:向应用程序添加 TypeScript**
> 
> [TypeScript，第 3 部分:使用自定义类型](https://link.medium.com/XMFlJgDDSmb)

毫不奇怪，TypeScript 的核心是类型。在 JavaScript 中，从最基本的意义上来说，类型实际上只被认为是需要的。它们往往是我们的代码或上下文的结果，而不是结构和保真度的工具。正因为如此，我们要看的第一件事就是，在创建这个订单时，我们到底要用什么类型。但是很快，在我们开始之前…

# 安装 TypeScript

TypeScript 是一个 [npm 包](https://www.npmjs.com/package/typescript)，您可以像安装任何其他依赖项一样将其安装在您的 JavaScript 应用程序中:

```
npm install typescript/* or */yarn add typescript
```

除了安装包本身之外，使用 TypeScript 开发还需要一些其他的包。它们帮助 Eslint、Jest 和 Node 很好地使用 TypeScript:

```
npm install @typescript-eslint/eslint-plugin @typescript-eslint/parser ts-jest ts-loader ts-node --dev/* or */yarn add @typescript-eslint/eslint-plugin @typescript-eslint/parser ts-jest ts-loader ts-node --dev
```

如果您使用 CRA 来构建新的应用程序，您可以使用 CRA 类型脚本模板:

```
npx create-react-app my-typescript-app --template typescript
```

# 你的标准类型

安装完成后，我们现在可以访问 TypeScript 本机识别和支持的许多类型。这些中的大部分你应该很熟悉，我们将简单地谈谈那些可能不熟悉的。这些类型是:

*   [布尔型](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#the-primitives-string-number-and-boolean)
*   [编号](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#the-primitives-string-number-and-boolean)
*   [字符串](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#the-primitives-string-number-and-boolean)
*   [数组](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#arrays)
*   [元组](https://www.typescriptlang.org/docs/handbook/2/objects.html#tuple-types)
*   [枚举](https://www.typescriptlang.org/docs/handbook/enums.html)
*   未知的
*   [任何一个](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#any)
*   空的
*   [功能](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#functions)
*   承诺
*   [空且未定义](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#null-and-undefined)
*   [对象](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#object-types)

我会假设 boolean，number，string，array，null/undefined，function 和 promise 都是可识别的。其他的我们将在下面深入讨论(我认为对象也是可以识别的，但是它没有你想象的那么简单，所以我把它留在这里简单介绍一下)。

## 元组

元组是一种指定长度的数组，其中每个位置都是指定的类型。例如，一个数组可能只知道它是一个字符串数组，长度可变，而一个元组总是知道它是一个位置为 0 的字符串数组，位置为 1 的数字数组，或者它的长度为 3，每一项都是一个字符串。

```
['hello, world', 5]['John', 'Joe', 'Jake']
```

## 列举型别

枚举是一种表示一组命名常数的类型。在您希望允许一个值不是任何字符串，而是一组预定义的字符串中的一个时，这些非常有用。想想某样东西的具体高度，或者指南针的方向。在创建枚举时，您减少了人为错误，创建了一致性，并增加了值*实际上应该是什么*的清晰度。我们更接近事物的真实世界。名称输入实际上应该只是一个字符串。但是作为一种价值观，指南针的方向与一个人的名字截然不同。

默认情况下，枚举将为常量分配一个数值，从 1 开始。或者，每个常数都可以用字符串值初始化。

```
enum Level {
   Low = 1,
   Medium,
   High,
}enum Compass {
   North = 'North',
   South = 'South',
   East = 'East',
   West = 'West',
}
```

## 任何的

任何变量都是显而易见的。它可以是任何东西。你可以用一个字符串值初始化它，然后把它重新赋值给一个数字，然后再把它变成一个函数。选项是无限的，这就是为什么**我们不喜欢任何类型**。any 类型很少是正确的选择。在测试或故事书中，我有时会使用这种类型，但这只是因为我在代码中保留了非常严格的类型规则，有时在这些环境中这种折衷是值得的。但是 any 类型真的不应该用在您的应用程序代码中。这违背了 TypeScript 的整个目的，即通过了解每个值的类型来将混乱变得有序。

## 未知的

一旦我们开始实现类型，未知类型可能会更有意义。但 TypeScript 的核心是，知道值的类型是什么以及将会是什么，会让您的生活更轻松。它减少了代码中的错误。然而，与真正的静态类型语言不同，JavaScript 不是为了支持这一点而创建的。所以会有不知道类型是什么的时候，那我们怎么办？我们已经说过 any 类型是危险的，应该尽量少用。在这些情况下，我们希望找到未知类型，因为它不是像 any 类型那样说“嘿，这个变量可以是任何东西”，而是说“嘿，我还不知道这个变量是什么，但在我对它做任何事情之前，我想弄清楚”。

## 空的

void 类型实际上只是用来表示函数不应该返回值。我们稍后将对此进行更深入的探讨。

## 目标

对象类型与您想象的略有不同。正如 TypeScript 文档所说的那样—

> 除了原语，你会遇到的最常见的类型是一个*对象类型*。这是指任何带有属性的 JavaScript 值，几乎是全部！要定义一个对象类型，我们只需列出它的属性和类型。

当你确切地知道你正在处理的对象时，对象类型是有帮助的，例如，确切的键和它们每个值的确切类型。

```
/* declaring variable dog with type of specified object */let dog: {
   name: string;
   age: number;
   color: string;
};/* variable dog can now be assigned the keys and values of types specified in the declaration */dog = {
   name: 'Zuma',
   age: 8,
   color: 'black'
};
```

在 JavaScript 中，更常见、更灵活的对象类型是[记录类型](https://www.typescriptlang.org/docs/handbook/utility-types.html#recordkeys-type)。记录类型表示一个对象，其中键是指定的类型，值是指定的类型，但没有指定文字键。

```
let dog: Record<string, string | number>;dog['name'] = 'Zuma';
dog['age'] = 8;
dog['color'] = 'black';
dog['favorite toy'] = 'Gordie';
dog['walks per day'] = 2;
```

这些标准类型是您在 TypeScript 中工作的基础，但它们只是我们可以对类型做些什么的开始。在下一篇文章中，我们将讨论自定义类型、接口以及利用依赖关系中的类型。