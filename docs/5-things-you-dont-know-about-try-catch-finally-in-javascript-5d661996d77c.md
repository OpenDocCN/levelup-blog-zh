# 关于 JavaScript 中的 try-catch-finally，你不知道的 5 件事

> 原文：<https://levelup.gitconnected.com/5-things-you-dont-know-about-try-catch-finally-in-javascript-5d661996d77c>

## 了解如何在 JavaScript 中执行 try-catch-finally

![](img/a3a47954ef4ebd077f906b480095bf57.png)

基思·约翰斯顿在 [Unsplash](https://unsplash.com/s/photos/try-catch?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

try-catch-finally 用于处理运行时错误，防止它们停止程序的执行。

[](/everything-you-need-to-know-about-error-handling-in-javascript-caa6c066274d) [## 关于 Javascript 中的错误处理，您需要知道的一切

### 了解如何在 Javascript 中处理异常。

levelup.gitconnected.com](/everything-you-need-to-know-about-error-handling-in-javascript-caa6c066274d) 

# 1.try 或 catch 块中的 Return 语句

如果我们有一个`finally`块，那么`try`和`catch`块中的`return`语句不会被执行。它总是会撞上`finally`的阻碍。

```
function test() {try {return 10;} **finally** {console.log("finally");return 1;
   }}console.log( test() ); // finally 1
```

示例 2:

```
function test() {try {     
    return 10;
    throw "error"; // this is not executed, control goes to finally
  } **catch** {
    console.log("**catch**"); 
    return 1;
  } finally { 
    console.log("finally");
    return 1000;
  }}console.log( test() ); // finally 1000
```

# 2.try 块中声明的变量在 catch 或 finally 块中不可用

如果我们使用`let`或`const`在`try`块中声明一个变量，它将不能被`catch`或`finally`使用。这是因为这些变量声明是块范围的。

```
try {

    let a = 10; throw "a is block scoped ";} catch(e) {

    console.log("Reached catch"); console.log(a); // Reference a is no defined}
```

但是如果我们使用`var`而不是`let`或`const`，那么它将在`catch`内部可用，因为`var`是函数作用域，声明将被提升。

```
try {

    var a = 10; throw "a is function scoped ";} catch(e) {

    console.log("Reached catch"); console.log(a); // 10}
```

# 3.无错误详细信息的捕获

在 ES2019 中，`catch`块的参数是可选的。

```
try {
   // code with bug;} catch { // catch without exception argument}
```

# 4.try…catch 在`setTimeOut`上不起作用

如果一个异常发生在“预定的”代码中，比如`setTimeout`，那么`try..catch`不会捕捉到它。

```
function callback() { // error code}function test() { try {
      setTimeout( callback , 1000);
   } catch (e) {
      console.log( "not executed" );
   }}
```

为了处理这个问题，我们需要在`setTimeOut`回调中添加`try…catch`:

```
function callback() { try {
     // error code 
   }catch
     console.log("Error caught") ;
   }}function test() { **setTimeout(callback, 1000);**}
```

# 5.添加全局错误处理程序

我们可以注册一个`window.onerror`事件监听器，它将在出现未被捕获的错误时运行。这不会处理错误，但会检测到错误——您需要在其中提供自己的`try…catch`。

```
window.onerror = function(e) { console.log("error handled", e);}function funcWithError() { **a; // a is not defined**}function test() { **funcWithError();** **console.log("hi"); // this will not be executed**}test(); 
```

跟随 Javascript Jeep🚙💨。

[](/learn-bigint-in-javascript-df9b61bc19ef) [## 学习 JavaScript 中的 BigInt

### 了解如何在 JavaScript 中使用 BigInt 存储大数。

levelup.gitconnected.com](/learn-bigint-in-javascript-df9b61bc19ef) [](https://medium.com/javascript-in-plain-english/understand-the-superpower-of-optional-chaining-in-javascript-fbc569244471) [## 理解 JavaScript 中可选链接的强大功能

### 了解如何使用即将推出的 JavaScript 特性可选链接

medium.com](https://medium.com/javascript-in-plain-english/understand-the-superpower-of-optional-chaining-in-javascript-fbc569244471)