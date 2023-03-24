# 智能类型脚本批量属性分配

> 原文：<https://levelup.gitconnected.com/smart-typescript-bulk-property-assignment-52162c1031da>

![](img/18b1f59b84b2288ff2db76480aad761f.png)

马特·施瓦茨在 [Unsplash](https://unsplash.com/s/photos/bulk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 目标

我希望我在 TypeScript 代码中使用的对象允许我一次对它们设置多个属性。我的对象必须有一个方法`setProps`,这样当给定一个正确类型的属性映射时，该方法在对象上设置相应的属性。

# 方法

我有一个基类，比如说`BaseObject`，它有公共的`setProps`方法。`setProps`必须采用与从 BaseClass 派生的类的实例兼容的参数。这是通过泛型实现的。该方法将类似于下面这段代码:

我使用语法`<T extends BaseObject>`约束类型参数`T`来表示从`BaseObject`继承的类型。

现在，我想将传递的参数的属性赋给调用了`setProps`的对象。为了实现这一点，我按照下面的摘录进行操作:

`for in` 循环获取`values` 中的所有键，对于每个这样的属性，如果它的值不为空，就将其分配给调用对象上的相同属性。

现在我想确定在`for in`循环中没有考虑方法。出于这个原因，我将使用[映射类型](https://www.typescriptlang.org/docs/handbook/advanced-types.html#mapped-types)和 npm 包[实用类型](https://github.com/piotrwitek/utility-types)，顾名思义，它提供了 TypeScript 实用类型。我将使用由`utility-types`提供的`NonFunctionKeys`来定义以下内容:

上面，我们使用类型级编程和表达式`{[P in NonFunctionKeys<T>]:T[P]}`来简单说明应用于类型参数 T 的`OnlyProperties`是一个仅由 T 的非方法键组成的类型。如果您还没有这样做，我强烈建议您熟悉 TypeScript [映射类型](https://www.typescriptlang.org/docs/handbook/advanced-types.html#mapped-types)。它们真的很有用。

我们新创建的类型将同样用于`setProps`:

通过说我想要属性的批量设置，我也暗示了所述属性的**部分**设置。为了实现这一点，我将利用 Typescript 提供的[部分](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)实用程序类型的帮助。对于传递给它的任何类型，它使该对象的所有属性都是可选的。使用`Partial,`我将能够编写以下类型:

因此，我们现在把`setProps`定义为:

下面是`setProps`的用法示例:

请注意，如果`setProps`的参数不是 O 类型，它将无法通过编译器检查，因为我提供了 O 类型参数。

# 奖金

当我们几乎可以免费获得一个**批量属性赋值构造函数**时，为什么要止步于此呢？在从`BaseObject`派生的任何类上，可以使用`setProps`获得批量属性赋值构造函数，如下所示:

# 结论

使用泛型和映射类型，我们可以在 TypeScript 对象上实现**类型安全的批量赋值**。当一个对象的属性数量变得很大，并且编写所有的赋值变得很繁琐时，这一点特别有用。

*本条代码见 https://github.com/kanian/setProps*[](https://github.com/kanian/setProps)**。**

*你可以通过 gmail.com 联系到我*