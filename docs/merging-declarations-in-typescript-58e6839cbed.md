# 在 TypeScript 中合并声明

> 原文：<https://levelup.gitconnected.com/merging-declarations-in-typescript-58e6839cbed>

![](img/584e7147f5cad73f322c5aa9b1867988.png)

米哈伊尔·瓦西里耶夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

使用 TypeScript，我们可以声明许多普通 JavaScript 中不存在的实体，如接口、枚举和类型别名。有时，这些类型必须合并成一个，就像当我们试图从多个类或接口派生一个新类时。这意味着有将它们合并在一起的规则。声明合并的过程是来自不同来源的多个成员的合并，这些成员具有重叠的成员名称或类型。

# 合并接口的基本规则

合并接口是声明合并最简单也是最常见的操作类型。最基本的情况是 2 个接口没有重叠的成员。在这种情况下，它们只是机械地融合在一起。例如，如果我们有 2 个`Person`接口，如下面的代码所示:

```
interface Person {
  name: string;
}interface Person {
  age: number;
}
```

然后当我们声明一个类型为`Person`的变量时，它们将会合并成一个。例如，我们可以这样写:

```
let person: Person = {
  name: 'Jane',
  age: 20
}
```

然后，TypeScript 将接受这些代码，并编译和运行这些代码。

如果有任何非函数成员的重叠，那么它们必须是唯一的。否则，它们必须具有相同的类型。例如，如果我们有:

```
interface Person {
  name: string;
  age: string;
}interface Person {
  age: number;
}
```

然后我们得到以下错误:

```
Subsequent property declarations must have the same type.  Property 'age' must be of type 'string', but here has type 'number'.(2717)input.ts(3, 5): 'age' was also declared here.
```

此外，当两个接口合并时，如果第一个接口与第二个接口有重叠的成员，则第二个接口中的任何内容都将覆盖第一个接口的成员。对于函数成员，它们只是作为同一个函数的重载组合在一起。例如，如果我们有以下接口声明:

```
interface Animal { };
interface Sheep { };
interface Dog { };
interface Cat { };interface Eater {
  eat(animal: Animal): Animal;
}interface Eater {
  eat(animal: Sheep): Sheep;
}interface Eater {
  eat(animal: Dog): Dog;
  eat(animal: Cat): Cat;
}
```

那么我们得到的`Eater`界面，就会是:

```
interface Eater {  
  eat(animal: Dog): Dog;
  eat(animal: Cat): Cat;
  eat(animal: Sheep): Sheep;
  eat(animal: Animal): Animal;
}
```

每个组的元素保持相同的顺序，但是在合并的接口中，在后面的接口中的功能组首先排序。唯一的例外是，当函数成员的签名由单个字符串组成时，它将被移到顶部。例如，如果我们有:

```
interface Animal { };
interface Sheep { };
interface Dog { };
interface Cat { };interface Eater {
  eat(animal: 'Animal'): Animal;
}interface Eater {
  eat(animal: Sheep): Sheep;
}interface Eater {
  eat(animal: Dog): Dog;
  eat(animal: Cat): Cat;
}
```

那么合并后的`Eater`接口将是:

```
interface Eater {  
  eat(animal: 'Animal'): Animal;
  eat(animal: Dog): Dog;
  eat(animal: Cat): Cat;
  eat(animal: Sheep): Sheep;
}
```

# 名称空间

相同名称的命名空间会将它们的成员合并在一起。所有导出的接口和类都合并到一个名称空间中。例如，如果我们有以下名称空间:

```
namespace Animals {
  export class Cat { }
}namespace Animals {
  export interface Mammal {
      name: string;        
  }
  export class Dog { }
}
```

那么合并后的名称空间将是:

```
namespace Animals {
  export interface Mammal {
      name: string;        
  } export class Cat { }
  export class Dog { }
}
```

另一方面，非导出成员只在它们自己的未合并命名空间中可见。例如，如果我们有:

```
namespace Animal {
  export class Cat { }
  export const getAnimal = () => {
    return animal;
  }
}namespace Animal {
  let animal = {};
  export interface Mammal {
    name: string;        
  }
  export class Dog { }
}
```

然后我们得到以下错误消息:

```
Cannot find name 'animal'. Did you mean 'Animal'?(2552)
```

因为`animal`变量没有被导出。

我们不能有两个同名的命名空间成员，因为我们会有重复的声明，这是不允许的。例如，如果我们有:

```
namespace Animal {
    export class Cat { }
    export let animal = {};
    export const getAnimal = () => {
        return animal;
    }
}namespace Animal {
    export let animal = {};
    export interface Mammal {
      name: string;        
    }
    export class Dog { }
}
```

然后我们得到错误:

```
Cannot redeclare block-scoped variable 'animal'.(2451)
```

![](img/fdfdbd02c66ac8b07630947938f12e77.png)

照片由 [NeONBRAND](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 将命名空间与类合并

将名称空间与类合并的规则与合并任何其他名称空间成员的规则相同。它们必须被导出，以便我们进行合并。我们可以编写如下代码，通过从外部类引用命名空间内的类来定义内部类:

```
class Person {
    label: Person.PersonName;    
}namespace Person {
    export class PersonName { }
}
```

我们也可以从与名称空间同名的函数中引用名称空间中的成员。例如，我们可以编写以下代码:

```
function buildName(middleName: string): string {
    return `${buildName.firstName} ${middleName} ${buildName.lastName}`;
}namespace buildName {
    export let firstName = "Jane";
    export let lastName = "Smith";
}buildName('Mary');
```

只要导出成员，我们就可以在`buildName`函数中访问`buildName`名称空间的成员。同样，我们可以从与枚举同名的名称空间中引用枚举成员。例如，我们可以这样写:

```
enum Fruit {
  Orange = 1,
  Apple = 2,
  Banana = 4
}namespace Fruit {
  export const mixFruit = (fruit: string) => {
    if (fruit == "Orange and Apple") {
      return Fruit.Orange + Fruit.Apple;
    }
    else if (fruit == "Apple and Banana") {
      return Fruit.Apple + Fruit.Banana
    }
    return 0;
  }
}
```

正如我们所见，我们可以在`mixFruit`函数内的`Fruit`名称空间中访问`Fruit`枚举的成员。

# 不允许的合并

目前，我们可以将类与其他类或变量合并。

# 模块扩充

我们可以使用`declare`关键字来声明一个模块的成员具有 TypeScript 编译器不知道的属性。例如，我们可以这样写:

```
// fruit.ts
export class Fruit<T> {
  // ...
}// setColor.tsimport { Fruit } from "./fruit";
declare module "./fruit" {
  interface Fruit<T> {
    setColor(f: (x: string) => string): Fruit;
  }
}
Fruit.prototype.setColor = function (f) {
  // ...
}
```

在上面的代码中，我们使用了`declare module`关键字来声明`Fruit`类中 TypeScript 编译器看不到的项目。它让我们操作那些已定义但 TypeScript 编译器不能发现的东西。

我们可以使用上面的方法来修补现有的声明。它不适用于新的顶级声明。此外，默认导出不能增加，因为它没有名称。

# 全球扩增

我们也可以在模块内部的全局范围内添加声明。例如，我们可以编写类似下面的代码:

```
// fruit.ts
export class Fruit {

}declare global {
  interface Array<T> {
    toFruitArray(): Fruit[];
  }
}Array.prototype.toFruitArray = function () {
  return this.map(a => <Fruit>{ ... });
}
```

在上面的代码中，我们让 TypeScript 编译器知道全局`Array`对象，然后向其原型添加一个`toFruitArray`。如果没有`declare`子句，我们会得到一个错误，因为 TypeScript 编译器不知道`toFruitArray`方法存在于`Array`全局对象中。

在 TypeScript 中，许多东西可以合并在一起。合并接口是声明合并中最简单也是最常见的操作类型。最基本的情况是 2 个接口没有重叠的成员。在这种情况下，它们只是机械地融合在一起。此外，当两个接口合并时，如果第一个接口与第二个接口有重叠的成员，则第二个接口中的任何内容都将覆盖第一个接口的成员。对于函数成员，它们只是作为同一个函数的重载组合在一起。相同名称的命名空间会将它们的成员合并在一起。所有导出的接口和类都合并到一个名称空间中。

我们可以使用`declare`关键字来声明一个模块的成员具有 TypeScript 编译器不知道的属性。我们还可以通过全局模块扩充在模块内部的全局范围内添加声明。类和变量目前不能合并在一起。