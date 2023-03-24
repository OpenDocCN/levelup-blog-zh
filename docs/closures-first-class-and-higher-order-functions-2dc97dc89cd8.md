# 闭包、一级函数和高阶函数

> 原文：<https://levelup.gitconnected.com/closures-first-class-and-higher-order-functions-2dc97dc89cd8>

![](img/172b15c8d7633dae07dd0735009f66ae.png)

照片由 [Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

程序员使用他们的行话，这很好，因为这使得交流不那么模糊。但是有些短语听起来可能比实际更复杂。当我第一次听说闭包、一级函数和高阶函数时，我认为它们是复杂的概念，如果我问它们是什么，我害怕看起来很愚蠢。事实证明，它们并不像我想象的那样复杂，我将用这篇文章来解释它们是什么。

# 一级功能

一级函数比你想象的要简单得多，JavaScript 也有一级函数。这意味着函数和其他变量一样被对待。可以在其他函数中作为参数使用，函数可以返回函数，函数可以保存在变量中。

```
// Saving a function in a variable
const myFirstFunc = () => 10// Passing a function as an argument
const mySecondFunc = (funcParam) => { 
  return console.log(funcParam())
}// Returning a function from a function
const myThirdFunc = () => {
  return () => console.log('Hello from returned function')
}
```

# 高阶函数

一旦你理解了一级函数，你就已经看到了一个高阶函数的例子。高阶函数是以函数为参数、返回函数或两者兼有的函数。

```
const myFirstHigherOrderFunc = () => {
 **return () => {
    const output = 'Returned from a higher order function'
    console.log(output)
  }**
}const mySecondHigherOrderFunc = (**funcParam**) => {
  **funcParam()**
}
```

# 什么时候用？

高阶函数和一级函数都是常用的，只是我们没有考虑太多，因为它是 JavaScript 的基础。例如，当向 HTML 元素添加事件监听器时。

```
const **clickHandler** = (event) => { 
    console.log(`${event.target.id} is clicked`)
}document.querySelector(‘#someId’)
  .**addEventListener(**'click', **clickHandler)**
```

# 关闭

闭包是我认为非常混乱的概念之一，主要是因为我不明白它为什么有用。如果你有同样的感觉，请稍等，我会试着解释一下。

闭包**将外部作用域保存在内部作用域**中。现在可能没什么好说的，一个常用的例子是计数器。

```
// Without closure
const **withoutClosure** = () => {
  let counter = 0 
  return ++counter
}console.log(**withoutClosure**()) // Prints: 1
console.log(**withoutClosure**()) // Prints: 1
console.log(**withoutClosure**()) // Prints: 1
```

函数`withoutClosure`是一个普通函数。每次执行函数时，变量 counter 将被声明并初始化为 0，然后递增到 1。变量只在函数执行期间存在，之后它将被垃圾收集并被移除。

现在让我们看一个使用闭包的类似函数。

```
// With closure
const **myClosure** = () => {
  let counter = 0
  return () => ++counter
}const **incrementCount** = **myClosure**()
console.log(**incrementCount**()) // Prints: 1
console.log(**incrementCount**()) // Prints: 2
console.log(**incrementCount**()) // Prints: 3
```

现在，变量递增到下一个数字。发生了什么事？

首先，我们声明一个名为`myClosure`的函数，这个函数声明一个名为`counter`的变量，并用值 0 初始化它。在`myClosure`内部，我们还声明了一个匿名函数，然后从`myClosure`返回。匿名函数在这里不被执行，只被返回。`myClosure`是高阶函数。

当`myClosure`被执行时，返回值被保存在一个名为`incrementCount`的变量中。如前所述，JavaScript 中的函数是一级函数，所以我们可以很容易地将它们保存在变量中。现在，当我们执行保存在`incrementCount`中的函数时，计数器按预期递增。这是一个奇怪而又非常有用的部分。

所以让我们退一步讲基本功能。一个函数可以到达它周围范围内的变量。这里没什么奇怪的。我相信你见过这样的代码:

```
let name = ‘Foo’const greeter = (name) =>{
  console.log(`Hello ${name}!`)
}greeter(name) //Prints: Hello Foo
```

这只是一个普通的函数到达它周围范围内的一个变量。

在闭包的情况下，我们返回的函数也可以到达在其周围作用域中声明的变量。当保存在`incrementCount`中的函数被执行时，它到达声明时包围它的作用域。即使我们作为程序员不能到达在`myClosure`中声明的变量`counter`，但是返回的函数可以。

那么这为什么有用呢？嗯，有多种用例，但最简单的一种是可以声明变量而不会混淆全局范围。该变量将只在需要的地方使用。这就是我们如何在 JavaScript 中创建类似私有变量的东西。

让我们看一下下面的代码

```
const **closure** = () => {
  let name = ‘name not set’
  return {
    **setName**: (*value*) => {name = value},
    **getName**: () => name
  }
}const objFromClosure = **closure**()console.log(objFromClosure.name) //Prints: undefined
console.log(objFromClosure.**getName**()) //Prints: name not set
console.log(objFromClosure.**setName**(‘Foo’))
console.log(objFromClosure.name) // Prints: undefined
console.log(objFromClosure.**getName**()) // Prints: Foo
```

*   闭包用于使`let name`变量私有。
*   第一个`console.log`打印 undefined，因为属性名不在范围内。
*   第二个`console.log`使用了一个叫做`getName`的方法，所以我们得到了默认的字符串“name not set ”,它是在闭包的外部作用域中设置的值。
*   然后我们将名称设置为 Foo。
*   没有 getters 和 setters，我们仍然无法获得属性名。
*   使用`getName`方法，新名称被打印到控制台。

# 概括起来

具有一级函数的语言将函数视为变量。高阶函数只是一个函数返回一个函数，或者以一个函数作为参数。

闭包用于将外部作用域保留在内部作用域中。这有助于避免全局范围内的变量变得杂乱，也有助于使变量看起来像是私有的。

这就是所有希望这篇文章给你一些东西。