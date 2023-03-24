# 从头开始作曲、吹笛子和咖喱

> 原文：<https://levelup.gitconnected.com/compose-pipe-and-curry-from-scratch-99b417608d57>

![](img/e311cb91e9443b3ad05fafb1bd884f58.png)

[https://unsplash.com/photos/ImcUkZ72oUs](https://unsplash.com/photos/ImcUkZ72oUs)

有很多文章解释什么是烹饪和作曲。甚至还有我写的[一个](https://medium.com/@rolandpeelen/function-composition-currying-in-real-life-e74c86302205)。

但是，虽然我觉得我的文章很全面，但我认为仍有一些解释的余地。当看着咖喱和组成，以及实际的实现，他们似乎很简洁，可能很难理解到底发生了什么。本文的目的是建立一些直觉，从头开始创建一个`compose`、`pipe`、**、**和`curry`函数。从最基本的版本开始，到最简洁/复杂但也最灵活的版本结束。

# 咖喱

> 让函数一次接受一个参数的过程。

让我们看一些代码。

```
// Uncurried
const add = (x, y) => x + y;
[1,2,3].map(x => add(x, 4)); // [5, 6, 7]// Curried
const add = x => y => x + y;
[1,2,3].map(add(4)); // [5, 6, 7]
```

如您所见，这有一些优点，不那么冗长。然而，以这种方式编写函数是令人讨厌的，并且不是特别灵活(相当极端的例子)；

```
// Curried
const add5args = a => b => c => d => e => a + b + c + d + e;
add5args(1)(2)(3)(4)(5); // 15// Possible uncurried variant
const add5args = (a,b,c,d,e) => a + b + c + d + e;
add5args(1, 2, 3, 4, 5); // 15
```

有一个函数会将我们的第二个变量转换成一个可以一次接受一个变量的变量，所以我们可以在例如`Array.map`中使用它。

# 合成和管道功能(0 级)

> 将多个函数链接在一起以创建一个新函数过程。

让我们看看这是如何工作的一些代码。

```
const add5 = x => x + 5;
const multiply6 = x => x * 6;const multiply6Add5 = compose(add, multiply);multiply6Add5(2); // 17 😎
```

所以从`add5`和`multiply6`中，我们创建了一个新的函数，它是两者的**组合**。给了我们一个既能乘以 6 又能加上 5 的新函数。但是为什么这个是反的呢？

函数合成的概念来源于数学。在加、乘、除之类的东西旁边，有一个`composition`操作符。它是一个点，上面有一个洞。

```
(f ° g)(x) = f(g(x));
```

这样读的话就是 x 的 g 后的**f**和取`x`，应用到`g`，再应用到`f`是一样的。我们在这里看到的是，合成以相反的顺序进行。第二个功能(`g`)在第一个功能之前应用。这也是函数组合在编程中落后的原因。在代码中，我们引入了`pipe`函数的概念，该函数反转应用程序，使其成为我们直觉认为的样子。

因此，让我们编写自己的组合和管道函数。

```
// Uncurried
const composeU = (f, g, x) => f(g(x));
const pipeU = (f, g, x) => g(f(x));// Curried
const compose = f => g => x => f(g(x));
const pipe = f => g => x => g(f(x));// Or reverse arguments for the pipe function
const pipe = f => g => x => compose(g)(f)(x);
```

如你所见，这与数学符号非常相似。我用一个`U`在后面固定了未绑定的函数，这样我们以后可以区分它们。让我们试用一下。

```
const add5 = x => x + 5;
const multiply6 = x => x * 6;// Uncurried
composeU(add, multiply, 2); // 17
pipeU(add, multiply, 2); // 42// Curried
const multiply6Add5 = compose(add)(multiply);
const add5Multiply6 = pipe(add)(multiply);multiply6Add5(2); // 17
add5Multiply6(2); // 42[1,2,3].map(multiply6Add5) // [11, 17, 23]
```

有时候，能够将[的两个函数部分应用到](https://en.wikipedia.org/wiki/Partial_application#:~:text=In%20computer%20science%2C%20partial%20application,producing%20a%20function%20of%20type%20.)的作品中真的很好，这样以后就可以在`map`中使用它们了。然而，有时候，能够在它们的未受影响的变体中使用它们也是很好的。理想情况下，我们希望拥有两个世界的优点，但是我们不想写两次函数。所以我们来做个帮手吧。

# 咖喱功能(0 级)

我们将直接进入我们的基本咖喱功能。

```
const curry = fn => x => y => fn(x, y);const addU = (x, y) => x + y;
const add = curry(addU);addU(4, 2); // 6
add(4)(2); // 6const add4 = add(4);
add4(10) // 14
```

我们的 curry 函数只是接受另一个函数，然后接受一些参数，并将它们应用到参数中。所以现在我们只需要定义我们的 addU 函数，然后我们可以根据另一个函数来定义我们的 curried 版本。不错！

## 但是等等…

如果我们想要组合两个以上的函数呢？还是库里一个有三个参数的函数？

# 撰写功能(第 1 级)

再补充几个版本吧。

```
const compose = f => g => x => f(g(x));
const compose3 = f => g => h => x => f(g(h(x)));
const compose4 = f => g => h => i => x => f(g(h(i(x))));
// …
```

虽然这可行，但并不理想。让我们使用 [rest 运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameter)。

```
const composeU = (...fns) => x => {
 let val = x;
   for (const fn of fns.reverse()) {
     val = fn(val)
   }
 return val;
};const add3 = x => x + 3;
const add4 = x => x + 4;
const add5 = x => x + 5;const add12 = compose(add3, add4, add5);add12(2); // 14 😎
```

所以这太棒了！我们取我们的函数，以相反的顺序一个接一个地检查它们，并将函数应用到我们想要使用的值上。但是，我们的函数是非常必要的，我们在做任何操作之前都要反转整个数组…也许我们可以做些什么。从概念上讲，我们接受一个类似数组的东西，一个接一个地遍历它，把它简化成一个值。嗯……`reduce`……

```
const compose = (...fns) => x => fns.reduceRight((acc, fn) => fn(acc), x);
```

那更好。我们从右到左减少数组，将每个函数应用到累加器，从输入值开始。但是现在有一个问题…

```
const composeU = (...fns) => x => fns.reduceRight((acc, fn) => fn(acc), x);
const compose = curry(composeU);const add3 = x => x + 3;
const add4 = x => x + 4;
const add5 = x => x + 5;const add12 = compose(add3, add4, add5);add12(2); // [Function] 😥
```

似乎我们的咖喱功能坏了，我们不能使用它与组成…

# 咖喱功能(1 级)

再补充几个版本吧。

```
const curry = fn => x => y => fn(x, y);
const curry3 = fn => x => y => z => fn(x, y, z);
const curry4 = fn => x => y => => z => a => fn(x, y, z, a);
// …
```

虽然这也可行，但也许我们也可以用 rest 参数做些什么…

```
const curry = fn => {
 return function curried (...args) {
   if(args.length >= fn.length) {
     return fn.apply(null, args);
   }
   return curried.bind(null, ...args);
 };
};
```

这到底是怎么回事？我们的 curry 函数需要一个函数。检查…但是然后…从概念上来说，即使它不是这样实现的，也要把它想象成一个缓冲区。我们的`…args`是根据它们的`length`(我们已经提供的数量)来检查的，如果我们已经达到了我们需要的数量(我们可以通过检查`fn`的参数数量—或者 [arity](https://en.wikipedia.org/wiki/Arity#:~:text=Arity%20(%2F%CB%88%C3%A6r%C9%AAt,also%20called%20adicity%20and%20degree.) —来检查)，那么我们将所有的参数应用到最初提供的函数(`fn`)并返回它。如果没有，返回内部函数，但是将我们已经收到的参数绑定到它(准备接收更多)。

换句话说，递归地返回`curried`，直到我们遇到最初提供的函数的 arity。

这里有一个漂亮简洁的 es6 等价物。

```
const curry = fn => {
 const curried = (...args) =>
   args.length >= fn.length
     ? fn.apply(null, args)
     : curried.bind(null, ...args);
 return curried;
};
```

让我们用之前的`add5args`函数来看看它的作用。

```
const add5argsU = (a, b, c, d, e) => a + b + c + d + e;
const add5args = curry(add5argsU);const a = add5args(1); // [Function] — 4 arguments left
const b = a(2); // [Function] — 3 arguments left
const c = b(3); // [Function] — 2 arguments left
const d = c(4); // [Function] — 1 arguments lefta(2,3,4,5) // 15
b(3,4,5) // 15
c(4,5) // 15
d(5); // 15
```

太棒了，我们现在可以一次提供所有的参数，或者一半，或者只有一个，并继续添加，直到我们有了完整的函数。不错！让我们看看是否可以在我们的 compose 函数中使用它。

```
// From earlier
const composeU = (...fns) => x => fns.reduceRight((acc, fn) => fn(acc), x);
const compose = curry(composeU);const add1 = x => x + 1;
const add2 = x => x + 2;
const add3 = x => x + 3;
const add4 = x => x + 4;const add5 = compose(add2, add3);
const add7 = compose(add3, add4);
const add11 = compose(add4, add4, add3);add5(4); // 9
add7(4); // 11
add11(4); // 15const add12 = compose(add5, add7); // Already composed functions 😎
add12(4); // 16
```

不错！无论我们提供多少参数，我们都可以按照自己喜欢的方式组合函数，并在以后使用它们。我们甚至可以用另一篇作文来定义我们的作文。

# 管道功能(1 级)

我们太兴奋了，完全忘记了我们的`pipe`功能。这很容易定义。

```
const composeU = (...fns) => x => fns.reduceRight((acc, fn) => fn(acc), x);
const pipeU = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x);
const compose = curry(composeU);
const pipe = curry(pipeU);
```

很好。对吗？嗯，anal-retentive-me 有点恼火，因为我们实际上在管道函数中重复了我们的 compose 函数，但只是参数颠倒了。也许我们可以根据彼此来定义它们。我们应该能够简单地反转函数的参数，并且做得很好。对吗？

```
const composeU = (...fns) => x => fns.reduceRight((acc, fn) => fn(acc), x);
const pipeU = (...fns) => x => composeU(...fns.reverse())(x)
const compose = curry(composeU);
const pipe = curry(pipeU);
```

好多了。我们也接近于`pipeU`拥有如此少的语法，我们可以开始省略它的一些测试…
但不是现在。我认为我们可以走得更远。

# 奖金水平

还有一件事很烦。我们有这个漂亮的 curry 函数，当我们用它把我们的`composeU`变成一个 curry 函数时，它已经以单独接受‘数据’参数的方式被 curry 化了。如果我们能让它直接变成 curried，或者立刻接受它的所有参数，那就太好了，这样我们至少感觉到我们的`curry`函数做了一些好事。或许我们可以试试:

```
const composeU = (...fns, x) => fns.reduceRight((acc, fn) => fn(acc), x);
```

但是我们不能…

> 语法错误:Rest 参数必须是最后一个形参。那是令人失望的。

然而。这还没完…如果我们阅读数组的归约函数的[文档，我们会看到以下内容:](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)

> initialValue **可选**
> 一个值，用作回调的第一次调用的第一个参数。如果未提供 initialValue，数组中的第一个元素将用作初始累加器值，并作为 currentValue 跳过。在没有 initialValue 的空数组上调用 reduce()将引发 TypeError。

啊哈。所以是**可选**。如果我们不提供参数，它会自动从数组中取出第**个**项。乍一看，这似乎对我们没有帮助，因为我们提供的参数是我们想要使用的 **last** ，所以它将是数组中的最后一个。但是，仔细研究一下，因为我们是从右边开始减少的，所以当我们到达那里时，它实际上是第一个元素！

所以我们不能把`x`放在第二个位置。但是我们可以完全忽略它。甚至更好！

```
const compose = (...fns) => fns.reduceRight((acc, fn) => fn(acc));
const pipe = (...fns) => compose(...fns.reverse());
```

很好…那正合我意。现在我们的函数有了一个可变的 arity，我们就不用去修饰它了！

# 结束的

因此，我们已经经历了几次`compose`、`pipe`和`curry` 函数的迭代。从最简单的变体开始，建立一些直觉，以非常灵活的抽象变体结束。

```
const compose = (...fns) => fns.reduceRight((acc, fn) => fn(acc));
const pipe = (...fns) => compose(...fns.reverse());const curry = fn => {
 const curried = (...args) =>
   args.length >= fn.length
     ? fn.apply(null, args)
     : curried.bind(null, ...args);
 return curried;
};
```