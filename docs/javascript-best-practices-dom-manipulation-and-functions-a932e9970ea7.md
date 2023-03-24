# JavaScript 最佳实践— DOM 操作和函数

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-dom-manipulation-and-functions-a932e9970ea7>

![](img/e24de49496e71f1fa960c15a736e38a9.png)

照片由 [Les routes sans fin(s)](https://unsplash.com/@routessansfins?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何其他编程语言一样，JavaScript 有自己的最佳实践列表，使程序更容易阅读和维护。JavaScript 有很多棘手的部分，所以我们应该避免降低代码质量的事情。通过遵循最佳实践，我们可以创建优雅且易于管理的代码，任何人都可以轻松使用。

在本文中，我们将研究减少 DOM 操作、使用快捷表示法以及让函数执行单一任务的方法。

# 避免 DOM 操作

在我们的 JavaScript 中，如果我们想动态显示某些东西，我们应该避免操纵 DOM。我们可以很容易地在 CSS 中放置大量样式代码，而不是直接用 JavaScript 操作它。

例如，如果我们希望在提交时输入无效时显示红色边框，我们可以编写以下 HTML 代码:

```
<form>
  <input type='text' required>
  <button type='submit'>
    Submit
  </button>
</form>
```

以及以下 JavaScript 代码:

```
const input = document.querySelector('input');
const form = document.querySelector('form');form.onsubmit = (e) => {
  e.preventDefault();
}input.oninvalid = () => {
  input.style.borderColor = 'red';
  input.style.borderStyle = 'solid';
  input.style.borderWidth = '1px';
}
```

正如我们所看到的，在我们分配给输入的`oninvalid`属性的事件处理程序中，我们用 JavaScript 设置了边框。

然而，我们可以通过创建一个类并在 JavaScript 中设置类名来实现 CSS。

我们保留了相同的 HTML 代码，但添加了以下 CSS 代码:

```
.invalid {
  border: 1px solid red;
}
```

然后，在我们的 JavaScript 代码中，我们用以下内容替换我们之前的内容:

```
const input = document.querySelector('input');
const form = document.querySelector('form');form.onsubmit = (e) => {
  e.preventDefault();
}input.oninvalid = () => {
  input.className = 'invalid';
}
```

正如我们所见，它以更短的方式做同样的事情。它需要更少的处理能力，因为它不动态设置样式。

此外，现在我们有了一个可以在其他 HTML 元素中重用的类和样式，这样我们就不必重复代码了。

# 使用快捷方式

在 JavaScript 中，有许多快捷方式对于开发者来说仍然清晰易读。

例如，当我们创建一个对象时，我们可以使用`Object`构造函数或者对象文字符号。

使用`Object`构造函数，我们可以如下创建一个对象:

```
let obj = new Object();
obj.foo = '1';
obj.bar = '2';
obj.baz = function() {
  console.log('baz');
}
```

或者，我们可以用对象文字符号写同样的东西:

```
let obj = {
  foo: '1',
  bar: '2',
  baz() {
    console.log('baz');
  }
}
```

我们有相同的行数，但是我们必须在第一个例子的每一行中重复对象名。然而我们不必对对象字面量这样做。

对象文字也提高了清晰度，所以我们可以用它代替`Object`构造函数来创建一个对象。它们都做同样的事情，但是我们不需要像用`Object`构造函数创建一个对象那样重复代码。

同样，不像下面这样定义数组:

```
let arr = new Array();
arr[0] = 1;
arr[1] = 2;
arr[2] = 3;
arr[3] = 4;
arr[4] = 5;
```

我们可以用一种更简短的方式来定义它:

```
let arr = [1, 2, 3, 4, 5];
```

正如我们所见，用数组文字定义它比用`Array`构造函数短得多。此外,`Array`构造函数令人困惑，因为它有两个签名。如果我们传入一个参数，那么我们得到的数组的长度由参数设定。否则，我们会得到一个数组，其中的参数是我们作为内容传入的。

另一个方便的快捷方式是三元运算符，在这里我们可以有条件地给变量赋值。

例如，不要写:

```
const x = 100;
let foo;
if (x === 100) {
  foo = 'bar';
} else {
  foo = 'baz';
}
```

我们可以写:

```
const x = 100;
let foo = (x === 100) ? 'bar' : 'baz';
```

在上面的代码中:

```
let foo;
if (x === 100) {
  foo = 'bar';
} else {
  foo = 'baz';
}
```

与以下内容相同:

```
let foo = (x === 100) ? 'bar' : 'baz';
```

他们都检查`x`是否是 100，如果是，则将`'bar'`赋给`foo`，否则赋给`'baz'`。

使用三元运算符时，代码要短得多，但我们得到了相同的结果。它也不会增加代码的可读性。

![](img/203134cbaced0c22d204d1e349b05803.png)

由[奥斯汀·迪斯特尔](https://unsplash.com/@austindistel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 每个任务一个功能

通常，一个函数应该只做一件事。这使得每个函数都简短易读。

这也使得重用它更容易，因为在每个函数中发生功能冲突的可能性更小。

此外，为常见任务创建助手函数也是一个好主意。

例如，如果我们想在页面上动态创建一些元素。我们可以写:

```
const createPage = () => {
  const items = ['foo', 'bar', 'baz'];
  const ul = document.createElement('ul');
  let li;
  for (const item of items) {
    li = document.createElement('li');
    li.textContent = item;
    ul.appendChild(li);
  }
  document.body.appendChild(ul); const googleLink = document.createElement('a');
  googleLink.href = '[http://www.googole.com'](http://www.googole.com');
  googleLink.textContent = 'Google';
  document.body.appendChild(googleLink); const yahooLink = document.createElement('a');
  yahooLink.href = '[http://www.yahoo.com'](http://www.yahoo.com');
  yahooLink.textContent = 'Yahoo';
  document.body.appendChild(yahooLink);
}createPage();
```

或者我们可以写:

```
const createLink = (textContent, href) => {
  const link = document.createElement('a');
  link.href = href;
  link.textContent = textContent;
  return link;
}const createUl = (items) => {
  const ul = document.createElement('ul');
  let li;
  for (const item of items) {
    li = document.createElement('li');
    li.textContent = item;
    ul.appendChild(li);
  }
  return ul;
}const items = ['foo', 'bar', 'baz'];
const ul = createUl(items);
const googleLink = createLink('Google', '[http://www.google.com'](http://www.google.com'));
const yahooLink = createLink('Google', '[http://www.yahoo.com'](http://www.yahoo.com'));
document.body.appendChild(ul);
document.body.appendChild(googleLink);
document.body.appendChild(yahooLink);
```

我们应该用第二种方式来写，因为我们把代码分成函数，这些函数在给定相同输入的情况下总是返回相同的结果。

而且，它们可以在任何地方被调用。因此，如果我们想要另一个链接，我们可以再次调用`createLink`。

我们还返回元素，所以我们可以将它附加到函数之外，这意味着我们可以动态地创建元素，而不用马上附加它们。

# 结论

当我们处理客户端 JavaScript 代码时，为了提高性能和降低代码复杂度，我们应该尽可能避免 DOM 操作。

此外，许多 JavaScript 快捷方式都是有意义的，比如只要文字可用就使用文字，而不是构造函数。

最后，我们应该将代码划分为多个函数，每个函数执行一项任务，以使代码易于阅读和维护。这也使得函数更容易重用，因为它们没有冲突的功能。