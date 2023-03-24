# 用 JavaScript 调用事物的更好方法

> 原文：<https://levelup.gitconnected.com/better-ways-of-calling-things-in-javascript-1499d68c6d10>

![](img/a1ddb81f7465ffceb63bd2e62f218cfd.png)

[Icons8 团队](https://unsplash.com/@icons8?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

JavaScript 是前端开发的主要语言。它也大量用于 Node 的后端。

在本文中，我们将研究用 JavaScript 调用事物的不同方式，包括函数、方法和构造函数。

# JavaScript 中的调用方式

以下内容可以在 JavaScript 中调用，它们包括:

*   功能—例如`foo(1)`
*   方法调用-例如`obj.foo(1)`
*   构造函数调用—例如`new Foo(1)`

# 超级功能

ES6 引入了创建构造函数的类语法。有了类语法，我们可以创建以一种简洁、易读的方式从其他构造函数继承的构造函数。

为了调用父构造函数，我们使用了`super`函数。

我们可以这样称呼它:

```
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }
}class Student extends Person {
  constructor(firstName, lastName, studentNumber) {
    super(firstName, lastName);
    this.studentNumber = studentNumber;
  }
}
```

上面的代码调用`Student`类中的`super`函数来调用`Person`构造函数，用`extends Person`代码将其指定为父类。

我们也可以调用父类的方法，如下所示:

```
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  } fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
}class Student extends Person {
  constructor(firstName, lastName, studentNumber) {
    super(firstName, lastName);
    this.studentNumber = studentNumber;
  } fullStudentName() {
    return super.fullName();
  }
}const student = new Student('Jane', 'Doe', '123');
console.log(student.fullStudentName());
```

在上面的代码中，我们在`Student`类中有`fullStudentName`方法，它调用`Person`类的`fullName`方法。

因此，最后一行应该记录`'Jane Doe'`，因为我们返回了`Person`类的`fullName`方法的结果。

# 更喜欢将箭头函数作为回调函数

默认情况下，箭头函数应该用作回调函数。只要不需要引用`this`，就可以使用箭头函数。因为它没有`this`或`arguments`值，所以产生的混淆较少。

如果 arrow 函数只有一行，输入起来也更短，我们不需要使用`return`关键字返回值。

# 使用它作为隐式参数

`this`有时用作回调的隐式参数。例如，在下面的示例中，我们有:

```
function foo(fn) {
  fn.call({
    bar() {
      console.log('bar');
    }
  })
}foo(function() {
  this.bar();
});
```

`foo`函数调用一个海关值为`this`的`fn`回调。然后我们引用`this`中的:

```
foo(function() {
  this.bar();
});
```

来调用`bar`函数。

现在我们要担心的是`this`的值，以及它是否改变。

我们可以通过编写以下代码来重写代码以移除对`this`的依赖:

```
const foo = (fn) => {
  const obj = {
    bar() {
      console.log('bar');
    }
  }
  fn(obj)
}foo((obj) => {
  obj.bar();
});
```

上面的代码和前面的例子做了同样的事情，但是我们使用了箭头函数。

我们所做的是将`obj`对象传递给`fn`回调，然后我们可以传入一个带有`obj`参数的函数，然后在其中调用`bar`。

# 更喜欢没有回调的函数声明

函数声明比回调更容易阅读。

传入回调要求我们查看回调中的代码，以及接受回调并调用它的函数中的代码。

另一方面，如果我们的函数不调用回调函数，那么我们就不必在不跟踪回调函数的情况下依次查看代码。

![](img/8aaf9453d1da0b0bf1f33bf01e59c905.png)

照片由[伯克利通信](https://unsplash.com/@berkeleycommunications?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 避免 ES6 中的生活

随着 ES6 中模块的广泛使用，我们不需要 IIFEs(立即调用函数表达式的缩写)来隐藏私有变量或名称空间项，因为我们可以在模块中这样做。

要创建私有变量，我们只需不导出模块中的成员。

如果我们想命名一些东西，我们可以把它们都放在一个模块中。

例如，我们可以把:

```
const foo = (() => {
  return {
    bar: () => {}
  }
})
```

变成:

`foo.js`:

```
export const bar = () => {};
```

模块更加简洁，允许我们拥有更小的代码片段。我们不必将所有的文件放在一个大文件中来访问它们，也不会通过将它们附加为`window`的属性来污染全局名称空间。

IIFEs 唯一有用的地方是立即调用匿名`async`函数来运行 promise 代码，如下所示:

```
(async () => {
  const result = await Promise.resolve(1);
  console.log(result);
})();
```

上面的代码运行一个承诺并记录值。

然而，很快我们就可以有顶级的`await`了，所以我们不再需要这个构造了。

# 结论

如果我们能避免引用`this`，我们应该。因此，我们应该使用箭头函数进行回调，而不应该使用`this`作为隐式参数。

如果我们想传入一个参数，那么我们显式地传递它。

IIFEs 也应该避免，因为大部分的使用可以被模块代替。随着顶级`await`的到来，我们不再需要它来调用匿名`async`函数了。

`super`很有用，因为它用于调用父构造函数和父类的方法。