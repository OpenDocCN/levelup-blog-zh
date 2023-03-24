# 使用类型脚本—泛型类型

> 原文：<https://levelup.gitconnected.com/using-typescript-generic-types-7a2cedd2af83>

![](img/65269f60af10cad142c0f072beb313f2.png)

照片由[伊娃·瓦登伯格摄影](https://unsplash.com/@cantusamator?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

TypeScript 是 JavaScript 的自然扩展，在许多项目中取代了 JavaScript。

然而，并不是每个人都知道它实际上是如何工作的。

在本文中，我们将了解如何在 TypeScript 中使用泛型类型。

# 泛型类型

泛型类型允许我们编写代码，当我们插入不同的类型时，代码可以有不同的行为。

# 创建泛型类

泛型类是具有泛型类型参数的类。泛型类型参数是在使用类创建新对象时指定的类型的占位符。

例如，我们可以写:

```
class Collection<T> {
  private items: T[] = [];
  constructor(items: T[]) {
    this.items.push(...items);
  } add(items: T) {
    this.items.push(items);
  } remove(index: number) {
    this.items.splice(index, 1);
  } getItem(index: number): T {
    return this.items[index];
  }
}
```

`T`是数据类型的占位符。

我们可以通过编写以下代码来实例化这个类:

```
const numbers: Collection<number> = new Collection<number>([1, 2, 3]);
```

我们把`T`换成了`number`类型。然后我们可以将数字添加到我们的`Collection`实例的`items`数组中。

泛型类可以有多个数据类型参数。

# 泛型类型参数

在上面的例子中，`number`是数据类型参数，`Collection`是泛型类。

# 不同类型的参数

我们可以将不同的数据类型参数作为类型参数插入。

例如，除了`number`，我们可以换成`string`:

```
const strings: Collection<string> = new Collection<string>(["foo", "bar"]);
```

# 泛型类型值

我们可以通过使用`extends`关键字来限制泛型类型代码中的值类型。

例如，我们可以写:

```
class Collection<T extends number | string> {
  private items: T[] = [];
  constructor(items: T[]) {
    this.items.push(...items);
  } add(items: T) {
    this.items.push(items);
  } remove(index: number) {
    this.items.splice(index, 1);
  } getItem(index: number): T {
    return this.items[index];
  }
}
```

然后我们可以插入类型参数，它们是`number`、`string`或任何更窄的参数。

例如，我们可以写:

```
const numbers: Collection<number> = new Collection<number>([1, 2, 3]);
```

或者:

```
const strings: Collection<1> = new Collection<1>([1, 1]);
```

它们都有效，因为`number`和`1`都是数字的子集。

`extends`表示我们可以分配所列类型之一的子集。

# 使用形状类型约束泛型类型

我们还可以使用形状类型来限制泛型类型。

例如，我们可以写:

```
class Collection<T extends { name: string }> {
  private items: T[] = [];
  constructor(items: T[]) {
    this.items.push(...items);
  } add(items: T) {
    this.items.push(items);
  } remove(index: number) {
    this.items.splice(index, 1);
  } getItem(index: number): T {
    return this.items[index];
  }
}interface Person {
  name: string;
}const people: Collection<Person> = new Collection<Person>([{ name: "james" }]);
```

我们有:

```
T extends { name: string }
```

将我们的类型限制在我们的`Collection`中，只使用`name`键。

任何形状相同的其他类型都可以。

# 多类型参数

一个类可以有多个类型参数。

我们可以向我们的`Collection`类添加第二个类型参数:

```
class Collection<T, U> {
  private items: (T | U)[] = [];
  constructor(items: T[], moreItems: U[]) {
    this.items.push(...items, ...moreItems);
  } add(items: T) {
    this.items.push(items);
  } remove(index: number) {
    this.items.splice(index, 1);
  } getItem(index: number): T | U {
    return this.items[index];
  }
}
```

我们需要添加参数`U`，让我们将不同类型的对象添加到`this.items`中。

然后我们可以写:

```
const items: Collection<number, string> = new Collection<number, string>(
  [1, 2, 3],
  ["foo", "bar"]
);
```

用可以有数字或字符串的`items`创建一个`Collection`实例。

像常规函数或方法参数一样，附加的类型参数用逗号分隔。

# 将类型参数应用于方法

我们也可以将类型参数应用到方法中。

例如，我们可以写:

```
class Collection<T, U> {
  private items: (T | U)[] = [];
  constructor(items: T[], moreItems: U[]) {
    this.items.push(...items, ...moreItems);
  } add(items: T) {
    this.items.push(items);
  } remove(index: number) {
    this.items.splice(index, 1);
  } getItem(index: number): T | U {
    return this.items[index];
  } searchItemsByType<U>(searchData: U[], target: U): U[] {
    return searchData.filter(s => s === target);
  }
}
```

我们有`getItemsByType`，它有类型为`U[]`的`searchData`参数和类型为`U`的`target`。

然后我们可以通过写来使用它:

```
const results = items.searchItemsByType<string>(["baz", "foo"], "foo");
```

然后我们在字符串集合中搜索带有`getItemsBuType`的`'foo'`字符串。

![](img/f5e08926faf207c7c401688d41efc206.png)

照片由 [Masaaki Komori](https://unsplash.com/@gaspanik?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以向类中添加泛型类型参数，使它们能够处理不同类型的数据。

可以是一个，也可以是多个。