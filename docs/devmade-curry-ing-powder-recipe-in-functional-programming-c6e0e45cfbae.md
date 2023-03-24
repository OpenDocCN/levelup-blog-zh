# 函数式编程:在 TypeScript 中运行

> 原文：<https://levelup.gitconnected.com/devmade-curry-ing-powder-recipe-in-functional-programming-c6e0e45cfbae>

![](img/ccf87e8dc00e52fa154d50e42b679395.png)![](img/430c2ae94b36b84233bf97b18468c020.png)![](img/0552156f224b2554d388ae83fdd1cf2d.png)

将一个给定了多个参数的函数转换成一系列函数，每个函数需要一个参数

## 用函数式编程开发咖喱粉配方

## 什么是 curry 和 curry 函数，如何在 **Java、JavaScript 库、React.js、React Native 中转换为 curry 和 curry 函数，为什么要使用它**

成分:

*   **愿意理解 currying】在函数式编程中，**
*   任何语言，如 JavaScript、Java、…，
*   **以固定 arity 作为输入的正常函数**。

# 我在掩盖什么

在**动机**部分，我已经提到了一些原因来回答**为什么**使用**驱动**和**驱动功能**，

接下来用**遍历**部分中的一个例子，我将解释**如何**将一个普通函数转换成一个简化函数(这非常简单，不需要特别了解 JavaScript)，

最后，在**其他演示**部分，我演示了这种转换和在其他一些语言中的使用，如 **Java** 和 **JavaScript，如 Lodash、Folktale、Ramda 和 Sanctuary、React.js 和 React Native** ，以及一些众所周知的事实，如部分应用、解弯曲和同构。

# TL；速度三角形定位法(dead reckoning)

**定义:** Currying 是通过固定的 arity(给定参数的数量)将一个函数转换为一个嵌套返回函数序列的函数，每个函数一次接受其中一个参数。

func([1..n]) -> func[1..n](1)

**代码:**如果我们有这个功能:

```
const soakIn = (a: string, b: string) => b + " " + a;
soakIn("soaked in BBQ sauce", "beef"); // beef soaked in BBQ sauce
```

我们可以把它形成一个固定的功能:

```
const soakIn = (a: string) => (b: string) => b + " " + a;
soakIn("soaked in BBQ sauce")("beef"); // beef soaked in BBQ sauce
```

简而言之，这就是 currying 所做的，但是更多的细节在👉**走查**部分。

> 我的一个叫 [Soheyl](https://twitter.com/_Sohayl) 的朋友相信烧烤酱让一切都变得完美！(真实故事)*😀*
> 
> *他可以通过* `const soakedInBBQ = soakIn("soaked in BBQ sauce");` *创建一个特定的函数，并通过他想要的每一个参数调用新的函数，就像* `soakedInBBQ("chicken"); // chicken soaked in BBQ sauce`一样

# 动机

**为什么在函数式编程中使用 curried 函数很重要？**

**以下是 curried functions 给你的一些关键优势:**

*如果他们中的一些人首先不清楚，请尝试在* ***演练*** *或* ***已知事实*** *章节*中了解他们中其他部分的组成部分

## **利用依赖注入进行测试**

例如，如果您有一个函数使用了 API 这样的副作用，您可以通过 curry 并期望 API 作为 curry 函数中的一个参数来使该函数抽象。这样你可以通过模仿 API 来测试你的 curried 函数。

之前:

```
import * as api from 'whatever';function saveData(payload: any) {  
  return api.save('Route', payload);
}
```

之后:

```
import * as api from 'whatever';
import * as R from 'ramda';function _saveData(apiDep: any, payload: any) {  
  return apiDep.save('Route', payload);
}export const saveData = R.curry(_saveData)(api);
```

简化版，灵感源自这篇中型文章:

[medium.com/@curtistatewilkinson](https://medium.com/@curtistatewilkinson/dependency-injection-currying-and-partial-application-for-easy-unit-tests-ded40c39016c)

## **简易专用功能**

你可以更早地将一些输入硬编码成函数(从一个简化的函数开始)，这个**可以让你灵活地使用不同的专用函数**来满足你的需求。因此，它**使代码更加简洁明了**(例如，在高阶函数上使用特殊函数时):

```
const sum = (a: number, b: number) => a + b;
const curriedSum = _.curry(sum);
```

之前:

```
[1, 2, 3].map((x: number) => curriedSum(2)(x));
```

之后:

```
const addedBy2 = curriedSum(2);

[1, 2, 3].map(addedBy2);
```

## **通过整体储存和新鲜研磨最大限度地增加香料风味**😀

制造专门的功能并使用它，就像研磨某种东西以添加到其他需要时研磨过的成分中(某种)

![](img/58db3df44f170c71f0bbb0ee74eb3802.png)

> 香料的味道部分来源于暴露在空气中时会氧化或蒸发的化合物(挥发性油)。研磨香料会大大增加其表面积，从而提高氧化和蒸发的速度。因此，通过储存整个香料并在需要时研磨，风味被最大化。一整支干香料的保质期大致是两年；大概六个月的时间。磨碎的香料的“风味寿命”会短得多。磨碎的香料最好避光保存。
> 
> [https://en.wikipedia.org/wiki/Spice](https://en.wikipedia.org/wiki/Spice)

## **它帮助我们创建抽象函数，并带来了许多类似于面向对象程序设计中抽象的优点，**

## **它让我们更灵活地将函数组合成高阶函数，…**

# 走查

让我们来看看这个正常的函数:

```
function grind(a: string, b: string, c: string, d: string) {
  return (a + b + c + d).shuffle();
}
```

这个函数将获得咖喱粉的 4 种成分，将它们连接在一起，然后混洗(粉碎)它们，并返回一个混洗的字符串(混合物)。

来洗牌(为什么？这里不重要，串联就够了，可以跳过`.shuffle()`部分)我用的是 StackOverflow 的[这个答案](https://stackoverflow.com/a/3943985/9470990)作者 *Andy E* 。这只是一个打乱或混淆字符串的函数。

```
String.prototype.shuffle = function () {
  var a = this.split(""),
    n = a.length;

  for(var i = n - 1; i > 0; i--) {
    var j = Math.floor(Math.random() * (i + 1));
    var tmp = a[i];
    a[i] = a[j];
    a[j] = tmp;
  }
  return a.join("");
}
```

这里我们传递 4 种香料:

```
grind("Chillies ", "Ginger ", "Turmeric ", "MellB!"); // I love currying!
```

`Mell B`？Nop！这并没有发生！实际上这是函数调用:

```
grind("Chillies", "Ginger", "Turmeric", "Coriander"); // dGorslgrcianhrireeernTliCiiuemC
```

是啊！这是地面输出！

让我们把研磨函数转换成一个`curried`函数:

```
function grind(a: string) {
  return (b: string) => {
    return (c: string) => {
      return (d: string) => {
        return (a + b + c + d).shuffle()
      }
    }
  }
}
grind("Chillies")("Ginger")("Turmeric")("Coriander") // dGorslgrcianhrireeernTliCiiuemC
```

如你所见，我们现在这样调用研磨函数👆有多个函数调用。每个调用都返回一个新的包装器(像函数(b))需要用一个参数来实现。

```
const groundByChillies = grind("Chillies");
const groundByChilliesAndGinger = groundByChillies("Ginger");
const groundByChilliesAndGingerAndTurmeric = groundByChilliesAndGinger("Turmeric");
const result = groundByChilliesAndGingerAndTurmeric("Coriander"); 
log(result); // dGorslgrcianhrireeernTliCiiuemC
```

通过调用第一个参数:

```
grind("Chillies");
```

该函数返回:

```
return (b: string) => {
  return (c: string) => {
    return (d: string) => {
      return (a + b + c + d).shuffle()
    }
  }
}
```

现在，通过传递第二个参数:

```
groundByChillies("Ginger");
```

该函数将被返回:

```
return (c: string) => {
  return (d: string) => {
    return (a + b + c + d).shuffle()
  }
}
```

这将持续到最后一次调用，并且通过每个参数，将保持对该词法环境(参数)的闭包(每个函数都可以访问其父函数的范围)，这意味着它具有先前传递的其他参数，最后，它返回结果。

```
log(result); // dGorslgrcianhrireeernTliCiiuemC
```

# 其他演示

如果您对其他语言(如 Java)的实现以及 React.js 和 React Native 等框架或库不感兴趣，您可以直接跳到众所周知的事实。

# Java 语言(一种计算机语言，尤用于创建网站)

在 Java 中，如果你有这个函数:

```
public static int **sum(**int a**,** int b**)** **{** return a **+** b**;
}**
```

你可以把它变成一个咖喱功能

```
public static Function**<**Integer**,** Function**<**Integer**,** Integer**>>** **curriedSum()** **{** return x **->** y **->** x **+** y**;
}**
```

调用第一个 sum 函数:

```
sum**(**1**,** 2**);** *// gives 3*
```

把咖喱称为:

```
curriedSum**();** *// returns a function* curriedSum**().**apply**(**1**);** *// returns a function* curriedSum**().**apply**(**1**).**apply**(2)** *// returns 3*
```

# **React.js 和 React Native**

除了 input 和 div 元素(事件处理程序在这里很重要，这是相同的)，React.js 和 React Native 之间的所有内容都是相同的。

## **反应和还原**

react-redux `connect()`函数是一个定制函数的例子。

```
export default connect(mapStateToProps)(MyComponent)
```

## 事件处理

React 上的事件处理程序已经像 curried 一样被处理了，所以在`handleChange`的定义中，你可以使用传递的事件参数。

```
const handleChange = (fieldName: string) => (event: any) => {
  saveField(fieldName, event.target.value)
}
<input type="text" onChange={() => handleChange('username')} />
```

React 在后台这样调用事件处理程序:`(event) => handleChange(param)(event)`。

我们可以更进一步。如果我们想将这个处理程序传递给不同的输入字段组件，我们可以将`saveField`和`fieldName`硬编码成`handleChange`化的函数。这样，即使`saveField`功能已经与`handleChange`合并，它也保持了代码的整洁。我使用钩子‘T5’来实现`saveField`,所以`handleChange`被重构为`handleState`,而`fieldName`被重构为状态`username`。

```
import React, { useState } from 'react';function Username({handleState}: {handleState: any}) {
  return (
    <input type="text" onChange={handleState} />
  );
}function Main() {
  const [username, setUsername] = useState('');   const handleState = (setState: any) => (state: string) => (event: any) => {
    setState(event.target.value);
  }; const setUsernameState = handleState(setUsername)(username);  return (
    <div>
      <Username handleState={setUsernameState} />
    </div>
  );
}
```

# 众所周知的事实

## **使用任意 arity 的函数**

以下是能够用多参数调用的 curry 实现(这意味着 curry 函数可以被称为普通或完全 curry):

```
function curry(func: Function) {
  return function curried(...args: any[]) {
    if (args.length >= func.length) {
      return func.apply(this, args);
    } else {
      return function(...args2: any[]) {
        return curried.apply(this, args.concat(args2));
      }
    }
  };
}function sum(a: number, b: number, c: number) {
  return a + b + c;
}let curriedSum = curry(sum);alert( curriedSum(1, 2, 3) ); // 6, still callable normally
alert( curriedSum(1)(2,3) ); // 6, currying of 1st arg
alert( curriedSum(1)(2)(3) ); // 6, full currying
```

[javascript.info](https://javascript.info/currying-partials#advanced-curry-implementation)

## **解开绳索**

uncurry 函数与 curry 函数正好相反，它接受一个 curried 函数并返回一个普通的 uncurried 函数。

```
function uncurry(fn: Function) {
  return function(a: any, b: any) {
    return fn(a)(b);
  }
}
```

这是为了不携带任何 arity 函数:

```
function uncurry(fn: Function) {
  return function(...args: any[]) {
    return args.reduce((fn: Function, arg: any) => fn(arg), fn);
  }
}
```

例如，如果你认为 curriedSum 是一个 curried，并不担心:

```
const curriedSum = (a: number) => {
    return (b: number) => {
        return a + b;
    }
}const uncurriedSum = uncurry(curriedSum);
uncurriedSum(1, 2); // 3
```

uncurriedSum 相当于一个普通的 uncurried 函数，如下所示:

```
const sum = (a: number, b: number) => return a + b;
```

## **同构**

如定义所说 *isos 的意思是*“相等”， *morphe* 相当于“形式”或“形状”。curry 和 uncurry 互为反义词，某一特定功能的 curry 和 un curry 等价于同一个特定功能。

```
function curry<X, Y, R>(fn : **(X, Y) => R**) : **X => Y => R** {
  ...
};

function uncurry<X, Y, R>(fn : **X => Y => R**) : **(X, Y) => R** {
  ...
};
```

`(X, Y) => R`和`X => Y => R`是由`curry`和`uncurry`函数同构的**。这种关系被称为一个**同构。****

## **性能成本**

当我们处理函数时，我们创建了包装到其他函数的函数。在高阶函数中，无论是组合它们还是不使用 curried 函数，都会导致创建数百或数千个额外的函数，这会导致明显的性能开销，因此最好在需要时使用 curried 函数。

## **部分应用**

部分应用与不同的 curry 函数相关，并且可以通过固定(硬编码)函数的一些自变量并减少该数目来获得。

如果我们有一个像`sum`一样的普通函数:

```
function sum(a: number, b: number, c: number) {
  return a + b + c;
}function sum2(b: number, c: number) {
  return sum(2, b, c)
}
```

`sum2`是部分功能应用。在 JavaScript 中，这种修复也可以通过内置的绑定函数来实现:

```
*function*.bind(*thisValue*, [*arg1*], [*arg2*], ...)const sum2 = sum.bind(null, 2)
```

## **JavaScript 库中的库里**

在 JavaScript 中，可以从 Lodash、Folktale、Ramda 和 Sanctuary 等库中使用 Curry。签名是一样的，除了我在这里提到的几个:

在民间故事中，你可以这样称呼它:`curry(arity, fn)`，

Sanctuary 有 curry2、curry3、curry4 和 curry5 函数。

这是洛达什的一个例子:

```
import * as _ from 'lodash'; function sum(a: number, b: number) {
  return a + b;
}

let curriedSum = _.curry(sum);
```

# 夏天似的

如果您将一个提供固定数量参数的普通函数转换为一个嵌套返回函数序列，每个函数每次接受其中一个参数，那么您可以从 curried 函数中受益。

如果我们有这个函数:

```
function grind(a: string, b: string, c: string, d: string) {
  return (a + b + c + d).shuffle();
}
```

`*shuffle*` *在前面的* ***走查*** *章节*中有定义

我们可以把它形成一个`curried`函数:

```
function grind(a: string) {
  return (b: string) => {
    return (c: string) => {
      return (d: string) => {
        return (a + b + c + d).shuffle()
      }
    }
  }
}
```

可以这样称呼:

```
grind("Chillies")("Ginger")("Turmeric")("Coriander") // dGorslgrcianhrireeernTliCiiuemC
```

此外，我们可以通过删除一些参数并对其他参数进行硬编码来实现一个专门化的函数:

```
const groundByFirst3 = grind("Chillies")("Ginger")("Turmeric");
groundByFirst3("Coriander"); // dGorslgrcianhrireeernTliCiiuemC
```

通过使用定制函数，你可以获得一些好处，比如通过创建一个像`groundByFirst3`这样的专用函数来增加灵活性，或者定制函数可以在高阶函数中组合。

# 进一步阅读

如果你对函数式编程感兴趣，并且希望**让你的代码更可预测、更安全、性能更好**，我有另一篇关于函数式编程中[不变性的文章](https://medium.com/@zareanmasoud/what-is-immutability-in-functional-programming-6b307ee8641)。