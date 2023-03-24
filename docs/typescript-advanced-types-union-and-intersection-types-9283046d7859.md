# TypeScript 高级类型:联合和交集

> 原文：<https://levelup.gitconnected.com/typescript-advanced-types-union-and-intersection-types-9283046d7859>

![](img/bd1188bfcbc309789dc8e7f03a916bf1.png)

照片由[URI·梅龙](https://unsplash.com/@urimeron?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

TypeScript 具有许多高级类型功能，这使得编写动态类型代码变得容易。它还有助于采用现有的 JavaScript 代码，因为它允许我们在使用 TypeScript 的类型检查功能的同时保留 JavaScript 的动态功能。TypeScript 中有多种高级类型，如交集类型、联合类型、类型保护、可空类型和类型别名等等。在本文中，我们将研究交集和并集类型。

# 交叉点类型

交集类型允许我们将多种类型组合成一种类型。具有交集类型的对象的结构必须同时具有构成交集类型的所有类型的结构。它由一个`&`符号表示。交集类型的对象中需要所有类型的所有成员。

例如，我们可以在下面的代码中使用交集类型:

```
interface Animal {
  kind: string;
}interface Person {
  firstName: string;
  lastName: string;
  age: number;
}interface Employee {
  employeeCode: string;
}let employee: Animal & Person & Employee = {
  kind: 'human',
  firstName: 'Jane',
  lastName: 'Smith',
  age: 20,
  employeeCode: '123'
}
```

从上面的代码中我们可以看到，每种类型都用一个`&`符号分隔开。同样，`employee`对象具有`Animal`、`Person`和`Employee`的所有属性。每个属性都有一个在每个接口中定义的类型。如果结构不完全匹配，那么我们将从 TypeScript 编译器得到如下的错误消息:

```
Type '{ kind: string; firstName: string; lastName: string; age: number; }' is not assignable to type 'Animal & Person & Employee'.Property 'employeeCode' is missing in type '{ kind: string; firstName: string; lastName: string; age: number; }' but required in type 'Employee'.(2322)input.ts(12, 3): 'employeeCode' is declared here.
```

如果我们有下面的代码，就会出现上面的错误:

```
let employee: Animal & Person & Employee = {
  kind: 'human',
  firstName: 'Jane',
  lastName: 'Smith',
  age: 20  
}
```

TypeScript 查找`employeeCode`属性，因为`employeeCode`属性在`Employee`接口中。

如果两个类型有相同的成员名称但不同的类型，那么当它们作为交集类型结合在一起时，它会自动被分配`never`类型。这意味着我们不能给它赋值。例如，如果我们有:

```
interface Animal {
  kind: string;
}interface Person {
  firstName: string;
  lastName: string;
  age: number;
}interface Employee {
  employeeCode: string;
  age: string;
}let employee: Animal & Person & Employee = {
  kind: 'human',
  firstName: 'Jane',
  lastName: 'Smith',
  age: 20
}
```

然后，我们从 TypeScript 编译器得到以下错误:

```
Type 'number' is not assignable to type 'never'.(2322)input.ts(8, 3): The expected type comes from property 'age' which is declared here on type 'Animal & Person & Employee'
```

如果我们忽略这个属性，那么编译器也会抛出一个关于`age`属性丢失的错误。因此，如果我们想从类型中创建交集类型，就不应该有成员名称相同的类型。

![](img/307b2837193f7c884df486bc6ce7646a.png)

[freestocks.org](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 工会类型

联合类型创建了一个新类型，它允许我们创建具有创建联合类型的每种类型的部分或全部属性的对象。联合类型通过用管道符号`|`连接多个来创建。

例如，我们可以定义一个具有联合类型的对象，如下面的代码所示:

```
interface Animal {
  kind: string;
}interface Person {
  firstName: string;
  lastName: string;
  age: number;
}interface Employee {
  employeeCode: string;
}let employee: Animal | Person | Employee = {
  kind: 'human',
  firstName: 'Jane',
  lastName: 'Smith',
  age: 20  
}
```

上面的代码有一个`Animal | Person | Employee`类型的`employee`对象，这意味着它可以拥有`Animal`、`Person`或`Employee`接口的一些属性。不是所有的都必须包含，但是如果包含了，那么类型必须与接口中的类型相匹配。

对于联合类型，我们可以有两个成员名称相同但类型不同的类型。例如，如果我们有以下代码:

```
interface Animal {
  kind: string;
}interface Person {
  firstName: string;
  lastName: string;
  age: number;
}interface Employee {
  employeeCode: string;
  age: string;
}let employee: Animal | Person | Employee = {
  kind: 'human',
  firstName: 'Jane',
  lastName: 'Smith',
  age: '20'
}
```

然后，我们可以将一个数字或一个字符串赋给`age`属性。这符合 JavaScript 的动态特性，同时允许我们为对象分配数据类型。这不同于传统的面向对象代码，在传统的面向对象代码中，我们可以将公共成员抽象到父类中，然后从中派生出具有专用成员的子类。

管道符号表示联合类型的对象可以具有每种类型的零个、一些或所有属性。

访问联合类型的对象中的成员不同于从交集类型访问成员。具有交集类型的对象必须在每种类型的成员中列出所有属性，因此从逻辑上讲，我们可以访问对象中定义的所有属性。然而，联合类型就不是这样了，因为某些成员可能只对组成联合类型的某些类型可用。

如果我们有一个联合类型，那么我们只能访问在组成联合类型的所有类型中可用的成员。例如，如果我们有:

```
interface Animal {
  kind: string;
}interface Person {
  firstName: string;
  lastName: string;
  age: number;
}interface Employee {
  employeeCode: string;
  age: string;
}let employee: Animal | Person | Employee = {
  kind: 'human',
  firstName: 'Jane',
  lastName: 'Smith',
  age: '20'
}console.log(employee)
```

然后我们不能访问`employee`对象的任何属性，因为没有一个成员在所有类型中都可用。如果我们试图访问类似于`kind`属性的属性，我们将得到以下错误:

```
Property 'kind' does not exist on type 'Animal | Person | Employee'.Property 'kind' does not exist on type 'Person'.(2339)
```

如果我们想让某些属性可访问，我们可以编写类似下面的代码来让我们访问属性:

```
interface Animal {
  kind: string;
}interface Person {
  kind: string;
  firstName: string;
  lastName: string;
  age: number;
}interface Employee {
  kind: string;
  employeeCode: string;
}let employee: Animal | Person | Employee = {
  kind: 'human',
  firstName: 'Jane',
  lastName: 'Smith',
  age: 20,
  employeeCode: '123'
}console.log(employee.kind)
```

在上面的所有 3 个接口中，我们都有`kind`成员。因为它们存在于我们在 union 类型中使用的所有 3 个接口中，所以我们可以访问`employee.kind`属性。然后我们会在`console.log`语句中得到文本‘人类’。

交集类型允许我们将多种类型组合成一种类型。具有交集类型的对象的结构必须同时具有构成交集类型的所有类型的结构。它由多个类型通过一个`&`符号连接而成。联合类型创建了一个新类型，它允许我们创建具有创建联合类型的每种类型的部分或全部属性的对象。联合类型是通过用管道符号`|`连接多个而创建的。它让我们创建一个新类型，该类型具有构成联合类型的每种类型的一些结构。