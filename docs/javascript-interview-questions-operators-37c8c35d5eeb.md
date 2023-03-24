# JavaScript 面试问题—运算符

> 原文：<https://levelup.gitconnected.com/javascript-interview-questions-operators-37c8c35d5eeb>

![](img/5667f0da6255f2c033d00dc1c9a9e45b.png)

照片由[约翰娜·布盖](https://unsplash.com/@johannabuguet?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

为了得到一份前端开发人员的工作，我们需要搞定编码面试。

在本文中，我们将研究一些 JavaScript 操作符问题。

# `&&`操作符是做什么的？

`&&`操作符是逻辑 AND 操作符，在其操作数中找到第一个 falsy 表达式并返回它。

例如，如果我们有:

```
console.log(0 && 1 && "foo");
```

然后我们记录 0，因为 0 是操作数中的第一个 falsy 表达式。

# `||`操作员做什么？

`||`操作符是逻辑 or 操作符，在其操作数中找到第一个真值表达式并返回它。

例如，如果我们有:

```
console.log("" || "foo" || false);
```

然后我们记录下`'foo'`,因为它是表达式中的第一个真值操作数。

如果在它之前的值是假的，它对于提供一个默认值也是有用的。

# +运算符是做什么的？

`+`操作符将后面的内容转换成数字，如果它放在表达式前面的话。所以如果我们有:

```
console.log(+"1");
```

然后我们记录了 1。

如果我们有:

```
console.log(+("1" + 2));
```

然后我们记录了 12 次。

正如我们所知，它也可以作为括号内字符串的连接操作符。

如果所有操作数都是数字，它也是加法运算符。

例如，如果我们有:

```
console.log(1 + 2 + 3);
```

然后我们得到 6。

# 这是什么！接线员做什么？

`!`运算符将操作数转换为布尔值，并对其求反。它会将假值`true`和真值转化为`false`。

例如，如果我们有:

```
console.log(!0);
console.log(!"");
console.log(!false);
console.log(!NaN);
console.log(!undefined);
console.log(!null);
```

然后都登录`true`。

如果`!`放在真值表达式之前，那么它将记录`false`。

例如，如果我们有:

```
console.log(!1);
```

然后我们会看到`false`被记录。

# 这是什么！！接线员做什么？

`!!`是双非运算符，将操作数强制为布尔值。它会将真值转换为`true`，将假值转换为`false`。

例如，如果我们有:

```
console.log(!!0);
console.log(!!'');
console.log(!!false);
console.log(!!NaN);
console.log(!!undefined);
console.log(!!null);
```

他们会记录`false`。

在任何真值表达式之前应用`!!`应返回`true`。

例如:

```
console.log(!!{});
```

日志`true`。

# rest 运算符是什么？

扩展操作符由`...`表示。

我们可以用它将析构过程中没有赋值的对象或数组条目赋值给一个包含剩余值数组的变量。

如果对象是一个变量，我们可以使用 rest 操作符将没有分配给对象的属性分配给包含原始对象剩余属性的变量。

例如，如果我们有:

```
const [one, two, ...rest] = [1, 2, 3, 4, 5, 6];
console.log(one);
console.log(two);
console.log(rest);
```

然后`one`是 1`two`是 2，`rest`有`[3, 4, 5, 6]`。

它还可以用于将对象分散到变量中。例如，我们可以这样做:

```
const { foo, bar, ...rest } = { foo: 1, bar: 2, a: 3, b: 4, c: 5 };
console.log(foo);
console.log(bar);
console.log(rest);
```

那么`foo`是 1，`bar`是 2，`rest`有`{a: 3, b: 4, c: 5}`。

我们还可以用它来获取传递给函数的参数，这个函数没有作为数组赋给它自己的参数。例如，我们可以写:

```
const foo = (a, b, ...rest) => console.log(rest);
console.log(foo(1, 2, 3, 4, 5));
```

然后`console.log`应该为`rest`记录`[3, 4, 5]`。

# 什么是传播算子？

展开操作符也由`...`操作符指示。它会将一个对象的属性传播到另一个对象，并将数组条目传播到另一个数组。

例如，如果我们有:

```
const foo = [1, 2, 3];
const bar = [...foo];
console.log(bar);
```

然后我们得到`[1, 2, 3]`作为`bar`的值，因为我们复制了`foo`并用 spread 操作符将其分配给`bar`。

它对于合并数组也很有用。例如，如果我们有:

```
const foo = [1, 2, 3];
const bar = [3, 4, 5];
const baz = [...foo, ...bar];
console.log(baz);
```

那么`baz`将会是`[1, 2, 3, 3, 4, 5]`，因为我们将`foo`和`bar`数组的条目合并到了`baz`数组中。

“扩展”操作符也适用于对象。它将制作一个对象的浅拷贝或将多个对象合并成一个。如果两个对象在合并时具有相同的属性，那么后来合并的对象将覆盖第一个对象。

例如，如果我们有:

```
const foo = { a: 1, b: 2, c: 3 };
const bar = { a: 2, d: 4, e: 5 };
const baz = { ...foo, ...bar };
console.log(baz);
```

那么`baz`就是`{a: 2, b: 2, c: 3, d: 4, e: 5}`，因为`bar`后来被合并了，所以属性`a` us 2。

spread 运算符也用于将数组扩展到函数中的参数列表中。

例如，如果我们有:

```
const add = (a, b) => a + b;
console.log(add(...[1, 2, 3, 3, 4, 5]));
```

那么`console.log`将显示 3，因为数组中的 1 和 2 被 spread 操作符分配给了`a`和`b`参数。

# 结论

`&&`、`||`、`!`和`!!`是逻辑运算符。

`&&`操作符是逻辑 AND 操作符，在操作数中找到第一个 falsy 表达式并返回它。

`||`操作符是逻辑 or 操作符，在其操作数中找到第一个真值表达式并返回。

`!`操作符将一个表达式强制转换为布尔值，然后对其求反。

`!!`操作符将一个表达式强制转换为没有否定的布尔值。

`+`操作符可以将表达式转换为数字，将数字相加，或者连接字符串。

rest 操作符对于获取数组或对象的剩余部分很有用。

spread 运算符对于合并对象和数组或制作它们的副本非常有用。它还可以用来将数组作为参数传递给函数。