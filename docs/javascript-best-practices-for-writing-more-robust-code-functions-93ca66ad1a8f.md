# 编写更健壮代码的 JavaScript 最佳实践—函数

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-for-writing-more-robust-code-functions-93ca66ad1a8f>

![](img/e568487beb62db68159ff4b8575b8cbe.png)

照片由 [Amar Yashlaha](https://unsplash.com/@pictagramar?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种简单易学的编程语言。编写运行和执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的 JavaScript 代码。

在本文中，我们将研究如何编写更加健壮和可维护的 JavaScript 函数。

# 多个参数优于一个对象参数

如果函数中没有太多的参数，我们应该有多个参数，而不是一个对象参数。

有多个参数比只有一个对象参数要清楚得多。

例如，以下内容:

```
const fullName = (person) => `${this.firstName} ${this.lastName}`
```

比以下内容更难阅读:

```
const fullName = (firstName, lastName) => `${firstName} ${lastName}`
```

因为我们不知道函数签名中的`person`参数包含什么，直到我们查看代码。

在第二个例子中，我们知道`firstName`和`lastName`是参数。

如果函数比上面的更复杂，那么追踪我们的函数就更难了。

一个好的经验法则是，对于具有 5 个或更少参数的函数，它们应该在函数签名中单独列出。

否则，我们别无选择，只能将它们中的一些或全部组合成一个对象参数，以保持参数的数量为 5 个或更少。

这是因为一旦一个函数有超过 5 个参数，现在函数签名变得难以阅读。

同样，如果一个函数有很多参数，那么就很难记住传递参数的顺序，这样我们就可以正确地调用我们的函数。

如果我们的函数有很多参数，跳过参数也是一个问题，因为我们必须检查在哪里传入`undefined`以便我们可以跳过传入该参数的值。

如果我们传入一个对象，那么我们可以将属性设置为`undefined`，然后调用函数，而不是考虑如果我们的函数有很多参数，我们需要在哪里传入`undefined`。

# 析构和函数

析构是 ES2015 的一个很好的功能，它让我们可以将数组条目和对象条目分解成它们自己的变量。

在函数方面，我们可以使用它来析构对象或数组参数，以选择性地引用我们需要的对象属性或数组条目。

例如，如果我们有以下函数:

```
const fullName = (person) => `${this.firstName} ${this.lastName}`
```

然后我们可以用析构语法重写它，写为:

```
const fullName = ({
  firstName,
  lastName
}) => `${firstName} ${lastName}`
```

然后我们可以这样称呼它:

```
fullName({
  firstName: 'joe',
  lastName: 'smith'
})
```

在上面的代码中，我们传入了:

```
{
  firstName: 'joe',
  lastName: 'smith'
}
```

到`fullName`，然后由于析构语法的原因，JavaScript 解释器将自动获取具有给定属性名的值，并插入具有该变量值的字符串。

因此，析构对象中的`firstName`与字符串中引用的相同，也与传递给`fullName`函数的`firstName`属性相同。

因此，控制台日志输出应该是`'joe smith'`。

它也适用于嵌套对象。例如，我们可以写:

```
const fullName = ({
  name: {
    firstName,
    lastName
  }
}) => `${firstName} ${lastName}`
```

那么如果我们如下调用`fullName`:

```
fullName({
  name: {
    firstName: 'joe',
    lastName: 'smith'
  }
})
```

我们会得到和以前一样的结果。重要的是要注意，我们不必在析构语法中包含对象的每个属性。

我们也可以写:

```
const name = ({
  name: {
    firstName,
  }
}) => `${firstName}`
```

称之为:

```
name({
  name: {
    firstName: 'joe',
    lastName: 'smith'
  }
})
```

析构也适用于数组。在函数方面，我们可以用它将数组参数析构为变量。例如，我们可以写:

```
const fullName = ([firstName, lastName]) => `${firstName} ${lastName}`
```

现在`fullName`函数接受一个数组作为参数，而不是一个对象。

那么我们可以这样称呼它:

```
fullName(['joe', 'smith'])
```

我们会像之前一样得到`'joe smith'`作为返回值。

![](img/405a2be99891267892987a5c4fca82bd.png)

照片由[阿维·理查兹](https://unsplash.com/@avirichards?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

如果我们的函数接受 5 个或更少的参数，那么它们应该是分开的，以便于阅读，并减少使用函数的人的认知负担。

此外，当我们需要将对象或数组条目作为函数中的参数，并且希望有选择地引用这些参数中的条目时，析构语法非常有用。