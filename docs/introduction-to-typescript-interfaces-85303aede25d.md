# TypeScript 接口简介

> 原文：<https://levelup.gitconnected.com/introduction-to-typescript-interfaces-85303aede25d>

![](img/5a08e8d48ae343839b0a754ce215dfc5.png)

艾默生·彼得斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

与普通 JavaScript 相比，TypeScript 的最大优势在于，它通过为我们的程序对象添加类型安全来扩展 JavaScript 的特性。它通过检查对象可以采用的值的形状来做到这一点。检查形状被称为鸭分型或结构分型。这对于在 TypeScript 程序的代码中定义契约非常有用。在本文中，我们将研究如何定义一个 TypeScript 接口，并向它添加必需的或可选的属性。

# 定义接口

为了定义一个基本接口，我们在 TypeScript 中使用了`interface`关键字。这个关键字是 TypeScript 专有的，在 JavaScript 中不可用。我们可以像下面的代码那样定义一个 TypeScript 接口:

```
interface Person {
  name: string
}
```

在上面的代码中，如果一个变量或参数已经用这个接口指定，那么所有使用它的类型的对象都将拥有`name`属性。分配给类型为的变量的对象文本不能有任何其他属性。作为这种类型的参数传入的参数也只能具有此属性。例如，下面的代码将成功编译，并使用 TypeScript 编译器运行:

```
interface Person{
  name: string
}const greet = (person: Person) => {
  console.log(`Hello, ${person.name}`);
}
greet({ name: 'Joe' });
```

上面的代码将会编译并运行，因为它只有`name`属性，并且它的值是一个字符串，就像在`Person`接口中指定的一样。但是，下面的代码不会被 TypeScript 编译器编译并引发错误:

```
interface Person {
  name: string
}const greet = (person: Person) => {
  console.log(`Hello, ${person.name}`);
}greet({ name: 'Joe', foo: 'abc' });
```

这是因为我们指定了`greet`函数中的`person`参数的类型为`Person`。作为参数传入的对象只能有`name`属性，并且它的值只能是字符串。如果一个变量已经被接口的类型指定了，我们就不能给一个对象赋予与接口中列出的属性不同的属性:

```
const p: Person = { name: 'Joe', foo: 'abc' };
```

上面的代码也不会编译，因为 object literal 有一个额外的属性没有在`Person`接口中列出。在支持 TypeScript 的编辑器(如 Visual Studio 代码)中，我们会得到错误消息“Type“{ name:string；foo:string；“}”不可赋给类型“Person”。Object literal 只能指定已知的属性，并且类型“Person”中不存在“foo”。

使用接口，当指定变量的类型时，当我们写对象文字时，我们得到代码的自动完成。

# 可选属性

TypeScript 接口可以有可选的属性。这使得接口比仅仅添加所需的属性更加灵活。我们可以在属性名后面加上问号`?`来指定一个可选属性。例如，我们可以编写以下代码来定义具有可选属性的接口:

```
interface Person {
  name: string,
  age?: number
}
```

在上面的代码中，`age`是一个可选属性，因为我们在属性名后面加了一个问号。我们可以用它作为下面的代码:

```
interface Person {
  name: string,
  age?: number
}const greet = (person: Person) => {
  console.log(`Hello, ${person.name}. ${person.age ? `You're ${person.age} years old.` : ''}`);
}greet({ name: 'Joe', age: 10 });
```

在我们传递给上面代码中的`greet`函数的对象中，我们传递了`age`属性及其值的一个数字。然后我们在代码中使用它，首先检查它是否被定义，如果被定义，我们在主字符串中添加额外的文本。我们也可以省略可选属性，如下面的代码所示:

```
interface Person {
  name: string,
  age?: number
}const greet = (person: Person) => {
  console.log(`Hello, ${person.name}. ${person.age ? `You're ${person.age} years old.` : ''}`);
}greet({ name: 'Joe' });
```

上面的代码仍然会运行，因为`age`属性已经被指定为可选的。如果我们给它分配一个对象文字，那么接口概述的规则仍然适用。例如，如果我们写:

```
interface Person {
  name: string,
  age?: number
}const greet = (person: Person) => {
  console.log(`Hello, ${person.name}. ${person.age ? `You're ${person.age} years old.` : ''}`);
}const person: Person = { name: 'Joe' };
greet(person);
```

这仍然有效，因为我们坚持在`Person`接口中定义属性描述。如果我们添加带有数字属性的`age`属性，它仍然可以编译和运行:

```
interface Person {
  name: string,
  age?: number
}const greet = (person: Person) => {
  console.log(`Hello, ${person.name}. ${person.age ? `You're ${person.age} years old.` : ''}`);
}const person: Person = { name: 'Joe', age: 20 }
greet(person);
```

可选属性很有用，因为我们可以定义可能使用的属性，同时防止使用不属于接口的属性。这可以防止由于代码中的拼写错误而产生的错误。

![](img/dd6a7e8ef1dc340e1bf4bf2ce92d95ed.png)

图片由 [Adam Grabek](https://unsplash.com/@agmakonts?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 只读属性

为了使属性在对象第一次被创建后不可修改，我们可以在属性前使用`readonly`关键字来指定属性只能在对象被创建时写一次，以后任何时候都不能写。例如，我们可以编写以下代码:

```
interface Person {
  readonly name: string,
  age?: number
}const greet = (person: Person) => {
  console.log(`Hello, ${person.name}. ${person.age ? `You're ${person.age} years old.` : ''}`);
}const person: Person = { name: 'Joe', age: 20 };
greet(person);
```

我们在`name`属性前面添加了`readonly`关键字，这样它只能被修改一次，而且只能修改一次。因此，如果我们试图给`name`属性赋予新的内容，TypeScript 编译器将不会编译和编码，代码也不会运行:

```
interface Person{
  readonly name: string,
  age?: number
}const greet = (person: Person) => {
  console.log(`Hello, ${person.name}. ${person.age ? `You're ${person.age} years old.` : ''}`);
}let person: Person = { name: 'Joe', age: 20 };
person.name = 'Jane';
greet(person);
```

当我们尝试编译它或在支持 TypeScript 的文本编辑器中编译它时，上面的代码会给我们错误消息“无法分配给' name ',因为它是只读属性”。但是，我们可以将整个对象重新分配给不同的值，如下面的代码所示:

```
interface Person {
  readonly name: string,
  age?: number
}const greet = (person: Person) => {
  console.log(`Hello, ${person.name}. ${person.age ? `You're ${person.age} years old.` : ''}`);
}let person: Person = { name: 'Joe', age: 20 };
person = { name: 'Jane', age: 20 };
greet(person);
```

然后不是记录“你好，乔。你已经 20 岁了。，我们得到‘你好，简。你已经 20 岁了。'

如果我们想将一个数组指定为只读的，也就是说，只在它被创建时才被改变，我们可以使用与`Array`相同的`ReadonlyArray`类型，但是所有的变异方法都被从中移除了，这样我们就不会意外地改变数组中的任何内容。例如，我们可以在下面的代码中使用它:

```
let readOnlyArray: ReadonlyArray<number> = [1,2,3];
```

然后，如果我们尝试编写以下代码，TypeScript 编译器会给出错误:

```
readOnlyArray[0] = 12;
readOnlyArray.push(5);
readOnlyArray.length = 100;
```

然后我们得到以下错误:

```
Index signature in type 'readonly number[]' only permits reading.(2542)Property 'push' does not exist on type 'readonly number[]'.(2339)Cannot assign to 'length' because it is a read-only property.(2540)
```

如果我们想将一个`ReadonlyArray`转换回一个可写数组，我们可以使用类型断言`as`操作符将其转换回一个常规数组:

```
let arr = readOnlyArray as number[];
```

## 结论

TypeScript 接口对于在我们的代码中定义契约非常方便。我们可以用它来指定变量和函数参数的类型。它们让我们知道变量可以具有什么属性，以及它们是必需的、可选的还是只读的。我们可以用`interfaces`关键字定义接口，用变量名后的问号定义可选属性，用`readonly`关键字定义只读属性。