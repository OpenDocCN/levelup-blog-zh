# EcmaScript 与 TypeScript —私有字段

> 原文：<https://levelup.gitconnected.com/ecmascript-vs-typescript-private-fields-640ae37aa162>

## TypeScript 的 private 关键字和 EcmaScript/JavaScript 的#字符有什么区别

![](img/cc780afae2353fe89b7ef06bc678808e.png)

[Yu Kato](https://unsplash.com/@yukato?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在 EcmaScript/JavaScript 类中，不能将属性或方法定义为 public 或 private，但幸运的是有一个[提案](https://github.com/tc39/proposal-class-fields/)引入了这一特性(阶段 3，这意味着未来 es 版本的候选)。

在 TypeScript 中，我们已经有了`public`和`private`关键字。但是现在在最近的 3.8 版本中，TypeScript 已经引入了 EcmaScript 私有字段。让我们找出不同之处。

## 角度 9

Angular 新版本刚刚发布，但是只支持 TypeScript 3.6 和 3.7。因此，通过使用 3.8，它将是实验性的。

# TypeScript private vs EcmaScript #

根据 [TypeScript 3.8 公告](https://devblogs.microsoft.com/typescript/announcing-typescript-3-8/#ecmascript-private-fields)我们可以说这些是主要的区别:

## 1-语法

```
**// TypeScript** class Person {
  private name;
}**// EcmaScript proposal / TypeScript 3.8** class Person2 {
  #name;
}
```

很多人可能会认为行为是一样的，唯一的区别是语法。但显然不是，否则，我没有写这篇文章。纯粹看语法，我觉得`private`看起来比用`#`干净很多。在其他所有以类为特征的语言中，他们使用`private`关键字。所以，如果你习惯了传统的关键字，看到代码中出现标签可能会有点奇怪。

## 2 —可访问性

```
**// TypeScript** class Person {
  private name = 'Jeroen';
}new Person().name     // compile error
new Person()['name']  // works, but not 'really' private**// EcmaScript proposal / TypeScript 3.8** class Person2 {
  #name = 'Jeroen';
}new Person2().#name     // syntax error
new Person2()['#name']  // undefined
```

所以通过对比上面的结果，你可以看出新的`#`关键词在任何时候都是隐私的(硬隐私)。所以这意味着你不能访问其类之外的属性，只能在内部使用。`private`关键字不是，它只是使它不可用，除非你使用`[]`括号(软隐私)。

## 3 —性能

`private`属性和方法只是一个和`public`一样快的字段。`#`字符可能会有较慢的性能，因为它们被编译为`[WeakMap](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)`的下级。

## 4 —打电话

```
**// TypeScript** class Person {
  private name = 'Jeroen'; constructor() {
    this.name; 
  }
}**// EcmaScript proposal / TypeScript 3.8** class Person2 {
  #name = 'Jeroen'; constructor() {
    this.#name; 
  }
}
```

您可能已经在可访问性(2)的例子中看到过它。但是要使用带有`#`字符的属性，需要用 hashtag: `#name`调用它。专业就是你能立刻看出某样东西是不是私人的。用`private`你就这样称呼物业:`name`。有些人喜欢在它前面加一个下划线`_name`，这样更直观。

## 谁赢了？

我个人认为:

*   **1 —语法**关键字
*   **2 —无障碍** `#`字符
*   **3 —绩效** `private`关键字
*   **4 —调用** `private`关键字

人物的易接近性仅仅是因为硬隐私而变得更有意义，所有其他的只是个人喜好。

## 感谢您的阅读！我的 [Github](https://github.com/jeroenouw/) 或 [Twitter](https://twitter.com/jeroenouw) 。如果你觉得这篇文章有用，请点击👏按钮，并考虑阅读我的其他一些文章:

[](https://medium.com/@jeroenouw/faster-testing-with-baretest-typescript-edition-5db12bd0beac) [## 使用 barestest-TypeScript Edition 进行更快速的测试

### 现在在 TypeScript 项目中使用这个极简的 JavaScript 测试运行器

medium.com](https://medium.com/@jeroenouw/faster-testing-with-baretest-typescript-edition-5db12bd0beac) [](https://medium.com/better-programming/javascript-prevent-objects-from-being-changed-d1ca82f02975) [## 防止对象在 JavaScript 中被更改

### 冻结、密封、防拉伸、isFrozen、isSealed、isExtendable 和 const 的不变性

medium.com](https://medium.com/better-programming/javascript-prevent-objects-from-being-changed-d1ca82f02975) [](/upgrade-to-angular-9-within-10-minutes-671c6fd6174b) [## 10 分钟内升级到 Angular 9

### 现在轻松地将您现有的项目更新到 Angular 的新版本

levelup.gitconnected.com](/upgrade-to-angular-9-within-10-minutes-671c6fd6174b)