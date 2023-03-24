# TypeScript 接口简介—对象文字和函数类型

> 原文：<https://levelup.gitconnected.com/introduction-to-typescript-interfaces-object-literals-and-function-types-3eafa3709336>

![](img/f84e665e5d870e0520b9d4f326bb4a1b.png)

Zoe Ra 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

与普通 JavaScript 相比，TypeScript 的最大优势在于，它通过添加确保程序对象类型安全的功能来扩展 JavaScript 的特性。它通过检查对象所呈现的值的形状来做到这一点。

检查形状被称为鸭分型或结构分型。接口是在 TypeScript 中填充角色命名数据类型的一种方式。这对于在 TypeScript 程序的代码中定义契约非常有用。在上一篇文章中，我们研究了如何定义一个 TypeScript 接口，并向它添加必需的和可选的属性。在本文中，我们将继续上一篇文章，看看 TypeScript 接口的其他属性。

# 超额财产检查

当对象属性被赋给一个由接口指定类型的变量时，它们会得到额外的检查。这也适用于我们作为参数传递给函数的对象文字。例如，下面的代码不会被 TypeScript 编译器编译，并给我们一个错误:

```
interface Person{
  name: string
}const greet = (person: Person) => {
  console.log(`Hello, ${person.name}`);
}greet({ name: 'Joe', foo: 'abc' });
```

TypeScript 编译器完成的额外属性检查将拒绝代码，因为我们有一个额外的未在`Person`接口中定义的`foo`属性，所以将它添加到参数中的对象会失败，因为 TypeScript 对对象文字进行了额外的属性检查。将相同的对象文本赋给变量也会失败。例如，如果我们有以下代码:

```
interface Person{
  name: string
}
const greet = (person: Person) => {
  console.log(`Hello, ${person.name}`);
}
const person: Person = { name: 'Joe', foo: 'abc' };
greet(person);
```

我们将得到错误“Type“{ name:string；foo:string；“}”不可赋给类型“Person”。Object literal 可能只指定已知的属性，如果我们尝试用 TypsScript 编译器编译代码或在支持 typscript 的文本编辑器中查看代码，则为“foo”。然而，我们可以使用类型断言操作符`as`来指定我们喜欢的对象文字的类型。因此，如果我们确定对象文字的类型是`Person`，即使它有一个`foo`属性，我们可以编写下面的代码:

```
interface Person{
  name: string
}
const greet = (person: Person) => {
  console.log(`Hello, ${person.name}`);
}
const person: Person = { name: 'Joe', foo: 'abc' } as Person;
greet(person);
```

有了上面的代码，TypeScript 编译器不会抱怨任何问题。它只是假设对象文字的类型是`Person`，即使它有一个`foo`属性。但是，我们确实有一些动态属性，或者可能只是偶尔出现，我们也可以将动态属性添加到我们的 TypeScript 接口中，如下面的代码所示:

```
interface Person{
  name: string,
  [prop: string]: any
}const greet = (person: Person) => {
  console.log(`Hello, ${person.name}. ${person.age ? `You're ${person.age} years old.` : ''}`);
}const person: Person = { name: 'Jane', age: 20 };
greet(person);
```

在上面的代码中，我们添加了:

```
[prop: string]: any
```

到我们的`Person`界面。上面的行意味着类型`Person`可以有除了`name`之外的任何其他属性。属性名是一个字符串，这是 JavaScript 中动态属性名的情况，并且这些动态属性可以采用任何值，因为为动态属性指定了`any`类型。正如我们所看到的，我们有下面这条线:

```
const person: Person = { name: 'Jane', age: 20 };
```

其中我们的对象文字有`age`属性，但是在我们的`interface`定义中没有明确定义。这是因为我们在`name`属性之后有了动态属性。`[prop: string]`被称为索引签名。

我们还可以通过将一个变量赋给另一个变量来绕过对象文字的额外属性检查。例如，如果我们有以下代码:

```
interface Person{
  name: string
}const greet = (person: Person) => {
  console.log(`Hello, ${person.name}. ${person.age ? `You're ${person.age} years old.` : ''}`);
}const person: Person = { name: 'Jane', age: 20 };
greet(person);
```

因为额外的属性检查而无法编译和运行，我们可以通过将`person`常量赋给一个没有指定类型的新变量或常量来解决这个问题，如下所示:

```
interface Person{
  name: string
}const greet = (person: Person) => {
  console.log(`Hello, ${person.name}`);
}const person = { name: 'Jane', age: 20 };
greet(person);
```

`person`常量没有指定类型，所以不会运行对象文字的多余属性检查。

对于简单的对象，比如我们上面提到的对象，建议强制执行超额属性检查。对于更复杂的动态对象，我们可以使用上面概述的方法来绕过检查，让代码运行。但是，请注意，大多数额外的属性错误实际上是我们代码中的拼写错误，所以它们是应该修复的合法错误。

![](img/36fdbbc993e45cbaa85a026c3c5a8af1.png)

Max Baskakov 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 功能类型

使用 TypeScript 接口，我们还可以通过指定每个参数的数据类型和函数的返回类型来定义函数的签名。这可以防止我们传入具有错误数据类型的参数，或者忘记将参数传入我们的函数调用，并且还可以确保我们的函数总是具有相同的返回类型，并且我们不会返回我们在代码中不期望的内容。

我们可以定义一个接口来指定函数的参数和返回数据类型，以及函数签名，如下面的代码所示:

```
interface GreetFn{
  (name: string, age: number): string
}const greet: GreetFn = (name: string, age: number) => {
  return `Hello, ${name}. You're ${age} years old`;
}console.log(greet('Jane', 20));
```

上面的代码有一个函数`greet`，它遵循在`GreetFn`接口中冒号左侧定义的函数签名和在接口右侧定义的返回数据类型，因此代码将运行并从最后一行的`console.log`语句中产生输出。我们应该得到'你好，简。你已经 20 岁了。如果我们用类型`GreetFn`指定我们的`greet`函数，但是我们的函数签名或返回类型偏离了在`GreetFn`接口中指定的类型，那么我们将会得到错误。例如，如果我们有:

```
interface GreetFn{
  (name: string, age: number): string
}
const greet: GreetFn = (name: string, age: number, foo: any) => {
  return `Hello, ${name}. You're ${age} years old`;
}
console.log(greet('Jane', 20));
```

然后我们会得到错误消息“Type”(name:string，age: number，foo: any) => string '不可赋给 type 'GreetFn '。(2322)“因为我们的参数列表与接口中列出的签名不匹配。同样，如果我们函数的返回类型与我们在接口中定义的不匹配，我们也会得到一个错误。例如，如果我们有以下代码:

```
interface GreetFn{
  (name: string, age: number): string
}
const greet: GreetFn = (name: string, age: number) => {
  return 0;
}
console.log(greet('Jane', 20));
```

我们将得到错误“Type”(name:string，age: number) => number '不能赋给 type 'GreetFn '。类型“number”不可赋给类型“string”。这意味着`greet`函数必须返回一个字符串，因为我们指定了`greet`函数的类型是`GreetFn`。

一次检查一个函数参数，因此即使我们在定义函数时没有指定类型，TypeScript 编译器也可以通过参数的位置来推断参数类型的位置。例如，即使我们没有明确指定参数的类型，下面的内容仍然有效:

```
interface GreetFn{
  (name: string, age: number): string
}
const greet: GreetFn = (name, age) => {
  return `Hello, ${name}. You're ${age} years old`;
}
console.log(greet('Jane', 20));
```

如果我们根据我们在下面代码中定义的接口传入错误数据类型的东西，我们会得到一个错误:

```
interface GreetFn{
  (name: string, age: number): string
}
const greet: GreetFn = (name, age) => {
  return `Hello, ${name}. You're ${age} years old`;
}
console.log(greet('Jane', ''));
```

当我们尝试编译上面的代码时，我们会得到错误“类型为“" "”的参数不可赋给类型为“number”的参数”。(2345)".这意味着 TypeScript 足够聪明，可以根据位置来推断类型。对返回类型也进行类型推断，因此如果我们编写以下代码:

```
interface GreetFn{
  (name: string, age: number): string
}
const greet: GreetFn = (name, age) => {
  return 0;
}
console.log(greet('Jane', 20));
```

然后我们会得到错误“Type”(name:string，age: number) => number '不能赋给 type 'GreetFn '。类型“number”不可赋给类型“string”。(2322)“所以代码无法编译。

对对象文字进行额外的属性检查非常有用，因为当我们给对象文字赋值或者将它们作为函数的参数传递时，我们很难在代码中添加错误的属性或拼写错误。我们可以通过类型断言或者给一个不同类型或者没有类型的变量赋值来解决这个问题。我们还可以为函数定义接口，以定义函数的预期参数以及它们的预期返回类型。