# TypeScript 如何知道哪些类型是兼容的？—对象和功能

> 原文：<https://levelup.gitconnected.com/how-does-typescript-know-which-types-are-compatible-with-each-other-objects-and-functions-924086117004>

![](img/740f34fbfe4c3198fa80f6ca2f38e116.png)

Vincent van Zalinge 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

与 JavaScript 相比，TypeScript 的优势在于我们可以用它来注释变量、函数和其他实体的数据类型。这使我们能够在文本编辑器中自动完成，并且它还为构建应用程序添加了一个编译步骤，这意味着编译时检查可以捕获更多错误，如未定义的错误和意外的数据类型错误，否则这些错误将在运行时被捕获。

为此，TypeScript 根据实体的结构和其他方面(如参数的数量和每个参数的类型等)检查不同的变量是否具有相同的类型。在本文中，我们将了解 TypeScript 如何确定哪些数据类型相互兼容。

TypeScript 的类型系统允许一些在编译时无法知道的操作是安全的。当一个类型系统不允许编译时未知的动作时，它就是健全的。然而，由于 TypeScript 是 JavaScript 的超集，它必须允许在编译时执行一些不合理的操作。

# 基本类型兼容性

TypeScript 用来确定类型兼容性的最基本的规则是，如果两种类型具有相同的结构，那么它就被认为是兼容的类型。具有相同的结构意味着两种类型具有相同的成员名称，并且每个成员具有相同的类型。例如，如果我们有:

```
interface A {
  name: string;
  age: number;
}interface B {
  name: string;
  age: number;
}let x: A = { name: 'Joe', age: 10 };
let y: B = { name: 'Jane', age: 20 };
x = y;
```

然后 TypeScript 将接受代码并编译它，因为两个接口`A`和`B`都有`name`和`age`作为它们的字段，并且两个接口中的`name`和`age`都具有相同的类型。此外，如果一个对象具有一个类型的所有属性和接口中没有的额外属性，那么该对象也可以被赋给一个没有类型注释的变量，然后该变量可以被赋给一个设置了类型的变量。例如，如果我们有以下代码:

```
interface A {
  name: string;
  age: number;
}let y: A;
let x = { name: 'Joe', age: 10, gender: 'male' };
y = x;
```

其中`y`的数据类型为`A`，那么我们可以将一个对象赋给一个没有数据类型注释的变量，即`x`，然后我们可以将它赋给类型为`A`的`y`。这是因为只有对象文字有多余属性检查。变量没有这种检查。

同样，在将函数赋给变量时，也会检查函数的数据类型。我们可以传入一个比参数类型有更多属性的对象。例如，我们可以编写类似下面的代码:

```
interface Person {
  name: string;
  age: number;
}let person = { name: 'Joe', age: 10, gender: 'male' };function echo(person: Person) {
  return person;
}echo(person);
```

`person`对象有一个不在`Person`接口中的`gender`属性。但是`echo`函数中的`person`参数属于`Person`类型。考虑到这一点，我们仍然可以将`person`作为`echo`函数调用的第一个参数传递给它。TypeScript 不关心我们传入的参数中是否有额外的属性。另一方面，如果我们的参数缺少属性，那么 TypeScript 会给我们一个错误，并且不会编译代码。例如，如果我们有以下代码:

```
interface Person {
  name: string;
  age: number;
}let person = { name: 'Joe' };function echo(person: Person) {
  return person;
}echo(person);
```

然后我们会得到错误:

```
Argument of type '{ name: string; }' is not assignable to parameter of type 'Person'.Property 'age' is missing in type '{ name: string; }' but required in type 'Person'.(2345)input.ts(3, 3): 'age' is declared here.
```

类型兼容性的结构检查是递归完成的。所以同样的检查也适用于任何嵌套层次的对象。例如，如果我们有:

```
interface Person {
  name: string;
  age: number;
  address: {
    street: string;
  }
}let person = { name: 'Joe', age: 20, address: {street: '123 A St.'} };function echo(person: Person) {
  return person;
}echo(person);
```

这将通过由 TypeScript 完成的结构类型检查，因为每个级别的所有属性都存在。另一方面，如果我们有:

```
interface Person {
  name: string;
  age: number;
  address: {
    street: string;
  }
}let person = { name: 'Joe', age: 20, address: { } };function echo(person: Person) {
  return person;
}echo(person);
```

那么 TypeScript 编译器会给出以下错误:

```
Argument of type '{ name: string; age: number; address: {}; }' is not assignable to parameter of type 'Person'.Types of property 'address' are incompatible.Property 'street' is missing in type '{}' but required in type '{ street: string; }'.(2345)input.ts(5, 5): 'street' is declared here.
```

因为分配给`address`属性的对象中缺少`street`属性。

![](img/d14483b349b4140d0902c2e2af72a68b.png)

[雷鹏](https://unsplash.com/@luipeng?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 比较函数

比较对象及其类型的原始值非常简单。然而，确定哪些函数具有相互兼容的类型是比较困难的。在 TypeScript 中，我们可以将参数较少的函数赋给参数较多但函数签名相同的函数。例如，如果我们有:

```
let x = (a: number) => 0;
let y = (a: number, b: string) => 0;
```

然后我们可以将`x`赋值给`y`，就像我们在下面的代码中做的那样:

```
y = x;
```

这是因为，在 JavaScript 中，我们经常可以将默认情况下应该有更多参数的函数分配给有更少参数的函数。例如，array `map`方法接受一个回调函数，它有两个参数，一个是正在处理的数组条目，另一个是条目的数组索引。然而，我们有时只是传入一个只有第一个参数的回调函数，如下面的代码所示:

```
let arr = ['a', 'b', 'c'];
arr.map(a => a);
```

TypeScript 必须允许丢弃参数，以保持与 JavaScript 的兼容性。同样，为了比较返回类型，TypeScript 确定返回类型具有更多属性的函数与具有较少属性但结构相同的函数兼容。例如，如果我们有如下代码中的两个函数:

```
let fn1 = () => ({ name: "Alice" });
let fn2 = () => ({ name: "Joe", age: 20 });
```

然后，我们可以将`fn2`赋值给`fn1`，如以下代码所示:

```
fn1 = fn2;
```

然而，反过来就不行了:

```
fn2 = fn1;
```

如果我们尝试编译上面的代码，我们会从 TypeScript 编译器得到以下错误:

```
Type '() => { name: string; }' is not assignable to type '() => { name: string; age: number; }'.Property 'age' is missing in type '{ name: string; }' but required in type '{ name: string; age: number; }'.(2322)input.ts(2, 33): 'age' is declared here.
```

正如我们所看到的，TypeScript 接受属性更多的返回类型，而不是属性更少但结构相同的返回类型。

# 结论

TypeScript 通过对象和函数的结构来检查它们的数据类型。通常，如果两种类型具有相同的属性和数据类型，那么它们被认为是相同的类型。否则，属性比另一个多但结构相同的对象也被认为是兼容的。

同样，如果一个函数的参数比另一个少，但结构相同，那么它们被认为是相同的。这也适用于由函数返回的对象的结构。如果一个函数返回的对象的属性比另一个函数多，而另一个函数返回的对象的属性比另一个函数少，但结构相同，那么这两种返回类型被认为是兼容的。