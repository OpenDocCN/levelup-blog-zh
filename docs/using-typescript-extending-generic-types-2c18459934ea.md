# 使用 TypeScript —扩展泛型类型

> 原文：<https://levelup.gitconnected.com/using-typescript-extending-generic-types-2c18459934ea>

![](img/2ba75e92beff8cbeb699185b7ccf58de.png)

鲍里斯·斯莫克罗维奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

TypeScript 是 JavaScript 的自然扩展，在许多项目中取代了 JavaScript。

然而，并不是每个人都知道它实际上是如何工作的。

在本文中，我们将研究如何在 TypeScript 中扩展泛型类型。

# 扩展泛型类

我们可以给现有的类添加额外的特性。

例如，我们可以写:

```
class Collection<T extends { name: string }> {
  protected items: T[] = [];
  constructor(items: T[]) {
    this.items.push(...items);
  }
  add(items: T) {
    this.items.push(items);
  }
  remove(index: number) {
    this.items.splice(index, 1);
  }
  getItem(index: number): T {
    return this.items[index];
  }
}class SearchableCollection<T extends { name: string }> extends Collection<T> {
  find(name: string): T | undefined {
    return this.items.find(item => item.name === name);
  }
}
```

在上面的例子中，我们有`T`必须匹配的`{ name: string }`类型。

至少我们需要`name`字符串属性。

例如，我们可以写:

```
interface Person {
  name: string;
  age: number;
}const persons: SearchableCollection<Person> = new SearchableCollection<Person>([
  {
    name: "james",
    age: 1
  }
]);
```

`Person`有`name`字符串属性和`age`数字属性，但是我们仍然可以使用它。

只要对象有`name`属性，并且它的值是字符串，我们就可以使用它。

# 限制泛型类型参数

我们可以指定一个比 type 参数中指定的更严格的类型。

例如，我们可以写:

```
class Person {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}class Owner extends Person {
  name: string;
  age: number;
  constructor(name: string, age: number) {
    super(name);
    this.age = age;
  }
}class Collection<T extends Person | Owner> {
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

然后，我们可以通过编写以下代码将其用于更具限制性的类型:

```
const items: Collection<Owner> = new Collection<Owner>([new Owner("james", 1)]);
```

`Owner`比`Person`更严格，因为它是`Person`的子类。

现在，如果我们试图将一个`Person`实例添加到我们传递给构造函数的数组中，我们会得到一个错误。

例如，如果我们有:

```
const items: Collection<Owner> = new Collection<Owner>([
  new Owner("james", 1),
  new Person("jane")
]);
```

我们得到错误“Person”类型中缺少“age”属性，但“Owner”类型中需要该属性。

# 泛型类的静态方法

我们可以在泛型类中定义一个静态方法。

例如，我们可以写:

```
class Collection<T extends Person | Owner> {
  private items: T[] = [];
  constructor(items: T[]) {
    this.items.push(...items);
  } add(items: T) {
    this.items.push(items);
  } remove(index: number) {
    this.items.splice(index, 1);
  } static getItem(items: any[], index: number) {
    return items[index];
  }
}
```

我们使用了关键字`static`来表示`getItem`是一个静态方法。我们不能用静态方法引用任何泛型类型参数。静态方法是一个类的所有实例之间的共享。

我们可以`getItem`通过写:

```
const item: number = Collection.getItem([1, 2, 3], 1);
```

如果我们想给静态方法添加泛型类型参数，那么我们必须添加类没有使用的类型参数。

例如，我们可以写:

```
class Collection<T extends Person | Owner> {
  private items: T[] = [];
  constructor(items: T[]) {
    this.items.push(...items);
  } add(items: T) {
    this.items.push(items);
  } remove(index: number) {
    this.items.splice(index, 1);
  } static getItem<A>(items: A[], index: number) {
    return items[index];
  }
}
```

我们添加了`A`数据类型占位符，它没有在其他地方使用。

然后，我们可以通过编写以下代码来调用该方法:

```
const item: number = Collection.getItem<number>([1, 2, 3], 1);
```

![](img/7e6ef282591cd496fa0213fb244db170.png)

照片由[梅里达勒](https://unsplash.com/@meric?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 通用接口

我们可以定义通用接口。

例如，我们可以写:

```
type Person = {
  name: string;
};interface Collection<T extends Person> {
  add(...members: T[]): void;
}
```

然后我们可以创建一个类或一个对象，它具有接受一个数组的`add`方法，该数组包含具有`Person`成员的`T`类型的对象。

一旦有了这些，我们就可以如下使用接口:

```
class DataCollection implements Collection<Person> {
  members: Person[] = [];
  add(...members: Person[]) {
    this.members.push(...members);
  }
}
```

我们将`T`设置为`Person`，然后使用`Collection`接口来执行我们的实现。

# 结论

我们可以向类方法、静态方法和接口添加泛型类型参数。

可以扩展泛型类来创建它们的子类，这些子类也是泛型的。

同样，接口也可以扩展。