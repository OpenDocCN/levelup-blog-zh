# TypeScript 高级类型—可空类型和类型别名

> 原文：<https://levelup.gitconnected.com/typescript-advanced-types-nullable-types-and-type-aliases-847f03b08fae>

![](img/8a1d1f1ff41ec4d0fd8767de0b9fce01.png)

照片由 [Gary Bendig](https://unsplash.com/@kris_ricepees?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

TypeScript 具有许多高级类型功能，这使得编写动态类型代码变得容易。它还有助于采用现有的 JavaScript 代码，因为它允许我们在使用 TypeScript 的类型检查功能的同时保留 JavaScript 的动态功能。TypeScript 中有多种高级类型，如交集类型、联合类型、类型保护、可空类型和类型别名等等。在本文中，我们研究可空类型和类型别名。

# 可空类型

为了让我们将`undefined`赋给带有`--strictNullChecks`标志的属性，TypeScript 支持可空类型。当标志打开时，我们不能将`undefined`赋给没有附加可空操作符的类型成员。要使用它，我们只需在我们想要使用的类型的成员名称后加上一个问号。

如果我们打开了`strictNullChecks`标志，并将一个属性值设置为`null`或`undefined`，那么就像我们在下面的代码中做的那样:

```
interface Person {
  name: string;
  age: number;
}let p: Person = {
  name: 'Jane',
  age: null
}
```

然后我们得到以下错误:

```
Type 'null' is not assignable to type 'number'.(2322)input.ts(3, 3): The expected type comes from property 'age' which is declared here on type 'Person'
```

如果我们关闭了`strictNullChecks`，上面的错误就不会出现，TypeScript 编译器将允许代码被编译。

如果我们打开了`strictNullChecks`标志，并且我们希望能够将`undefined`设置为一个属性的值，那么我们可以将该属性设置为 able。例如，我们可以使用以下代码将接口的成员设置为可空:

```
interface Person {
  name: string;
  age?: number;
}let p: Person = {
  name: 'Jane',
  age: undefined
}
```

在上面的代码中，我们在`Person`接口中的`age`成员后添加了一个问号，使其可为空。那么当我们定义对象的时候，就可以把`age`设置为`undefined`。我们仍然不能将`age`设置为`null`。如果我们尝试这样做，我们会得到:

```
Type 'null' is not assignable to type 'number | undefined'.(2322)input.ts(3, 3): The expected type comes from property 'age' which is declared here on type 'Person'
```

正如我们所见，可空类型只是我们声明的类型和`undefined`类型之间的联合类型。这也意味着我们可以像其他联合类型一样使用类型保护。例如，如果我们想只获取已定义的年龄，我们可以编写以下代码:

```
const getAge = (age?: number) => {
  if (age === undefined) {
    return 0
  }
  else {
    return age.toString();
  }
}
```

在`getAge`函数中，我们首先检查`age`参数是否为`undefined`。如果是，那么我们返回 0。否则，我们可以在它上面调用`toString()`方法，该方法可用于给对象编号。

同样，我们可以用类似的代码消除`null`值，例如，我们可以写:

```
const getAge = (age?: number | null) => {
  if (age === null) {
    return 0
  }  
  else if (age === undefined) {
    return 0
  }
  else {
    return age.toString();
  }
}
```

这很方便，因为可空类型排除了将`null`赋值为开`strictNullChecks`的情况，所以如果我们希望`null`能够作为`age`参数的值传入，那么我们需要将`null`添加到联合类型中。我们也可以将前两个`if`模块合并成一个:

```
const getAge = (age?: number | null) => {
  if (age === null || age === undefined) {
    return 0
  }
  else {
    return age.toString();
  }
}
```

![](img/139a16a48327e1b8877b285809114744.png)

照片由 [Linnea Sandbakk](https://unsplash.com/@linneasandbakk?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 键入别名

如果我们想为一个现有的类型创建一个新的名称，我们可以给这个类型添加一个类型别名。这可以用于许多类型，包括原语、联合、元组和任何其他我们可以手写的类型。要创建类型别名，我们可以使用`type`关键字来完成。例如，如果我们要向联合类型添加别名，我们可以编写以下代码:

```
interface Person {
  name: string;
  age: number;
}interface Employee {
  employeeCode: number;
}type Laborer = Person & Employee;
let laborer: Laborer = {
  name: 'Joe',
  age: 20,
  employeeCode: 100
}
```

`laborer`的声明与直接使用交集类型来类型化`laborer`对象是一样的，如下所示:

```
let laborer: Person & Employee = {
  name: 'Joe',
  age: 20,
  employeeCode: 100
}
```

我们可以像声明其他类型一样声明基本类型的类型别名。例如，我们可以用不同的基元类型创建一个联合类型，如下面的代码所示:

```
type PossiblyNumber = number | string | null | undefined;
let x: PossiblyNumber = 2;
let y: PossiblyNumber = '2';
let a: PossiblyNumber = null;
let b: PossiblyNumber = undefined;
```

在上面的代码中，`PossiblyNumber`类型可以是数字、字符串、`null`或`undefined`。如果我们尝试像下面的代码那样将一个无效的布尔值赋给它:

```
let c: PossiblyNumber = false;
```

我们得到以下错误:

```
Type 'false' is not assignable to type 'PossiblyNumber'.(2322)
```

就像任何其他无效的赋值一样。

我们还可以在类型别名中包含泛型类型标记。例如，我们可以写:

```
type Foo<T> = { value: T };
```

泛型类型别名也可以在类型的属性中引用。例如，我们可以写:

```
type Tree<T> = {
  value: T;
  left: Tree<T>;
  center: Tree<T>;
  right: Tree<T>;
}
```

然后我们可以像在下面的代码中一样使用`Tree`类型:

```
type Tree<T> = {  
  value: T,
  left: Tree<T>;
  center: Tree<T>;
  right: Tree<T>;
}let tree: Tree<string> = {} as Tree<string>;
tree.value = 'Jane';tree.left = {} as Tree<string>
tree.left.value = 'Joe';
tree.left.left = {} as Tree<string>;
tree.left.left.value = 'Amy';
tree.left.right = {} as Tree<string>
tree.left.right.value = 'James';tree.center = {} as Tree<string>
tree.center.value = 'Joe';tree.right = {} as Tree<string>
tree.right.value = 'Joe';console.log(tree);
```

最后一行的`tree`对应的`console.log`应该会让我们:

```
{
  "value": "Jane",
  "left": {
    "value": "Joe",
    "left": {
      "value": "Amy"
    },
    "right": {
      "value": "James"
    }
  },
  "center": {
    "value": "Joe"
  },
  "right": {
    "value": "Joe"
  }
}
```

在我们的 TypeScript 编译器配置中，当`strictNullChecks`标志为 on 时，我们希望能够将`undefined`赋给一个属性，可空类型是有用的。它只是你拥有的任何类型和`undefined`之间的联合类型。它由属性名后的问号表示。这意味着我们可以像其他联合类型一样使用类型保护。注意，可空类型不允许将`null`值赋给它，因为只有当`strictNullChecks`标志打开时才需要可空类型。类型别名让我们为已经存在的类型创建一个新的名称。我们也可以使用带有类型别名的泛型，但是我们不能将它们作为独立的类型使用。