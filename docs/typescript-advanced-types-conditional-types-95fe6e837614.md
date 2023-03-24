# TypeScript 高级类型—条件类型

> 原文：<https://levelup.gitconnected.com/typescript-advanced-types-conditional-types-95fe6e837614>

![](img/373d916c1f0345b61c75edd19742d1b5.png)

照片由 [Wynand van Poortvliet](https://unsplash.com/@wwwynand?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

TypeScript 具有许多高级类型功能，这使得编写动态类型代码变得容易。它还有助于采用现有的 JavaScript 代码，因为它允许我们在使用 TypeScript 的类型检查功能的同时保留 JavaScript 的动态功能。TypeScript 中有多种高级类型，如交集类型、联合类型、类型保护、可空类型和类型别名等等。

在本文中，我们将研究条件类型。

# 条件类型

从 TypeScript 2.8 开始，我们可以用条件测试来定义类型。这使我们可以向数据添加类型，根据我们设置的条件，数据可以有不同的类型。在 TypeScript 中定义条件类型的一般表达式如下:

```
T extends U ? X : Y
```

`T extends U`描述了通用类型`T`和`U`之间的关系。如果`T extends U`是`true`，那么`X`类型是预期的。否则，预计为`Y`型。例如，我们可以在下面的代码中使用它:

```
interface Animal {  
  kind: string;
}interface Cat extends Animal {
  name: string;
}interface Dog {
  name: string;
}type CatAnimal = Cat extends Animal ? Cat : Dog;
let catAnimal: CatAnimal = <Cat>{
  name: 'Joe',
  kind: 'cat'
}
```

在上面的代码中，我们创建了`CatAnimal`类型别名，如果`Cat extends Animal`被设置为`Cat`类型。否则设置为`Dog`。由于`Cat`确实扩展了`Animal`，因此`CatAnimal`类型别名被设置为`Cat`类型。

这意味着在上面的例子中，如果我们像下面的代码那样将`<Cat>`改为`<Dog>`:

```
interface Animal {  
  kind: string;
}interface Cat extends Animal {
  name: string;
}interface Dog {
  name: string;
}type CatAnimal = Cat extends Animal ? Cat : Dog;
let catAnimal: CatAnimal = <Dog>{
  name: 'Joe',
  kind: 'cat'
}
```

我们将得到以下错误消息:

```
Property 'kind' is missing in type 'Dog' but required in type 'Cat'.(2741)
```

这确保我们根据类型中表达的条件为`catAnimal`选择正确的类型。如果我们想让`Dog`成为`catAnimal`的类型，那么我们可以改为编写以下代码:

```
interface Animal {  
  kind: string;
}interface Cat  {
  name: string;
}interface Dog extends Animal {
  name: string;
}type CatAnimal = Cat extends Animal ? Cat : Dog;
let catAnimal: CatAnimal = <Dog>{
  name: 'Joe'
}
```

我们还可以使用嵌套条件来从多个条件中确定实际类型。例如，我们可以写:

```
interface Animal {  
  kind: string;
}interface Bird  {
  name: string;
}interface Cat  {
  name: string;
}interface Dog extends Animal {
  name: string;
}type AnimalTypeName<T> =
  T extends Animal ? Cat :    
  T extends Animal ? Dog :    
  T extends Animal ? Bird :
  Animaltype t0 = AnimalTypeName<Cat>;  
type t1 = AnimalTypeName<Dog>;
type t2 = AnimalTypeName<Animal>;
type t3 = AnimalTypeName<Bird>;
```

然后我们得到类型别名`t0`、`t1`、`t2`和`t3`的如下类型:

```
type t0 = Animal
type t1 = Cat
type t2 = Cat
type t3: Animal
```

确切的不一定要马上选择，我们也可以有这样的东西:

```
interface Foo {}interface Bar extends Foo {

}function bar(x) {
  return x;
}function foo<T>(x: T) {
  let y: T extends Foo ? string : number = bar(x);
  let z: string | number = y;
}foo<Bar>(1);
foo<Bar>('1');
foo<Bar>(false);
```

正如我们所看到的，我们可以向`foo`传递任何东西，即使我们已经设置了条件类型。这是因为类型条件中的实际类型还没有被选择。，所以 TypeScript 没有对我们可以给`foo`函数中的变量赋什么做任何假设。

![](img/5b873aea6b5efa8f367d78ebca69acd7.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 分配条件类型

条件类型是分布式的。如果我们有多个条件类型可以扩展一个类型，如下面的代码所示:

```
interface A {}
interface B {}
interface C {}
interface D {}
interface X {}
interface Y {}type TypeName = (A | B | C) extends D ? X : Y;
```

那么最后一行相当于:

```
(A extends D ? X : Y) | (B extends D ? X : Y) | (C extends D ? X : Y)
```

例如，我们可以用它来过滤出各种条件的类型。例如，我们可以写:

```
type Diff<T, U> = T extends U ? never : T;
```

从`T`中移除可分配给`U`的类型。如果是`T extends U`，那么`Diff<T, U>`的类型就是`never`，这意味着我们可以给它赋值任何东西，否则它就取类型`T`。同样，我们可以写:

```
type Filter<T, U> = T extends U ? T : never;
```

从`T`中移除不可分配给`U`的类型。在这种情况下，如果`T extends U`，那么过滤器类型与`T`类型相同，否则，它采用`never`类型。例如，如果我们有:

```
type Diff<T, U> = T extends U ? never : T;
type TypeName = Diff<string| number | boolean, boolean>;
```

那么`TypeName`具有类型`string | number`。这是因为`Diff<string| number | boolean, boolean>`与:

```
(string extends boolean ? never : string) | (number extends boolean ? never: number) | (boolean extends boolean ? never: boolean)
```

另一方面，如果我们写:

```
type Filter<T, U> = T extends U ? T : never;
type TypeName = Filter<string| number | boolean, boolean>;
```

则`TypeName`具有`boolean`类型。这是因为`Diff<string| number | boolean, boolean>`与:

```
(string extends boolean ? string: never) | (number extends boolean ? number: never) | (boolean extends boolean ? boolean: never)
```

# 预定义的条件类型

TypeScript 2.8 具有以下预定义的条件类型，它们是:

*   `Exclude<T, U>`–从`T`中排除可分配给`U`的类型。
*   `Extract<T, U>`–从`T`中提取可分配给`U`的类型。
*   `NonNullable<T>`–从`T`中排除`null`和`undefined`。
*   `ReturnType<T>`–获取函数类型的返回类型。
*   `InstanceType<T>`–获取构造函数类型的实例类型。

从 TypeScript 2.8 开始，我们可以用条件测试来定义类型。在 TypeScript 中定义条件类型的一般表达式是`T extends U ? X : Y`。它们是分布式的，所以`(A | B | C) extends D ? X : Y;`和`(A extends D ? X : Y) | (B extends D ? X : Y) | (C extends D ? X : Y)`是一样的。