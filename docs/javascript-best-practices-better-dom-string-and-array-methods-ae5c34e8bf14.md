# JavaScript 最佳实践—更好的 DOM、字符串和数组方法

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-better-dom-string-and-array-methods-ae5c34e8bf14>

![](img/f96d078caa03b545ae741117c5e4fc11.png)

[奥利·伍德曼](https://unsplash.com/@braintax?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

为了使代码易于阅读和维护，我们应该遵循一些最佳实践。

在这篇文章中，我们将看看我们应该遵循的一些最佳实践，以使每个人的生活更轻松。

# 使用`.flatMap(…)`超过`.map(…).flat()`

我们一起使用`flatMap`而不是`map`和`flat`，因为我们可以用`flatMap`做两件事，

例如，不要写:

```
[1, 2, 3].map(i => [i]).flat();
```

我们写道:

```
[1, 2, 3].flatMap(i => [i]);
```

# 在检查存在或不存在时，`Use .includes()`而不是`.indexOf()`

在检查存在或不存在时，`includes`比`indexOf`更好。

`includes`更短。

所以与其写:

```
[].indexOf('foo') !== -1;
```

我们写道:

```
[].includes('foo');
```

# 使用现代 DOM APIs

我们应该使用新的 DOM 方法，而不是旧的。

例如，不写:

```
foo.replaceChild(baz, bar);

foo.insertBefore(baz, bar);

foo.insertAdjacentText('position', bar);

foo.insertAdjacentElement('position', bar);
```

我们写道:

```
foo.replaceWith(bar);foo.before(bar);foo.prepend(bar);foo.append(bar);
```

# 对切片和拼接使用超过`.length - index`的负索引

`slice`和`splice`的负指标比`length — index`短。

例如，不写:

```
foo.slice(foo.length - 3, foo.length - 1);
foo.splice(foo.length - 1, 1);
```

我们写道:

```
foo.slice(-3, -1);
foo.splice(-1, 1);
```

它对我们的大脑来说更短更容易。

# `Use Node#append()`超过`Node#appendChild()`

我们应该使用`append`而不是`appendChild`将元素追加到父元素中。

`append`除了节点，我们还可以添加字符串。

`append`有节点返回值。

我们可以用它附加几个节点和字符串。

例如，不写:

```
foo.appendChild(bar);
```

我们写道:

```
foo.append(bar);
foo.append(bar, 'baz');
```

# 使用`childNode.remove()`超过`parentNode.removeChild(childNode)`

我们可以使用`remove`直接移除一个节点，而不是获取父节点并调用`removeChild`来移除一个节点。

例如，不写:

```
parentNode.removeChild(foo);
```

我们写道:

```
foo.remove();
```

# 在全局属性上使用静态属性

数字静态特性优于全局特性。

`Number.isNaN`和`Number.isFinite`在检查之前不转换参数的类型。

例如，不写:

```
const foo = parseInt('20', 2);
const foo = parseFloat('30.5');
const foo = isNaN(20);
const foo = isFinite(20);
if (Object.is(foo, NaN)) {}
```

我们写道:"

```
const foo = Number.parseInt('20', 2);
const foo = Number.parseFloat('30.5');
const foo = Number.isNaN(20);
const foo = Number.isFinite(20);
if (Object.is(foo, Number.NaN)) {}
```

# 使用 c `atch`绑定参数

如果没有使用`catch`的绑定参数，那么应该省略它。

所以与其写:

```
try {} catch (error) {}
```

我们写道:

```
try {} catch {}
```

# 在`.getElementById()`上使用`.querySelector()`，在`.getElementsByClassName()`和`.getElementsByTagName()`上使用`.querySelectorAll()`

`querySelector`比`getElementById`更通用，因为前者可以使用任何选择器。

`querySelectorAll`比`getElementsByClassName`和`getElementsByTagName`更通用，因为它采用任何选择器。

所以与其写:

```
document.getElementById('baz');
document.getElementsByClassName('baz bar');
document.getElementsByTagName('main');
```

我们写道:

```
document.querySelector('main #baz .bar');
document.querySelectorAll('.baz .bar');
```

# 使用`Reflect.apply()`超过`Function#apply()`

`Reflect.apply`比`Function.apply`简短易懂。

我们知道它永远不会被覆盖，不像`Function.apply`。

所以与其写:

```
function foo() {}foo.apply(null, [42]);
```

我们写道:

```
function foo() {}Reflect.apply(foo, null, [42]);
```

# 在带有全局标志的正则表达式搜索中使用`String#replaceAll()`

`replaceAll`比正则表达式搜索替换子字符串更容易理解。

例如，不写:

```
string.replace(/foo/g, '');
```

我们写道:

```
string.replaceAll('foo', '');
```

# `Use Set#has()`检查存在或不存在时越过`Array#includes()`

我们可以用`has`代替`includes`来检查存在或不存在，因为它更快。

例如，不写:

```
const array = [1, 2, 3, 4, 5];
const hasValue = value => array.includes(value);
```

我们写道:

```
const set = new Set([1, 2, 3, 4, 5]);
const hasValue = value => set.has(value);
```

# 在`Array.from()`上使用扩展运算符

我们应该在`Array.from`上使用 spread 运算符。

这是将类似数组的对象转换为数组的一种更简单的方法。

例如，不写:

```
Array.from(set).filter(() => {});
```

我们写道:

```
[...set].filter(() => {});
```

# 使用`String#startsWith()` & `String#endsWith()`检查字符串的开始和结束

我们应该使用`startsWith`或`endsWith`来检查一个字符串是以给定的字符串开始还是结束。

例如，不写:

```
/^bar/.test(foo);
/bar$/.test(foo);
```

我们写道:

```
foo.startsWith('bar');
foo.endsWith('bar');
```

![](img/2281f9101a3359d92c2e942d32f70420.png)

扬·伊沃·亨策在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们应该使用现代的 JavaScript 方法来使我们的生活变得更容易。

有许多字符串和数组方法使我们的生活变得更加容易。

我们还应该注意新 DOM 方法，并使用它们来代替以前可用的方法。