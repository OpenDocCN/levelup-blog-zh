# 用于在 Typescript 中转换类型的 5 种实用工具类型

> 原文：<https://levelup.gitconnected.com/5-utility-types-for-transforming-types-in-typescript-942e63a61e8c>

## 每个开发人员都应该知道的实用程序类型

![](img/8077773916d7b2e40823a9d60740fa92.png)

关于 [typescript](https://www.typescriptlang.org/) 的一个伟大的事情是它的灵活性。您可以使用现有类型转换、更改和创建新类型。在本文中，我们将研究每个开发人员都应该知道的 5 种实用程序类型。

## 省略

顾名思义，它用于省略类型的属性。让我们看一个例子。

```
type Person = {
  firstname: string
  lastname: string
  email: string
  age: number
}
```

如果我们想从这个类型中删除`age`并创建一个新类型，我们可以这样做:

```
type PersonWithoutAge = Omit<Person, 'age'>
```

结果类型将是。

```
type PersonWithoutAge = {
  firstname: string
  lastname: string
  email: string
}
```

我们可以使用`|`(管道)省略多个属性，如下所示:

```
type SimplePerson = Omit<Person, 'email' | 'age'>
```

结果类型将是。

```
type SimplePerson = {
  firstname: string
  lastname: string
}
```

## 挑选

它做的和`Omit`做的相反。我们可以使用`Pick`从类型中挑选我们想要的属性。例如，在我们的`SimplePerson`示例类型中，我们可以使用`Pick`，选择`firstname`和`lastname`来创建类型，而不是从`Person`中省略`email`、`age`。这里有一个例子:

```
type SimplePerson = Pick<Person, "firstname" | "lastname">
```

导致与我们使用`Omit`时相同的类型。

```
type SimplePerson = {
  firstname: string;
  lastname: string;
}
```

## 部分的

我发现自己用得最多的就是这种类型。使用`Partial`使一个类型的所有属性可选。有时，我们不需要为一个对象的所有属性提供值。在这种情况下，我们可以使用`Partial`创建一个新的类型，产生一个所有属性都可选的类型。让我们看一个例子。如果我们想让我们的`Person`属性可选。

```
type PartialPerson = Partial<Person>
```

导致以下类型。

```
type PartialPerson = {
  firstname?: string | undefined
  lastname?: string | undefined
  email?: string | undefined
  age?: number | undefined
}
```

## 需要

该类型与`Partial`所做的完全相反。它将一个类型的所有可选属性转换为必需属性。比方说，我们想让`PartialPerson`的属性，变成强制的。我们可以这样做，如下图所示。

```
type RequiredPerson = Required<PartialPerson>
```

导致以下类型。

```
type RequiredPerson = {
  firstname: string
  lastname: string
  email: string
  age: number
}
```

## 只读

顾名思义。如果你不想允许，给对象属性赋值，你可以用这个。`Readonly`使一个类型的所有属性，只读(我知道你猜到了)。我发现这在创建一个 React 状态对象时非常有用，它可以防止给状态属性赋值。下面是使用方法。

```
type ReadonlyPerson = Readonly<Person>
```

结果类型将如下所示。

```
type ReadonlyPerson = {
  readonly firstname: string;
  readonly lastname: string;
  readonly email: string;
  readonly age: number;
}
```

我希望，我已经给了你足够的知识来开始使用实用类型来转换类型。有了这些知识，您应该能够从现有的类型中组合和创建新的类型。你可以[在这里](https://www.typescriptlang.org/docs/handbook/utility-types.html)阅读更多关于公用事业类型[。](https://www.typescriptlang.org/docs/handbook/utility-types.html)

如果你喜欢这篇文章，给它一个掌声。你可以关注我，看更多类似的文章。

感谢阅读。