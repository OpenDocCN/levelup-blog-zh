# TypeScript Nullish 合并运算符:始终有一个回退值

> 原文：<https://levelup.gitconnected.com/typescript-nullish-coalescing-operator-always-have-a-fallback-value-c24e2be6fe5e>

## 没有 null 且未用 Nullish 合并运算符定义的回退值

![](img/93de856434303d9a6d390688d78123c6.png)

[天一马](https://unsplash.com/@tma?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

## 零融合算子

在 TypeScript 3.7 版本中，我们提供了一个新的逻辑操作符。它叫`nullish coalescing`，长得像这个`??`。但是它有什么用呢？

如果操作符左边的值是`null`或`undefined`，那么右边的值将用于回退。所以通过使用这个，你可以防止代码中出现`null`或`undefined`值。听起来不错，让我们看看下面的例子:

```
const var01 = **null**;const example01 = var01 **??** 'My other value';console.log(example01); // **'My other value'**
```

`example01`会导致`My other value`。因为左手值是`null`，所以会返回右手值。同样的情况也适用于`undefined`。参见下面的例子。

```
const var02 = **undefined**;const example02 = var02 **??** 'My other value';console.log(example02); // **'My other value'**
```

对于所有其他不包含值`null`或`undefined`的场景，它返回左手值。

```
const var03 = **'Hello'**;
const var04 = **true**;const example03 = var03 **??** 'My other value';
const example04 = var04 **??** 'My other value';console.log(example03); // **'Hello'**
console.log(example04); // **true**
```

[](https://medium.com/@jeroenouw/ecmascript-vs-typescript-private-fields-640ae37aa162) [## EcmaScript 与 TypeScript —私有字段

### TypeScript 的 private 关键字和 EcmaScript/JavaScript 的#字符有什么区别

medium.com](https://medium.com/@jeroenouw/ecmascript-vs-typescript-private-fields-640ae37aa162) 

## OR 逻辑运算符(比较)

新的 nullish 合并操作符`??`没有 or 操作符`||`严格。`??`允许使用数字零`0`，非数字`NaN`或空字符串`""`。然而，这些值是错误的，不允许使用`||`，这在某些情况下会导致不希望的行为。`undefined`和`null`不允许出现在两个运算符中。

```
const var05 = **0**;
const var06 = **NaN**;
const var07 = **''**;**// Will return right-hand (restricts)**
const example05 = var05 **||** 'My other value';
const example06 = var06 **||** 'My other value';
const example07 = var07 **||** 'My other value';**// Will return left-hand (allows)**
const example08 = var05 **??** 'My other value';
const example09 = var06 **??** 'My other value';
const example10 = var07 **??** 'My other value';
```

> ***注:*** *用* `*||*` *和* `*??*` *合在一起会给出一个* `*SyntaxError*` *。*

## 感谢您的阅读！我的 [Github](https://github.com/jeroenouw/) 和 [Twitter](https://twitter.com/jeroenouw) 。如果你觉得这篇文章有用，可以考虑看看我的其他文章:

[](/10-advanced-typescript-questions-for-an-interview-with-answers-6f0513b1688e) [## 面试中的 10 个高级打字问题及答案

### 检查你的候选人是否有能力回答这些更高级的打字问题

levelup.gitconnected.com](/10-advanced-typescript-questions-for-an-interview-with-answers-6f0513b1688e) [](https://medium.com/@jeroenouw/ecmascript-vs-typescript-private-fields-640ae37aa162) [## EcmaScript 与 TypeScript —私有字段

### TypeScript 的 private 关键字和 EcmaScript/JavaScript 的#字符有什么区别

medium.com](https://medium.com/@jeroenouw/ecmascript-vs-typescript-private-fields-640ae37aa162) [](https://medium.com/@jeroenouw/angular-7-share-component-data-with-other-components-1b91d6f0b93f) [## 在 Angular 10 中与其他元件共享元件数据

### 使用 Angular 的输入、输出、EventEmitter 和 ViewChild 共享组件数据

medium.com](https://medium.com/@jeroenouw/angular-7-share-component-data-with-other-components-1b91d6f0b93f)