# TypeScript 3.7 中发布的很酷的新功能

> 原文：<https://levelup.gitconnected.com/cool-news-features-released-with-typescript-3-7-4d65adc8c994>

![](img/ea884014e1e6564f736df8e7f2fc77c8.png)

照片由 [Josh Rakower](https://unsplash.com/@joshrako?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

随着 TypeScript 3.7 的发布，ES2020 中包含的一些出色的新功能现已成为 TypeScript 的一部分。新特性包括可选链接、无效合并、检查未调用函数等等。

在本文中，我们将更详细地研究其中的一些。

# 可选链接

可选链接是一个特性，它让我们获得一个对象的深层嵌套属性，而不用担心任何属性是`null`还是`undefined`。

我们可以使用`?.`操作符来获取对象的属性，而不是使用通常的点操作符。例如，不要写:

```
let x = a.b.c.d;
```

我们写道:

```
let x = a?.b?.c?.d;
```

使用可选的链接操作符，我们不必担心属性`b`或`c`为空或未定义，这使我们不必编写大量代码来检查这些属性是否存在。

如果任何中间属性为空或未定义，则返回 undefined，而不是使应用程序出错。

这意味着我们不再需要写这样的东西:

```
let x = a && a.b && a.b.c && a.b.c.d;
```

从`a`对象中获取`d`属性。

需要注意的一点是，如果`strictNullChecks`标志为 on，那么如果我们在一个函数中使用可选的链接操作符对一个表达式进行操作，而这个函数的参数将可选的参数作为操作数，那么我们将会得到错误。

例如，如果我们写:

```
let a = { b: { c: { d: 100 } } };
const divide = (a?: { b: { c: { d: 100 } } }) => {
  return a?.b?.c?.d / 100;
}
```

然后我们得到错误“对象可能是‘未定义的’”。(2532)'.

# 无效合并

nullish 合并操作符让我们在某个值为空或未定义时给它赋予一个默认值。

带有无效合并的表达式如下所示:

```
let x = foo ?? bar;
```

其中`??`是无效合并运算符。

例如，我们可以如下使用它:

```
let x = null;
let y = x ?? 0;
console.log(y); // 0
```

然后我们得到 0 作为`y`的值。

这很方便，因为替代方法是使用`||`操作符来分配默认值。左操作数上的任何 falsy 值都会导致右操作数上的默认值被赋值，这可能并不总是我们想要的。

nullish 合并运算符仅在左操作数为空或未定义时分配默认值。

例如:

```
let x = 0;
let y = x || 0.5;
```

在上面的代码中，0 是一个可以赋给`y`的有效值，但是我们还是给它赋了默认值 0.5，因为 0 是 falsy，这是我们不希望的。

# 断言函数

TypeScript 3.7 附带了`asserts`关键字，允许我们编写自己的函数来对数据进行一些检查，如果检查失败，就会抛出一个错误。

例如，我们可以编写以下代码来检查传递给断言函数的参数是否是数字:

```
function assertIsNumber(x: any): asserts x is number {
    if (typeof x === 'number') {
        throw new Error('x is not a number');
    }
}
assertIsNumber('1');
assertIsNumber(1);
```

当我们运行上面的代码时，我们应该得到‘未捕获的错误:x 不是一个数字’。

在上面的代码中，`asserts`关键字检查它后面的任何条件。

这是一个返回`void`的函数，这意味着它不返回任何东西。它只能在不满足我们定义的条件时抛出错误。

![](img/2ad3d8320a048944a41a18f520c2d6d0.png)

[freestocks.org](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 更好地支持返回 Never 类型的函数

在 TypeScript 3.7 中，现在 TypeScript 编译器识别出返回`never`类型的函数运行在返回其他类型的函数中。

例如，在 TypeScript 3.7 之前，我们必须编写以下代码来避免错误:

```
const neverFn = (): never => { 
    throw new Error();
};const foo = (x: string | number): number => {
    if (typeof x === 'string') {
        return +x;
    }
    else if (typeof x === 'number') {
        return x;
    }
    return neverFn();
}
```

上面的代码会给我们一个错误“函数缺少结束返回语句，返回类型不包含‘undefined’。”因为我们确实在`neverFn()`函数调用之前加了`return`。

早于 3.7 的 TypeScript 版本不将 never 函数识别为有效路径，因为如果指定了返回类型，它不允许在返回`undefined`的代码中使用路径。

现在，如果使用 TypeScript 3.7 来编译代码，删除`return neverFn();`中的`return`就可以了。

# 允许一些递归类型别名

TypeScript 3.7 现在允许未分配给自身的类型别名。例如，以下内容仍然是不允许的:

```
type Bar = Bar;
```

因为它只是用自己永远取代了`Bar`类型。

如果我们试图编译上面的代码，我们会得到错误“类型别名‘Bar’循环引用自身”。(2456)".

然而，现在我们可以这样写:

```
interface Foo { };
interface Baz { };
type Bar = Foo | Baz | Bar[];
```

这是因为`Bar[]`类型没有直接替换`Bar`，所以这种类型的递归是允许的。

# AllowJs 标志打开时生成声明文件

在 TypeScript 3.7 之前，当`--allowJs`标志打开时，我们不能生成声明文件，所以用 JavaScript 编译的 TypeScript 代码不能生成任何声明文件。

这意味着不能对正在编译的 JavaScript 文件进行类型检查，即使它们有 JSDoc 声明。

有了这个改变，现在可以用这些 JavaScript 文件进行类型检查了。

现在我们可以用 JSDoc 带注释的 JavaScript 编写库，用同样的代码支持 TypeScript 用户。

从 3.7 开始，TypeScript 编译器将从 JSDoc 注释中推断 JavaScript 代码的类型。

# 未调用的函数检查

通过省略括号忘记调用函数是一个问题，有时会导致错误。例如，如果我们编写以下内容:

```
const foo = () => { };const bar = () => {
    if (foo) {
        return true;
    }
}
```

我们会得到错误“这个条件总是返回真，因为函数总是被定义的。你的意思是叫它代替？(2774)“当我们写上面的代码，尝试用 TypeScript 3.7 编译器编译时。

在 3.7 版本之前，这段代码不会给出任何错误。

# 结论

正如我们所看到的，TypeScript 3.7 给了我们许多以前没有的有用的新特性。可选的链接和 nullish 联合操作符可以方便地避免 null 或未定义的错误。

识别返回`never`类型的函数调用对于用不总是返回的路径编写代码也很方便。

拥有递归类型别名有助于将某些类型组合成一个别名。

对于编写库的开发人员来说，通过检查 JSDoc 注释，TypeScript 3.7 可以从用 TypeScript 编译器编译的 JavaScript 文件中推断类型。

最后，未调用的函数现在会被 TypeScript 编译器检查，如果它们被编写得像我们试图将它作为属性来访问一样。