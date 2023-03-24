# 用调用、绑定和应用搞乱 JavaScript

> 原文：<https://levelup.gitconnected.com/messing-with-javascript-this-with-call-bind-and-apply-52f37bc1729e>

![](img/362908c5b662724b9e63b5b457e795c8.png)

由[凯利·西克玛](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 JavaScript 中，`this`对象在不同的上下文中有不同的含义。这主要取决于`this`关键字所在的位置。如果它在一个物体中，那么`this`就是这个物体。如果`this`在一个函数中，那么`this`就是这个函数。对于类，`this`将是类，因为它只是普通构造函数的语法糖。在 JavaScript 函数中，有一个`call`、`bind`和`apply`方法来改变函数中`this`的值。在本文中，我们将仔细研究它们，以及如何使用它们来更改`this`的值。这些方法仅适用于常规函数，不适用于箭头函数。

# 呼叫

`call`方法让我们用给定的`this`对象调用一个函数，当我们调用这个函数时，我们把给定的对象和参数传递给函数。

它需要多个参数。第一个参数是一个对象，我们希望将它设置为调用`call`方法的函数中`this`的值。这是一个可选参数。我们可以将其设置为一个对象，`null`或`undefined`。在非严格模式下，`null`和`undefined`将被替换为全局对象，原始值将被转换为对象。第二个和随后的参数是我们传递给函数的参数。

`call`方法的返回值是用指定的`this`值和参数调用的同一个函数。

call 方法的目的是我们可以重用具有不同`this`值的同一个函数，而不用用我们想要的`this`值写一个新值。

例如，我们可以通过编写以下代码，用`call`方法更改函数中`this`的值:

```
let obj = {
  firstName: 'Joe',
  lastName: 'Smith'
}let person  = {
  firstName: 'Jane',
  lastName: 'Smith',
  getFullName()  {
    return `${this.firstName} ${this.lastName}`
  }
}console.log(person.getFullName());
console.log(person.getFullName.call(obj));
```

在上面的代码中，当我们调用`person.getFullName()`时，我们得到‘简·史密斯’,因为我们在`person`对象中将`firstName`设置为`'Jane'`,并且在同一个对象中将`lastName`设置为`'Smith'`。由于`this`是`getFullName`函数中的`person`对象，因此我们得到“简·史密斯”。

当我们使用`call`方法通过调用`person.getFullName.call(obj))`将`getFullName`函数内的`this`对象更改为`obj`时，我们得到‘乔·史密斯’，因为我们将`this`更改为`obj`，而`obj`将`firstName`设置为`'Joe'`并将`lastName`设置为`‘Smith’`。

我们也可以对构造函数使用`call`方法。例如，如果我们有:

```
const Person = function(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}const Employee = function(firstName, lastName, employeeCode) {
  this.employeeCode = employeeCode;
  Person.call(this, firstName, lastName)
}const employee = new Employee('Jane', 'Smith', '123');
console.log(`${employee.firstName} ${employee.lastName}`, employee.employeeCode);
```

然后在`Employee`构造函数中，我们调用之前定义的`Person`构造函数。当我们创建一个新的`Employee`对象时，我们传入一个新的`firstName`和`lastName`，然后在`Employee`构造函数内部，我们使用`Person`函数的`call`方法将`firstName`和`lastName`传递给`Person`构造函数，从而设置`this`的`firstName`和`lastName`属性的值，而不用直接在`Employee`构造函数中设置它们。

当我们在最后一行运行`console.log`时，我们得到`'Jane Smith'`和`'123'`。

再比如我们可以把`call`和匿名函数一起使用。例如，我们可以编写以下函数来记录问候语:

```
(function(greeting) {
  console.log(`${greeting} ${this.firstName} ${this.lastName}`);
}.call({
  firstName: 'Joe',
  lastName: 'Smith'
}, 'Hello'));
```

在上面的函数调用中，我们将带有`firstName`和`lastName`属性的对象传递给匿名函数的`call`方法的第一个参数。然后我们在第二个实参中传入字符串`'Hello'`，它被传入匿名函数的第一个形参。这意味着`'Hello'`是`greeting`参数的值，`this.firstName`是`'Joe'`，`this.lastName`是`'Smith'`。

所以，当我们运行上面的代码时，我们应该得到`'Hello Joe Smith’`。

# 应用

`apply`方法与`call`方法几乎相同，除了我们为第二个参数传入一个数组作为函数调用的参数，而不是一个逗号分隔的对象列表。

这意味着我们可以通过做一些小的改动，用上面例子中的`apply`代替`call`。

第一个例子是一样的，除了我们将`call`改为`apply`，因为我们没有传入第二个或后续的参数:

```
let obj = {
  firstName: 'Joe',
  lastName: 'Smith'
}let person  = {
  firstName: 'Jane',
  lastName: 'Smith',
  getFullName()  {
    return `${this.firstName} ${this.lastName}`
  }
}console.log(person.getFullName());
console.log(person.getFullName.apply(obj));
```

如果我们用构造函数的`apply`方法链接构造函数，那么我们必须将`call`改为`apply`，并将参数列表改为数组。例如，如果我们有:

```
const Person = function(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}const Employee = function(firstName, lastName, employeeCode) {
  this.employeeCode = employeeCode;
  Person.call(this, firstName, lastName)
}const employee = new Employee('Jane', 'Smith', '123');
console.log(`${employee.firstName} ${employee.lastName}`, employee.employeeCode);
```

就像我们上面做的那样，然后我们只需要把它改成:

```
const Person = function(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}const Employee = function(firstName, lastName, employeeCode) {
  this.employeeCode = employeeCode;
  Person.apply(this, [firstName, lastName])
}const employee = new Employee('Jane', 'Smith', '123');
console.log(`${employee.firstName} ${employee.lastName}`, employee.employeeCode);
```

然后我们得到和上面一样的输出。

最后，对于最后一个示例，我们可以将代码从:

```
(function(greeting) {
  console.log(`${greeting} ${this.firstName} ${this.lastName}`);
}.call({
  firstName: 'Joe',
  lastName: 'Smith'
}, 'Hello'));
```

收件人:

```
(function(greeting) {
  console.log(`${greeting} ${this.firstName} ${this.lastName}`);
}.apply({
  firstName: 'Joe',
  lastName: 'Smith'
}, ['Hello']));
```

然后我们得到和改变前一样的输出。

![](img/90958f03984231b9f59c6efdc5e9a900.png)

戴夫·威尔海特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 约束

`bind`方法仅用于改变函数中`this`的值。像`call`和`apply`一样，它可以用来将参数发送到函数中。

它需要多个参数。第一个参数是一个对象，我们希望将其设置为调用`bind`方法的函数中`this`的值。如果绑定函数是用`new`操作符构造的，这个参数将被忽略。当我们使用`bind`方法创建一个在`setTimeout`函数中作为回调提供的函数时，传递给第一个参数的任何原始值都被转换成一个对象。如果没有向`bind`提供参数，则执行范围的`this`值被视为新函数的值。

当我们用`bind`调用函数时，第二个和随后的参数是要添加到参数列表中的参数。

`bind`方法的返回值是用指定的`this`值和参数调用的同一个函数。

例如，如果我们像下面的代码那样不带参数地调用`bind`:

```
let person = {
  firstName: 'Jane',
  lastName: 'Smith',
  getName(){
    return `${this.firstName} ${this.lastName}`
  }
}console.log(person.getName.bind()());
```

然后我们得到`undefined undefined`，因为`this`将被设置为`window`对象，因为执行范围的`this`是一个`window`对象，因为它运行在顶层。

如果我们用传入的对象调用`bind`，如下面的代码所示:

```
let person = {
  firstName: 'Jane',
  lastName: 'Smith',
  getName() {
    return `${this.firstName} ${this.lastName}`
  }
}const joe = {
  firstName: 'Joe',
  lastName: 'Smith'
}console.log(person.getName.bind(joe)());
```

我们得到了`'Joe Smith'`,因为我们将`joe`对象传递给了`bind`方法的第一个参数，这将`getName`方法中的`this`对象设置为`joe`对象。

同样，我们可以用`bind`将参数传递给函数。例如，我们可以添加一个`greet`方法，该方法采用一个`greeting`和一个`age`参数，并返回一个新字符串，其中所有参数与现有字段组合在一起，如下面的代码所示:

```
let person = {
  firstName: 'Jane',
  lastName: 'Smith',
  getName() {
    return `${this.firstName} ${this.lastName}`
  },
  greet(greeting, age) {
    return `${greeting} ${this.firstName} ${this.lastName}. You're ${age} years old`
  }
}const joe = {
  firstName: 'Joe',
  lastName: 'Smith'
}console.log(person.greet.bind(joe, 'Hello', 20)());
```

如果我们运行上面的代码，那么我们得到`'Hello Joe Smith. You’re 20 years old’`，因为我们通过将`joe`传递到`bind`方法的第一个参数中，将`this`设置为`joe`。然后，我们通过传入`'Hello'`将我们的参数传入`person.greet`方法，并将 20 作为`bind`方法的第二个和第三个参数，它被传入`greet`方法调用的第一个和第二个参数。

我们也可以配合`setTimeout`功能使用。根据上面对`bind`的定义，如果我们将一个原始值传递给`setTimeout`函数的回调函数的第一个参数`bind`，那么它将被转换成一个对象。例如，如果我们有:

```
setTimeout(function(x, y) {
  console.log(this, x, y);
}.bind(1, 1, 1), 1)
```

然后我们从回调函数的`console.log`输出中得到`Number {1} 1 1`，因为我们将第一个参数中的原始值 1 转换成了一个对象，然后其他两个参数是回调函数被调用时传递给它的参数。

在 JavaScript 函数中，有一个`call`、`bind`和`apply`方法来改变函数中`this`的值。在本文中，我们将仔细研究它们，以及如何使用它们来更改`this`的值。它们仅适用于常规函数，因为箭头函数不会改变`this`的值。所有 3 个都非常相似，因为它们都改变了`this`的值，并让我们向调用它们的函数传递参数。