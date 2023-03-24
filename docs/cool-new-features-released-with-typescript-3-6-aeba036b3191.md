# TypeScript 3.6 发布的很酷的新功能

> 原文：<https://levelup.gitconnected.com/cool-new-features-released-with-typescript-3-6-aeba036b3191>

![](img/02cbdb8355b07303f6c7a458ffc400df.png)

照片由[俊介小野](https://unsplash.com/@genmai?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

TypeScript 3.6 发布了许多新功能。它包含了可迭代的特性，比如更严格的生成器类型检查，更精确的数组分布，允许在`declare`语句中使用`get`和`set`，等等。

在本文中，我们将逐一介绍它们。

# 对发电机进行更严格的类型检查

在 TypeScript 3.6 中，TypeScript 编译器对生成器中的数据类型进行了更多的检查。

现在我们有一种方法来区分我们的代码`yield`或`return`是否来自生成器。

例如，如果我们有以下生成器:

```
function* bar() {
    yield 1;
    yield 2;
    return "Finished!"
}let iterator = bar();
let curr = iterator.next();
curr = iterator.next();if (curr.done) {
    curr.value
}
```

TypeScript 3.6 编译器自动知道`curr.value`是一个字符串，因为我们在函数的末尾返回了一个字符串。

此外，当我们将`yield`赋值给某个对象时，`yield`不会被假定为`any`类型。

例如，我们有以下代码:

```
function* bar() {
    let x: { foo(): void } = yield;    
}let iterator = bar();
iterator.next();
iterator.next(123);
```

现在 TypeScript 编译器知道 123 不能赋给类型为`{ foo(): void }`的东西，类型为`x`。而在早期版本中，编译器不检查上面代码的类型。

因此，在 TypeScript 3.6 或更高版本中，我们会得到错误:

```
Argument of type '[123]' is not assignable to parameter of type '[] | [{ foo(): void; }]'.Type '[123]' is not assignable to type '[{ foo(): void; }]'.Type '123' is not assignable to type '{ foo(): void; }'.
```

此外，现在`Generator`和`Iterator`的类型定义有了`return`和`throw`方法，并且是可迭代的。

TypeScript 3.6 将`IteratorResult`转换为`IteratorYieldResult<T> | IteratorReturnResult<TReturn>`联合类型。

它还可以从调用它的地方推断出从`next()`返回的值。

例如，由于我们将正确类型的值传入了字符串`next()`，下面的代码将会编译并运行:

```
function* bar() {
    let x: string = yield;
    console.log(x.toUpperCase());
}let x = bar();
x.next(); 
x.next('foo');
```

但是，由于参数和`x`的类型不匹配，下面的代码将无法编译:

```
function* bar() {
    let x: string = yield;
    console.log(x.toUpperCase());
}let x = bar();
x.next(); 
x.next(42);
```

我们必须传入一个字符串来修复从`x.next(42);`中产生的。此外，TypeScript 编译器知道对`next()`的第一次调用没有任何作用。

# 更精确的阵列排列

在 TypeScript 3.6 中，当代码转换为 ES5 或更早的目标且`--downlevelIteration`打开时，一些数组展开运算符的转换现在会产生等效的结果。

该标志的目的是转换 ES6 迭代结构，如 spread 操作符和`for...of`循环，以将对 ES6 迭代结构的支持添加到转换到比 ES6 更老的代码中。

例如，如果我们有:

```
[...Array(3)]
```

我们应该得到:

```
[undefined, undefined, undefined]
```

但是，早于 3.6 的 TypeScript 版本将`[…Array(3)]`更改为`Array(3).slice();`

这将得到一个空数组，其中的`length`属性设置为 3。

# 用不良承诺代码引发错误

如果我们忘记在`async`函数中把`await`放在承诺之前，或者忘记在承诺之后调用`then`，TypeScript 3.6 编译器会让我们知道。

例如，如果我们有:

```
interface Person {
    name: string;    
}let promise: Promise<Person> = Promise.resolve(<Person>{ name: 'Joe' });
(async () => {
    const person: Person = promise;
})();
```

然后我们得到错误:

```
Property 'name' is missing in type 'Promise<Person>' but required in type 'Person'.
```

在`promise`之前放入`await`可以解决问题:

```
interface Person {
    name: string;    
}let promise: Promise<Person> = Promise.resolve(<Person>{ name: 'Joe' });
(async () => {
    const person: Person = await promise;
})();
```

写着这样的东西:

```
(async () => {
      fetch("https://reddit.com/r/javascript.json")
        .json()
})();
```

会给我们带来错误:

```
Property 'json' does not exist on type 'Promise<Response>'.(2339)input.ts(3, 10): Did you forget to use 'await'?
```

以下内容将修复该错误:

```
(async () => {
  const response = await fetch("[https://reddit.com/r/javascript.json](https://reddit.com/r/javascript.json)")
  const responseJson = response.json()  
})();
```

![](img/5fca0ec1192c71327fb8a631d75c316d.png)

照片由[埃迪·拉克曼](https://unsplash.com/@eddyray?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# Unicode 字符标识符

现在，我们可以使用 Unicode 字符作为 TypeScript 的标识符。例如:

```
const 😚 = 'foo';
```

将使用 TypeScript 3.6 编译器或更高版本。

# 在 Declare 语句中允许使用`get`和`set`访问器

我们现在可以将`get`和`set`添加到`declare`语句中。例如，我们可以写:

```
declare class Bar {    
  get y(): number;
  set y(val: number);
}
```

在 TypeScript 3.7 或更高版本中，生成的类型定义还将发出`get`和`set`访问器。

# 合并类和构造函数声明语句

在 TypeScript 3.6 或更高版本中，编译器足够聪明，可以合并同名的函数构造函数和类`declare`语句。例如，我们可以写:

```
export declare function Person(name: string, age: number): Person;
export declare class Person {
    name: string;
    age: number;
    constructor(name: string, age: number);
}
```

它知道函数是一个构造函数，类和函数是一样的。

函数和类构造函数中的构造函数的签名不必匹配，因此:

```
export declare function Person(name: string): Person;
export declare class Person {
    name: string;
    age: number;
    constructor(name: string, age: number);
}
```

仍然有效。

# 分号

现在，TypeScript 足够智能，可以根据样式约定在需要分号的地方自动添加分号，而不是自动添加到每个语句中。

TypeScript 3.6 是另一个功能丰富的版本。它专注于改进一些特性，比如推断类型和生成器中的类型检查，以及在 ES5 或更早版本中为代码提供更精确的数组分布。

同样，糟糕的承诺代码会引发错误，比如错过了`await`或`then`的错误。

现在也支持在`declare`语句中合并函数构造函数和类代码。

标识符现在支持 Unicode 字符，分号不会自动添加到每一行。