# JavaScript 中的‘this’是什么？

> 原文：<https://levelup.gitconnected.com/what-is-this-in-javascript-5d9bea7f3f06>

![](img/04a4332ff6fa2ea6db6eb29f34247cf9.png)

照片由[李东旻](https://unsplash.com/@alexleegdp?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

`this`的值是 JavaScript 中最令人困惑的部分之一。根据它所处的位置，它可以呈现不同的值。

在本文中，我们将看看`this`在哪里使用，它可以取什么值。

# 最高级的

如果我们在顶层使用`this`，那么我们应该得到全局对象作为`this`的值，因为它不在任何主机对象中。

例如，如果我们有:

```
console.log(this)
```

在浏览器脚本的顶层，我们应该看到记录为`this`值的`window`对象。我们可以通过以下方式验证这一点:

```
console.log(this === window)
```

# 顶级函数内部

如果我们定义一个常规函数并引用`this`，那么我们可能会得到 2 个不同的值，这取决于我们的脚本是否开启了严格模式。

`this`如果不是在严格模式下就是`window`。然而，如果一个脚本使用严格模式，那么它将是`undefined`。

例如，如果我们有:

```
'use strict';function foo() {
  console.log(this);
}
foo();
```

然后我们看到`this`就是`undefined`。

另一方面，如果我们有:

```
function foo() {
  console.log(this);
}
foo();
```

然后我们从`console.log`看到`this`的值是`window`。

# 内部对象

在一个对象内部，`this`当它在一个对象的常规方法内部时，应该接受宿主对象的值。例如，如果我们有:

```
function fullName() {
  return `${this.firstName} ${this.lastName}`;
}const jane = {
  firstName: 'Jane',
  lastName: 'Smith',
  fullName
};
const alex = {
  firstName: 'Alex',
  lastName: 'Smith',
  fullName
};console.log(jane.fullName());
console.log(alex.fullName());
```

在上面的代码中，我们有一个名为`fullName`的常规函数，它引用`this.firstName`和`this.lastName`返回它的组合。

然后，如果我们定义`jane`和`alex`对象，并将`fullName`函数放入其中，并像上面那样调用它们，我们将得到:

```
Jane Smith
```

从第`console.log`和:

```
Alex Smith
```

从第二个`console.log`开始。

这是因为如果`this`位于宿主对象的方法的顶层，它就会接受宿主对象的值。

# 内部构造函数

在 JavaScript 中，构造函数是使用`new`操作符创建新实例来改变`this`属性的函数。

例如，如果我们在没有严格模式的情况下运行以下代码:

```
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}Person('Joe', 'Smith');
const {
  firstName,
  lastName
} = window;
console.log(firstName, lastName);
```

然后我们会看到`Joe Smith`从`console.log`登录，因为`this`是`window`如果我们的脚本不使用严格模式并且在顶层。

所以`this`是`window`，所以添加`firstName`和`lastName`作为`window`的属性。

这是我们需要严格模式的一个原因。我们不想通过给`window`附加属性而意外附加新的全局变量。

如果我们以严格模式运行上面的代码，如下所示:

```
'use strict';
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}Person('Joe', 'Smith');
const {
  firstName,
  lastName
} = window;
console.log(firstName, lastName);
```

然后我们会得到一个错误，说“无法设置未定义的属性‘first name ’”,因为在严格模式下`this`会是`undefined`。

我们应该做的是使用`new`操作符来调用构造函数。例如，我们应该创建一个新的`Person`实例，如下所示:

```
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}const person = new Person('Joe', 'Smith');
```

然后我们得到:

```
{
  "firstName": "Joe",
  "lastName": "Smith"
}
```

作为`person`的值。

如果我们用`new`操作符调用构造函数，那么`this`引用构造函数的当前实例。

![](img/90823f8169cc6e5d8bb2b14a21c4d3f2.png)

照片由 [sydney Rae](https://unsplash.com/@srz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 课堂内部

由于 JavaScript 类只是构造函数的语法糖，我们上面看到的构造函数都适用于类。

例如，下面的示例将得到与上一个示例相同的结果:

```
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }
}const person = new Person('Joe', 'Smith');
```

`person`应该是:

```
{
  "firstName": "Joe",
  "lastName": "Smith"
}
```

`constructor`的参数与构造函数中的参数相同。

然而，如果我们试图在没有`new`操作符的情况下调用`Person`类，我们将会得到一个错误。

这是使用类而不是构造函数的一个很好的特性。

# 内部箭头功能

箭头函数没有绑定到`this`，所以上面的结果对箭头函数来说是不一样的

例如，无论是否启用严格模式，顶级箭头函数都将`window`作为`this`的值:

```
const foo = () => {
  console.log(this);
}
foo();
```

下面的例子，将为我们获取每个`console.log`的`undefined undefined`:

```
const fullName = () => {
  return `${this.firstName} ${this.lastName}`;
}const jane = {
  firstName: 'Jane',
  lastName: 'Smith',
  fullName
};
const alex = {
  firstName: 'Alex',
  lastName: 'Smith',
  fullName
};
console.log(jane.fullName());
console.log(alex.fullName());
```

正如我们所见，箭头函数不会设置`this.firstName`和`this.lastName`的值。`this`正如我们所见只是`window`而不是宿主对象，所以两者都是`undefined`。

# 结论

`this`如果在用传统函数定义的方法中，则接受主机类或主机对象的值。箭头函数没有绑定到`this`，所以我们不能像传统函数那样引用它。

很容易像普通函数一样意外调用构造函数。如果这样做了，而我们没有使用严格模式，那么我们会意外地给`window`添加新的属性。因此，使用类更好，因为没有`new`我们不能调用类。构造函数和类是等价的。