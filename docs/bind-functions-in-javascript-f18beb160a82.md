# 理解 Javascript 中的 bind()函数

> 原文：<https://levelup.gitconnected.com/bind-functions-in-javascript-f18beb160a82>

![](img/afc8ea12583517396eae7c7d58da44aa.png)

`.bind()` 功能允许 as 通过设置自定义的`this` 值来创建功能。`bind` 函数返回一个像普通函数一样可调用的函数，也可以传递给其他函数。

> 当我们需要捆绑的时候。

假设我们有一个对象:

```
window.Name  = "Window";var JsJeep = { Name  : "Javascript Jeep 🚙 🚗", greet : function() {

          console.log(" 👋  from " + this.Name); }}
```

当我们用`JsJeep`对象调用函数`greet`时:

```
JsJeep.greet(); //  👋from Javascript Jeep 🚙 🚗
```

如果我们将函数存储在另一个变量中，并调用函数:

```
var greet = JsJeep.greet;greet(); //  👋 from Window.
```

`this`指向`"Window"`是因为 greet 函数里面的`this`是`global window object`，所以我们需要告诉浏览器具体使用它应该引用的`JsJeep`对象。这就是使用`bind`的时候。

我们再看一个例子，然后我们就明白了`bind`。当我们在 a `setTimeout`中做同样的事情时:

```
setTimeout(JsJeep.greet , 0); //  👋 **from Window**
```

在`setTimeout`函数内部，`this`的值将再次为`window`，因为我们正在将`greet`函数的引用传递给`setTimeout`，并且窗口对象正在执行`0 milliseconds`之后的 greet 函数调用。

上面的`setTimeout`代码相当于

```
var func = JsJeep.greet;setTimeout(func, 0); // which is similar to above case.
```

为了解决上述问题，我们使用了`bind`函数，它允许我们在函数中指定`this`的值。

## 句法

`functionToBind.**bind(**ourCustomThisObject, argumentsToFunction**)**`**→****附加参数可选。**

**上述问题的解决方案:**

```
var fun = JsJeep.greet;var newFun = fun.bind(JsJeep);**newFun**(); //  👋 from Javascript Jeep 🚙 🚗**setTimeout(newFun, 0); // ** 👋from Javascript Jeep 🚙 🚗
```

**所以我们解决了这个问题。**

**让我们看看一些棘手的领域。**

> **1.当我们通过`null`而不是`JsJeep`对象时会发生什么**

```
 var fun = JsJeep.greet; var newFun = fun.bind(null); // then we are binding nothing
```

*   **有两种情况，如果我们使用严格模式，那么`this`将是`null`。**
*   **如果我们处于非严格模式，那么`this`将是`window`对象。**

> **2.一旦`bind`完成，我们就不能改变`this`的值。**

```
var fun = JsJeep.greet;var bindFun= fun.bind( {Name: "Balaji"} );

bindFun = bindFun.bind( {name: "Raju"} );bindFun(); //  👋 from Balaji
```

**原因是在第一次调用后，`bindFun`已经修复了`this`,并且我们不能在之后改变它。**

> **3.如何判断一个函数是否是有界函数？**

**绑定函数的 name 属性以“bound”为前缀**

```
log(**JsJeep.greet.name**) // **greet**var fun = JsJeep.greet;var bindFun= fun.bind( JsJeep );log(**bindFun.name**); // **bound greet**
```

> **4.如果对象的一个属性被更新，那么该属性的值也会在绑定函数中被更新。**

```
var JsJeep = { Name  : "Javascript Jeep 🚙 🚗", greet : function() {

          console.log(" 👋  from " + this.Name); }}var fun = JsJeep.greet;var bindFun= fun.bind( JsJeep );**JsJeep.Name = "New Jeep 🚘 "; //updating the property.** bindFun(); // **  👋  from New Jeep 🚘**
```

> **笔记**

**为✍️写这篇文章，我🤷🏻‍♂️参考了`[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind)`和`[javascrip.info](https://javascript.info/bind)`以及`StackOverflow`的一些回答，谢谢🙏对他们来说🌟 💖 😜。**

**— — — — — — — — — — — — — — — — — — — — —**

****在这里** 学习创建随机数的新方法[](http:// https://link.medium.com/rXCCnb6BJY)**

****— — — — — — — — — — — — — — — — — — — — —****

****如果你发现这个有用的惊喜🎁我这里的[](https://www.paypal.me/jagathishSaravanan?source=post_page---------------------------)****。********

******开心就分享😃 😆 🙂。******

******跟随** [**Javascript 吉普🚙**](https://medium.com/u/f9ffc26e7e69?source=post_page---------------------------) **如果你觉得值得。******

****[](https://gitconnected.com/learn/javascript) [## 学习 JavaScript -最佳 JavaScript 教程(2019) | gitconnected

### JavaScript 是世界上最流行的编程语言之一——它随处可见。JavaScript 是一种…

gitconnected.com](https://gitconnected.com/learn/javascript)****