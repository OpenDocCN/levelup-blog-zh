# 为什么我们不再需要 JavaScript 中的“函数”关键字

> 原文：<https://levelup.gitconnected.com/why-we-dont-need-the-function-keyword-in-javascript-anymore-7e43a8709491>

![](img/15453d09973589757e7108a011b3a0eb.png)

照片由[艾莉·史密斯](https://unsplash.com/@creativegangsters?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

从版本 1 开始，`function`关键字就出现在 JavaScript 中，让我们可以用各种方式定义函数。

在本文中，我们将看看为什么我们再也不需要使用`function`关键字来定义函数了。

# 箭头功能

我们可以将箭头函数用于所有非构造函数方法或不引用对象或构造函数中的`this`的对象方法。

例如，不写:

```
const arr = [1, 2, 3].map(function(x) {
  return x ** 2;
})
```

我们写道:

```
const arr = [1, 2, 3].map(x => x ** 2);
```

它要短得多，也更好，我们不必担心函数中`this`的值。

我们可以在回调中引用`this`，但是我们不应该这样做，因为嵌套的作用域会使引用它更加混乱。

同样，如果我们有一个不引用`this`的独立函数，我们也可以使用箭头函数:

```
const greet = (name) => console.log(`hi ${name}`);
```

正如我们所见，箭头函数也适用于独立函数。

对于对象中的方法，不要这样写:

```
const obj = {
  foo: function() {
    console.log('foo')
  }
}
```

我们可以写:

```
const obj = {
  foo: () => {
    console.log('foo')
  }
}
```

由于上面的`foo`方法没有引用`this`，我们可以使用一个箭头函数。

# 引用此的对象方法

如果我们的对象有引用`this`的方法，那么我们可以使用方法简写。

例如，不要这样写:

```
const person = {
  name: 'jane',
  greet: function() {
    console.log(`hi ${this.name}`)
  }
}
```

我们写道:

```
const person = {
  name: 'jane',
  greet() {
    console.log(`hi ${this.name}`)
  }
}
```

正如我们再次看到的，它要短得多，它们做同样的事情。简写与使用`function`关键字相同。

![](img/d23b5662c7dce5227f215ba5d711a973.png)

[奥比·奥尼耶德](https://unsplash.com/@thenewmalcolm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 构造函数和类

如果我们想创建构造函数，那么我们应该使用类语法。

例如，如果我们有以下带方法的构造函数:

```
function Person(name) {
  this.name = name;
}Person.prototype.greet = function() {
  console.log(`hi ${this.name}`)
}
```

我们写道:

```
class Person {
  constructor(name) {
    this.name = name;
  } greet() {
    console.log(`hi ${this.name}`)
  }
}
```

如果我们做错了什么，旧的构造函数语法会抛出错误。

但是，新的类语法可以。此外，我们可以像对待`greet`方法一样使用 class 方法简写，我们不必将它作为属性附加到类的`prototype`属性上。

因此，我们再次不需要`function`关键字，因为它再次变得多余。

# 结论

在今天的 JavaScript 代码中，我们可以不使用`function`关键字。我们可以对不需要引用`this`的函数使用箭头函数，对任何需要引用`this`的函数使用方法简写。