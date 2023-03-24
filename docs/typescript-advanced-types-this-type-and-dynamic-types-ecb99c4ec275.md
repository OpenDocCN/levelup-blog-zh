# TypeScript 高级类型—“this”类型和动态类型

> 原文：<https://levelup.gitconnected.com/typescript-advanced-types-this-type-and-dynamic-types-ecb99c4ec275>

![](img/3db8f0694af50f906d976bdfa8a7576b.png)

照片由 [Jf 博鲁](https://unsplash.com/@jfbrou?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

TypeScript 具有许多高级类型功能，这使得编写动态类型代码变得很容易。它还有助于采用现有的 JavaScript 代码，因为它允许我们在使用 TypeScript 的类型检查功能的同时保留 JavaScript 的动态功能。TypeScript 中有很多种高级类型，如交集类型、联合类型、类型保护、可空类型和类型别名等等。在本文中，我们将研究`this`类型，并使用索引签名和映射类型创建动态类型。

# 这种类型

在 TypeScript 中，我们可以使用`this`作为类型。它表示包含类或接口的子类型。我们可以使用它轻松地创建流畅的接口，因为我们知道类中的每个方法都将返回类的实例。

例如，我们可以使用它来定义一个具有可链接方法的类，如下面的代码所示:

```
class StringAdder {
  value: string = '';
  getValue(): string {
    return this.value;
  } addFoo(): this {
    this.value += 'foo';
    return this;
  } addBar(): this {
    this.value += 'bar';
    return this;
  } addGreeting(name: string): this {
    this.value += `Hi ${name}`;
    return this;
  }
}const stringAdder: StringAdder = new StringAdder();
const str = stringAdder
  .addFoo()
  .addBar()
  .addGreeting('Jane')
  .getValue();
console.log(str);
```

在上面的代码中，`addFoo`、`addBar`和`addGreeting`方法都返回了`StringAdder`类的实例，一旦实例被实例化，我们就可以将实例的更多方法调用链接到它。链接是通过我们在每个方法中拥有的`this`返回类型实现的。

# 索引类型

要让 TypeScript 编译器用动态属性名检查代码，我们可以使用索引类型。我们可以使用`extends keyof`关键字组合来表示该类型具有另一种类型的属性名。例如，我们可以写:

```
function choose<U, K extends keyof U>(o: U, propNames: K[]): U[K][] {
  return propNames.map(n => o[n]);
}
```

然后我们可以使用下面代码中的`choose`函数:

```
function choose<U, K extends keyof U>(o: U, propNames: K[]): U[K][] {
  return propNames.map(n => o[n]);
}const obj = {
  a: 1,
  b: 2,
  c: 3
}
choose(obj, ['a', 'b'])
```

然后我们得到值:

```
[1, 2]
```

如果我们记录`choose`函数的结果。如果我们将一个不存在于`obj`对象中的属性名传递到第二个`choose`函数的数组中，那么我们会从 TypeScript 编译器得到一个错误。因此，如果我们编写如下代码:

```
function choose<U, K extends keyof U>(o: U, propNames: K[]): U[K][] {
  return propNames.map(n => o[n]);
}const obj = {
  a: 1,
  b: 2,
  c: 3
}
const arr = choose(obj, ['d']);
```

然后我们得到错误:

```
Type 'string' is not assignable to type '"a" | "b" | "c"'.(2322)
```

在上面的例子中，`keyof U`与字符串文字类型`“a” | “b” | “c”`相同，因为我们传入了泛型`U`类型标记的类型，其中实际类型是从我们传入第一个参数的对象中推断出来的。`K extends keyof U`部分意味着第二个参数必须有传递给第一个参数的部分或全部键名的数组，我们用泛型`U`类型表示。然后，我们将返回类型定义为一个值数组，我们通过循环传递给第一个参数的对象来获得该数组，因此我们有了`U[K][]`类型。`U[K][]`也叫索引访问操作符。

![](img/139f3003181bbaddf608ec93157a19de.png)

照片由[марьянблан| @ marjanblan](https://unsplash.com/@marjan_blan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 索引类型和索引签名

索引签名是一个参数，它在 TypeScript 接口中必须是字符串或数字类型。我们可以用它来表示动态对象的属性。例如，我们可以像在下面的代码中一样使用它:

```
interface DynamicObject<T> {
  [key: string]: T;
}
let obj: DynamicObject<number> = {
  foo: 1,
  bar: 2
};
let key: keyof DynamicObject<number> = 'foo';
let value: DynamicObject<number>['foo'] = obj[key];
```

在上面的代码中，我们定义了一个`DynamicObject<T>`接口，它的成员采用动态类型。我们有一个名为`key`的索引签名，它是一个字符串。也可以是数字。动态成员的类型由通用类型标记符`T`表示。这意味着我们可以向它传递任何数据类型。

然后我们定义了类型为`DyanmicObject<number>`的`obj`对象。这利用了我们之前创建的`DynamicObject`接口。然后我们定义了类型为`keyof DynamicObject<number>`的`key`变量，这意味着它必须是一个字符串或数字。这意味着`key`变量必须有一个属性名作为值。然后我们定义了`value`变量，它必须有一个`DynamicObject`类型的对象的值。

这意味着除了字符串或数字之外，我们不能给`key`变量赋值。所以如果写下这样的话:

```
let key: keyof DynamicObject<number> = false;
```

然后，我们从 TypeScript 编译器得到以下错误消息:

```
Type 'false' is not assignable to type 'string | number'.(2322)
```

# 映射类型

我们可以通过将现有类型的成员映射到新类型来创建新类型。这称为映射类型。

我们可以像在下面的代码中那样创建映射类型:

```
interface Person {
  name: string;
  age: number;
}type ReadOnly<T> = {
  readonly [P in keyof T]: T[P];
}type PartialType<T> = {
  [P in keyof T]?: T[P];
}type ReadOnlyPerson = ReadOnly<Person>;
type PartialPerson = PartialType<Person>;let readOnlyPerson: ReadOnlyPerson = {
  name: 'Jane',
  age: 20
}readOnlyPerson.name = 'Joe';
readOnlyPerson.age = 20;
```

在上面的代码中，我们创建了`ReadOnly`类型别名，让我们通过将类型的每个成员设置为`readonly`来将现有类型的成员映射到新类型。这本身并不是一个新类型，因为我们需要将一个类型传递给泛型类型标记`T`。然后，我们为我们定义的类型创建一个别名，方法是将`Person`类型分别传入`ReadOnly`别名和`Partial`别名。

接下来，我们用设置的`name`和`age`属性定义了一个`ReadOnlyPerson`对象。然后，当我们再次尝试设置这些值时，会出现以下错误:

```
Cannot assign to 'name' because it is a read-only property.(2540)Cannot assign to 'age' because it is a read-only property.(2540)
```

这意味着来自`ReadOnly`类型别名的`readonly`属性正在被执行。同样，我们可以对`PartialType`类型别名做同样的事情。我们通过将`Person`类型的成员映射到具有`PartialPerson`类型的`PartialPerson`类型来定义`PartialPerson`类型。然后我们可以像下面的代码一样定义一个`PartialPerson`对象:

```
let partialPerson: PartialPerson = {};
```

正如我们所见，我们可以根据需要省略`partialPerson`对象的属性。

我们可以通过从映射的类型别名创建一个交集类型来向它添加新成员。因为我们使用了`type`关键字来定义映射的类型，所以它们实际上是类型。它们实际上是类型别名。这意味着我们不能把成员直接放在里面，即使它们看起来像接口。

要添加成员，我们可以编写如下代码:

```
interface Person {
  name: string;
  age: number;
}type ReadOnly<T> = {
  readonly [P in keyof T]: T[P];
}type ReadOnlyEmployee = ReadOnly<Person> & {
  employeeCode: string;
};let readOnlyPerson: ReadOnlyEmployee = {
  name: 'Jane',
  age: 20,
  employeeCode: '123'
}
```

`Readonly<T>`和`Partial<T>`包含在 TypeScript 标准库中。`Readonly`将我们传递给泛型类型占位符的类型成员映射到只读成员。关键字`Partial`让我们将一个类型的成员映射为可空成员。

# 结论

在 TypeScript 中，我们可以使用`this`作为类型。它表示包含类或接口的子类型。我们可以使用它轻松地创建流畅的接口，因为我们知道类中的每个方法都将返回类的实例。索引签名是一个参数，它在 TypeScript 接口中必须是字符串或数字类型。

我们可以用它来表示动态对象的属性。要转换某个类型的成员以向其添加一些属性，我们可以将现有类型的成员映射到新类型中，以便向具有映射类型的接口添加属性。