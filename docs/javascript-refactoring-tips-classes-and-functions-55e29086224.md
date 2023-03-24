# JavaScript 重构技巧—类和函数

> 原文：<https://levelup.gitconnected.com/javascript-refactoring-tips-classes-and-functions-55e29086224>

![](img/46027bc679d8b94a742bfc94056d0a3f.png)

[梁杰森](https://unsplash.com/@ninjason?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难写出一段干净的 JavaScript 代码。

在本文中，我们将看看如何重构我们的 JavaScript 函数，使其清晰易读。

# 将构造函数重构为类

在 JavaScript 中，类只是构造函数的语法糖，所以它们实际上是函数。

当我们扩展另一个构造函数时，通过避免处理原型和使用`call`调用父构造函数，类所做的就是为构造函数提供更直观的语法。

例如，不写:

```
function Foo(name) {
  this.name = name;
}Foo.prototype.getName = function() {
  return this.name;
}
```

我们写道:

```
class Foo {
  constructor(name) {
    this.name = name;
  } getName() {
    return this.name;
  }
}
```

正如我们所看到的，使用类语法，构造函数在哪里以及我们的类包含哪些实例方法就很清楚了。

我们没有将实例方法附加到构造函数的原型上，而是将其添加到类中。

我们也不必一直写出关键字`function`，这使得打字更短，同时保持代码易于理解。这种语法没有歧义。

如果我们想做继承，而不是写作:

```
function Animal(name) {
  this.name = name;
}
Animal.prototype.getName = function() {
  return this.name;
}function Cat(name) {
  Animal.call(this, name);
}
Cat.prototype.constructor = Animal;
```

我们写道:

```
class Animal {
  constructor(name) {
    this.name = name;
  }
  getName() {
    return this.name;
  }
}class Cat extends Animal {
  constructor(name) {
    super(name);
  }
}
```

这样，我们会得到一个错误，就是我们忘记了调用父构造函数，而在构造函数的例子中，当我们忘记调用父构造函数时，我们不会得到这个错误。

我们还明确表示，我们正在用关键字`extends`扩展`Animal`类，而不是必须自己在子构造函数中设置父构造函数。

![](img/61df319a03d6929179adda7bd24565ad.png)

由 [David Clode](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 尽可能避免传统功能

ES2015 之前的一切都使用传统功能。它被用于封装代码，被用作块，被用于我们上面提到的构造函数，被用于回调，等等。

这些情况中的大多数已经被 ES2015 中引入的其他构造函数所取代。

如果我们想封装代码，我们可以使用块。例如，代替编写如下的立即调用的函数表达式(IIFE ):

```
(function() {
  let x = 1;
  console.log(x);
})()
```

我们可以改为写:

```
{
  let x = 1;
  console.log(x);
}
```

第二个例子更短，我们只需要用花括号来分隔代码块。

变量`x`在上面代码的代码块之外不可用。

封装代码的另一种方法是使用模块。例如，我们可以编写以下代码:

`module.js`

```
export const x = 1;
const y = 2;
const z = 3;
export default y;
```

`index.js`

```
import { x } from "./module";
import y from "./module";
console.log(x, y);
```

在上面的代码中，我们只在用`export`关键字导出它们的时候暴露我们所拥有的。因此，`x`和`y`可以从另一个模块导入。

我们可以看到，`x`和`y`是从`module.js`导入的。但是`z`不可能是因为没有出口。

因此，我们不再需要像下面这样的生活:

```
const module = (function() {
  const x = 1;
  const y = 2;
  const z = 3;
  return {
    x,
    y
  }
})();
```

上面的代码比较长，并且使用了一个不必要的函数。此外，随着函数的成员越来越多，它会变得越来越长，使用长函数并不是一个好主意。

对于回调，我们可以使用箭头函数来代替。例如，不写:

```
const arr = [1, 2, 3].map(function(a) {
  return a * 2;
})
```

我们应该写:

```
const arr = [1, 2, 3].map(a => a * 2)
```

它更短，所以我们不必键入太多，就像我们不必键入我们的`function`关键字一样。此外，对于单行箭头函数，返回是隐式的。我们也不必担心`this`和`arguments`的值，因为箭头函数不会绑定到这些值。

# 结论

要重构函数，我们应该将构造函数转换为类。否则，我们应该将它们转换成我们认为合适的块、模块和箭头函数。