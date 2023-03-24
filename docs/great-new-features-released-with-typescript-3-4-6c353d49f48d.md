# TypeScript 3.4 中发布的优秀新功能

> 原文：<https://levelup.gitconnected.com/great-new-features-released-with-typescript-3-4-6c353d49f48d>

![](img/7bf52b6dcd334b16b6daa256fc8630c8.png)

照片由 [Elena Taranenko](https://unsplash.com/@elenatrn?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

TypeScript 每天都在改进。我们在每个版本中都不断获得新的特性。在本文中，我们将看看 TypeScript 3.4 中发布的新内容。

新特性包括更好的高阶泛型函数的类型推断。对`readonly`类型的更改和使用`--increment`标志的更快构建，等等。

# TypeScript 3.4 中的新功能

## -增量标志

为了在第一次构建后加快构建速度，TypeScript 编译器的`--incremental`标志将让我们只根据发生的变化来构建。

我们可以将选项添加到我们项目的`tsconfig.json`来获得这个特性，在`compilerOptions`部分下，如下所示:

```
{
    "compilerOptions": {
        "incremental": true,
        "outDir": "./lib"
    },
    "include": ["./src"]
}
```

它通过寻找第一次构建时创建的`.tsbuildinfo`来工作。如果它不存在，那么它会被生成。它将使用这个文件来了解什么已经建立，什么还没有。

它可以被安全地删除，不会影响我们的构建。我们可以通过在`compilerOptions`部分`tsconfig.json`添加一个`tsBuildInfoFile`选项，用不同的名称命名文件，如下所示:

```
{
    "compilerOptions": {
        "incremental": true,
        "tsBuildInfoFile": "./front-end-app",
        "outDir": "./lib"
    },
    "include": ["./src"]
}
```

对于在`tsconfig.json`中将`composite`标志设置为`true`的复合项目，不同项目之间的引用也可以增量构建。这些项目总是会生成一个`.tsbuildinfo`文件。

当使用`outFile`选项时，构建信息文件名将基于输出文件名。例如，如果输出文件是`foo.js`，那么构建信息文件将是`foo.tsbuildinfo`。

# 一般函数中的高阶类型推理

当我们的函数将其他函数作为参数时，我们将得到传入和返回的函数类型的类型推断。

例如，如果我们有一个由多个函数组成的函数，返回一个新函数，如下所示:

```
function compose<A, B, C, D>(
    f: (arg: A) => B,
    g: (arg: B) => C,
    h: (arg: C) => D
): (arg: A) => D {
    return x => h(g(f(x)));
}
```

当我们按如下方式填写通用标记的类型时:

```
function compose<A, B, C, D>(
    f: (arg: A) => B,
    g: (arg: B) => C,
    h: (arg: C) => D
): (arg: A) => D {
    return x => h(g(f(x)));
}interface Employee {
    name: string;    
}const getName = (employee) => employee.name;
const splitString = (name) => name.split('');
const getLength = (name) => name.length;const fn = compose(getName, splitString, getLength)
```

然后我们可以通过编写来调用`fn`函数:

```
const len: number = fn(<Employee>{ name: 'Joe' });
```

TypeScript 3.4 或更高版本足够智能，可以遍历函数调用链，并自动推断每个函数的类型以及从`compose`返回的函数的返回类型。

它可以推断出`fn`返回一个数字。

早于 3.4 的 TypeScript 版本将推断出空对象类型，并且我们得到上面的赋值表达式的错误。

# `ReadonlyArray`和`readonly`元组

在 TypeScript 3.4 中，使用只读数组类型现在更容易了。我们现在可以用关键字`readonly`声明一个只读数组。

例如，如果我们想要一个只读的字符串数组，我们可以写:

```
const strArr: readonly string[] = ['a', 'b', 'c'];
```

现在我们有了一个数组，我们不能将它压入、更改条目或做任何修改数组的事情。

与`ReadOnlyArray<string>`型相比，这款更加紧凑。

在 TypeScript 3.4 中，我们有了新的只读元组类型。我们可以声明一个只读元组，如下所示:

```
const strTuple: readonly [string, string] = ['foo', 'bar'];
```

映射类型上的`readonly`修饰符将把类似数组的类型转换成它们对应的`readonly`类型。

例如，如果我们有这样的类型:

```
type ReadOnly<T> = {
    readonly [K in keyof T]: T[K]
}
```

然后，当我们将一个类型传入通用类型占位符`Readonly`时，如下所示:

```
type foo = Readonly<{foo: number, bar: string}>;
```

我们得到`foo`类型为:

```
type foo = {
    readonly foo: number;
    readonly bar: string;
}
```

正如我们所看到的，两个字段都变成了`readonly`，这在 TypeScript 3.4 之前并不是这样。

我们也可以使用映射类型从所有字段中移除`readonly`修饰符。为此，我们在`readonly`修饰符前添加了一个`-`。

例如，我们可以写:

```
type Writable<T> = {
    -readonly [K in keyof T]: T[K]
}interface Foo{
    readonly foo: string;
    readonly bar: number;
}type foo = Writable<Foo>;
```

然后我们得到:

```
type WriteFoo = {
    foo: string;
    bar: number;
}
```

对于类型`foo`。

`readonly`修饰符只能用于数组类型和元组类型的语法。不能用在别的东西上。

![](img/2ae6815fcc0a529bba64de98f017716b.png)

由[艾琳·威尔森](https://unsplash.com/@erinw?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 常量断言

TypeScript 3.4 引入了一个名为`const`断言的构造函数。当我们使用它时，我们发出信号，文字类型不能改变为范围更广的类型，比如从`1`到`string`。对象文字得到`readonly`属性。数组文字变成了`readonly`元组。

例如，以下是有效的:

```
let x: 'foo' = "foo" as const;
```

当我们检查它的类型时，我们得到`x`是类型`'foo'`。

另一个例子是数字数组:

```
let x = [1, 2] as const;
```

当我们将鼠标悬停在`x`上方时，我们得到的类型是`readonly [1, 2]`。

# 结论

在 TypeScript 3.4 中，我们对只读类型进行了多处修改，包括使用`readonly`关键字来声明只读数组和元组。

此外，我们可以添加和删除映射类型的`readonly`修饰符，在索引签名或字段名之前添加`readonly`和`-readonly`修饰符。

`const`断言用于将值转换成只读实体。

高阶泛型函数允许我们将多个函数组合在一起返回一个新的组合函数，与早期版本相比，它还具有更智能的类型推断。

最后，我们有`--incremental`标志来创建增量构建，这使得代码在后续构建中构建得更快。