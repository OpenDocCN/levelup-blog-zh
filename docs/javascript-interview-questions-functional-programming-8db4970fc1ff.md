# JavaScript 面试问题—函数式编程

> 原文：<https://levelup.gitconnected.com/javascript-interview-questions-functional-programming-8db4970fc1ff>

![](img/c90fdaa898ed75052fd70c02e4fc5962.png)

塞巴斯蒂安·赫尔曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了得到一份前端开发人员的工作，我们需要搞定编码面试。

在本文中，我们将探讨 JavaScript 的一些函数式编程问题。

# 什么是函数式编程？是什么让 JavaScript 成为函数式语言？

函数式编程是一种编程范式，它规定了我们构建应用程序的方式。函数式编程将程序中的函数视为数学函数。

这意味着我们避免改变状态和创建可变数据。这也意味着函数是纯粹的，即在给定相同输入的情况下，函数返回相同的结果。

函数在函数式编程中也不应该有副作用，因为它会使函数不纯。

JavaScript 函数也是一级函数。这意味着我们可以有函数作为参数或者函数返回函数。

它还支持闭包，我们返回的函数可以使用父函数中的一些值。

例如，JavaScript 数组有`map`、`filter`和`reduce`方法，它们接受在处理数组中的项目时调用的回调函数。

`map`、`filter`和`reduce`是高阶函数，因为它们接受函数作为参数。返回函数的函数也是高阶函数。

例如，`map`方法可以如下使用:

```
const nums = [1,2,3];
const doubleNums = nums.map(x => x*2);
```

在上面的代码中，我们将函数`x => x*2`传递给了`map`。

`x => x*2`对每个条目执行代码指定的计算，然后返回一个包含新值的新数组。然后我们得到`doubleNum`就是`[2, 4, 6]`。

# 什么是高阶函数？

高阶函数是以函数为自变量或返回函数的函数。

例如，如果我们有:

```
const hoc = (fn)=> fn();
hoc(()=>{
 console.log('foo');
});
```

上面的代码有一个`hoc`函数，它接受一个函数`fn`并运行它。

然后，当我们进行以下调用时:

```
hoc(()=>{
 console.log('foo');
});
```

然后我们记录了`foo`，因为我们传入的函数是在`hoc`函数中运行的。

# 为什么函数被称为一级对象？

JavaScript 函数是一级对象，因为它们被视为其他类型的对象。

这意味着函数可以存储在变量、对象或数组中。它们可以作为参数传递给函数。同样，它们也可以从函数中返回。

例如，我们可以将函数分配给变量，如下所示:

```
let foo = () => {};
```

我们给变量`foo`分配了一个箭头函数。

此外，它们可以作为参数传入，如下所示:

```
let foo = () => {
 console.log('foo');
};
let bar = (fn) => fn();
bar(foo);
```

然后我们可以调用传入了`foo`的`bar`。注意我们只是传入`foo`的引用，我们不调用它。因此，`foo`后面没有括号。

它们可以由如下函数返回:

```
let foo = () => {
 return ()=> console.log('foo')
};foo()();
```

上面的`foo`函数返回一个记录`'foo'`的函数。

然后我们像上一行那样调用它。

JavaScript 函数也可以存储在对象和数组中。例如，我们可以写:

```
let obj = {
  foo: () => {
    console.log("foo");
  }
};
obj.foo();
```

我们将`foo`函数作为属性放在对象内部，然后在最后一行调用它。

最后，我们可以将它们存储在一个数组中，如下所示:

```
const arr = [
  () => {
    console.log("foo");
  }
];arr[0]();
```

或者我们可以调用`push`将其放入数组:

```
const arr = [];arr.push(() => {
  console.log("foo");
});arr[0]();
```

![](img/2cf4ace20e44cf39255ae157bba5a6dc.png)

照片由[艾米·赫斯基](https://unsplash.com/@amyhirschi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 什么是 arguments 对象？

`arguments`对象是一个类似数组的对象，它有我们传递给函数的参数。Array-like 意味着它具有`length`属性，我们可以通过使用索引来循环访问这些项，但是它不包含任何数组方法，如`map`、`reduce`或`filter`。

它只得到传统函数的参数。

例如，我们可以写:

```
function foo() {
  console.log(arguments);
}foo(1, 2, 3, 4, 5);
```

然后`console.log`将返回我们传递给`foo`的参数，即 1、2、3、4 和 5。

箭头函数没有绑定到`arguments`对象，所以我们不能用这个对象将所有的参数传递给一个箭头函数。

`for...of`循环也适用于`arguments`对象，因此我们可以如下循环遍历参数:

```
function foo() {
  for (let arg of arguments) {
    console.log(arg);
  }
}foo(1, 2, 3, 4, 5);
```

我们可以使用 spread 操作符将`arguments`转换成一个数组。例如，我们可以写:

```
function foo() {
  console.log([...arguments]);
}foo(1, 2, 3, 4, 5);
```

然后我们得到`[1, 2, 3, 4, 5]`日志。

# 结论

JavaScript 有很多函数式编程特性。函数是一级对象，这意味着它们像其他对象一样被对待。

它还有古怪的、类似数组的参数，用来获取传递给传统函数的参数。