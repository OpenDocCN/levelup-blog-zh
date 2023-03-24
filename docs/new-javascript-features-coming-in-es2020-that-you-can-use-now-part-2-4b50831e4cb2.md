# 您现在可以使用的 ES2020 中的新 JavaScript 特性(第 2 部分)

> 原文：<https://levelup.gitconnected.com/new-javascript-features-coming-in-es2020-that-you-can-use-now-part-2-4b50831e4cb2>

## 类中的静态字段/方法和顶级等待

![](img/3aa289f8beae74b63c8d8666d0b0440b.png)

帕特里克·托马索在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

自 2015 年以来，JavaScript 一直在快速发展，每次迭代都会推出许多新功能。JavaScript 语言规范的新版本每年都在更新，新的语言特性提案的完成速度比以往任何时候都要快。

这意味着新特性正以前所未有的速度融入现代浏览器和 Node.js 等其他 JavaScript 运行时引擎。2019 年，有许多新功能处于“阶段 3”阶段，这意味着它非常接近完成，浏览器现在开始支持这些功能。

如果我们想将它们用于生产代码，我们可以使用 Babel 之类的东西将它们转换成旧版本的 JavaScript，以便在需要时可以在旧版本的浏览器中使用，如 Internet Explorer。

在本文中，我们将研究类中的静态字段和方法，并在常规代码和模块导出中的顶层使用`await`关键字。

# 类中的静态字段和方法

类中的静态字段是 browsers 和 Node.js 即将推出的新特性，它允许我们为不需要创建类实例就能访问的类添加静态字段。它们可以是私有的，也可以是公共的。这是枚举的一个方便的替代品。例如，根据最新的提议，我们可以编写一个包含静态变量的类，如下例所示:

```
class Fruit {  
  static orange = 'orange';
  static grape = 'grape';
  static banana = 'banana';
  static apple = 'apple'; static #secretPear = 'pear';}console.log(Fruit.orange);
console.log(Fruit.grape);
console.log(Fruit.banana);
console.log(Fruit.apple);
```

上面的示例应该输出:

```
orange
grape
banana
apple
```

如果我们试图从类外部访问`secretPear`，如下面的代码所示:

```
console.log(Fruit.#secretPear);
```

我们将得到“未捕获的语法错误:私有字段“#secretPear”必须在封闭类中声明。”

关键字`static`从 ES6 开始就已经在类中使用方法了。例如，我们可以通过使用`static`关键字在类中声明一个静态方法，如下面的代码所示:

```
class ClassStatic {
  static staticMethod() {
    return 'Static method has been called.';
  }
}console.log(ClassStatic.staticMethod());
```

我们应该从`console.log`输出中得到‘静态方法已被调用’。正如我们所看到的，调用了`staticMethod`方法，但没有像我们预期的那样实例化类`ClassStatic`。静态方法也可以是私有的，我们只是在方法名前加一个`#`符号，使其私有。例如，我们可以编写以下代码:

```
class ClassStatic {
  static #privateStaticMethod(){
    return 'Static method has been called.';
  }

  static staticMethod() {
    return ClassStatic.#privateStaticMethod();
  }
}console.log(ClassStatic.staticMethod());
```

在上面的代码中，我们从`staticMethod`调用了`privateStaticMethod`来获取`staticMethod`中`privateStaticMethod`的返回值。然后当我们通过运行`ClassStatic.staticMethod()`调用`staticMethod`时，我们得到‘静态方法已被调用’然而，我们不能直接从类外调用`privateStaticMethod`，所以如果我们运行类似于:

```
class ClassStatic {
  static #privateStaticMethod(){
    return 'Static method has been called.';
  }

  static staticMethod() {
    return ClassStatic.#privateStaticMethod();
  }
}console.log(ClassStatic.#privateStaticMethod());
```

那么我们将得到一个错误。另外，请注意，我们没有使用`this`来访问`privateStaticMethod`，我们使用了类名`ClassStatic`来访问。如果我们使用`this`来调用它，就像在`this.#privateStaticMethod()`中一样，我们将得到一个错误或`undefined`，这取决于我们使用的 Babel 版本。浏览器部分支持变量的关键字`static`。Chrome 和 Opera 等 Chromium 浏览器都支持。Node.js 也支持这个关键字。其他浏览器还不支持它，所以我们必须使用 Babel 将其转换成旧的 JavaScript 代码，以便在其他浏览器上运行。

![](img/4de21bf06dc5ba0c1efbd91f8ffd489a.png)

[凯·皮尔格](https://unsplash.com/@kaip?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 顶级等待

目前，`await`关键字只能在`async`函数中使用，它是返回承诺的函数。如果我们在一个`async`函数之外使用`await`关键字，我们将会得到一个类似‘不能在一个异步函数之外使用关键字’这样的错误，我们的程序将不会运行。现在，我们可以使用`await`关键字，而不必将其放在`async`函数中。例如，我们可以在下面的代码中使用它:

```
const promise1 = new Promise((resolve)=>{
  setTimeout(()=> resolve('promise1 resolved'), 1000);
})const promise2 = new Promise((resolve)=>{
  setTimeout(()=> resolve('promise2 resolved'), 1000);
})const val1 = await promise1;
console.log(val1);
const val2 =  await promise2;
console.log(val2);
```

然后，当我们在 Chrome 中运行上面的代码时，我们会得到:

```
promise1 resolved
promise2 resolved
```

有了顶级的`await`，我们可以随心所欲地运行异步代码，而且我们不必再使用冗长的`then`函数链，将回调传递给每个`then`函数调用。这非常简洁，如果我们不想声明一个命名函数，我们不必将它包装在一个`async`life(立即调用的函数表达式)中来运行它。

顶级`await`的另一个好处是我们可以直接导出它。这意味着当模块导出带有`await`关键字的东西时，它将在运行任何东西之前等待作为`await`关键字的承诺被解析。表面上，它的行为就像是照常同步导入一样，但在本质上，它实际上是在等待`export`表达式中的承诺进行解析。例如，我们可以写:

```
const promise1 = new Promise((resolve)=>{
  setTimeout(()=> resolve('promise1 resolved'), 1000);
})
export const val1 = await promise1;
```

当我们在`module2.js`中导入`module1.js`时:

```
import { val1 } from './module1';
console.log(val1);
```

然后我们可以看到记录的`val1`的值，好像它正在同步运行，等待`promise1`解析。

# 结论

类中的静态字段是 browsers 和 Node.js 即将推出的新特性，它允许我们为不需要创建类实例就能访问的类添加静态字段。它们可以是私有的，也可以是公共的。这是枚举的一个方便的替代品。

静态方法从 ES6 开始就存在了，所以它现在可以使用，并得到了广泛的支持。一旦特性完成，在常规代码和模块导出的顶层使用`await`关键字会非常方便。它允许我们使用`await`在`async`函数之外编写异步代码，这比使用带有回调的`then`要干净得多。

此外，它可以让我们导出异步代码的解析值，然后导入它，并在其他模块中使用它，就像它是同步代码一样。