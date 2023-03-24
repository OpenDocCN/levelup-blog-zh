# 使用 JavaScript 代理对象控制对象操作

> 原文：<https://levelup.gitconnected.com/using-proxies-to-control-javascript-object-operations-3ce1738ba811>

## JavaScript 对象操作可以使用特殊的代理对象来控制

![](img/7095b5db24566c5389dcf4e44e0cdd49.png)

由[肯德尔·莱恩](https://unsplash.com/@kendall3lane?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

在 JavaScript 中，代理是一个对象，它让我们控制当我们做一些操作时会发生什么。例如，我们可以使用它们来控制属性的查找、赋值、枚举或函数的调用方式。

代理构造函数有两个参数。第一个是目标，它是您想要对其应用控制操作的对象，第二个是处理程序，它是实际控制操作在目标对象中如何行为的对象，也称为陷阱。

处理程序对象是包含代理陷阱的对象。它有很多方法让我们控制对象的基本操作。对象有许多方法来捕获由对象构造函数中的方法完成的各种操作。它们包括:

*   `handler.getPrototypeOf()` —让我们控制目标对象的`Object.getPrototypeOf()`方法的行为
*   `handler.setPrototypeOf()` —让我们控制目标对象的`Object.setPrototypeOf()`方法的行为
*   `handler.isExtensible()` —让我们控制目标对象的`Object.isExtensible()`方法的行为
*   `handler.preventExtensions()` —让我们控制目标对象的`Object.preventExtensions()`方法的行为
*   `handler.getOwnPropertyDescriptor()` —让我们控制目标对象的`Object.getOwnPropertyDescriptor()`方法的行为
*   `handler.defineProperty()` —让我们控制目标对象的`Object.defineProperty()`方法的行为
*   `handler.has()` —让我们控制目标对象的`Object.has()`方法的行为
*   `handler.get()` —让我们控制目标对象的`Object.get()`方法的行为
*   `handler.set()` —让我们控制目标对象的`Object.set()`方法的行为
*   `handler.deleteProperty()` —让我们控制目标对象的`Object.deleteProperty()`方法的行为
*   `handler.ownKeys()` —让我们控制目标对象的`Object.ownKeys()`方法的行为
*   `handler.apply()` —让我们控制目标对象的`Object.apply()`方法的行为
*   `handler.construct()` —让我们控制目标对象的`Object.construct()`方法的行为

一个基本的例子是用代理返回一个属性的默认值。例如，如果我们有以下代码:

```
const handler = {
  get(obj, prop) {
    return prop === 'a' && obj[prop] ?
      obj[prop] :
      1;
  }
};let p = new Proxy({}, handler);
console.log(p.a); // 1
p.a = 2;
console.log(p.a); // 2
```

那么第一个`console.log`语句将输出 1，第二个将输出 2。这是因为在`handler`对象中，我们有一个`get`函数来修改属性的检索方式。在函数中，如果属性名是`a`而`obj[prop]`是 truthy，也就是说`obj['a']`是 truthy，那么我们返回它，否则返回 1。这将把`p.a`的默认值设置为 1，其中`p`是由代理构造器构造的代理对象。如果我们为`p.a`设置一个新值，那么`get`函数将返回新值，因为它是真的。因此，`p.a`的第二个`console.log`语句输出 2。

我们可以为参数`handler`传入一个空对象。它将使所有对目标对象的默认操作按原样转发。例如，如果我们有:

```
let p = new Proxy({}, {});
console.log(p.a); // undefined
p.a = 2;
console.log(p.a); // 2
```

然后我们得到第一个`console.log`语句是`undefined`，但是第二个是 2，因为我们没有修改 handler 对象中的`get`函数，以便在没有设置任何内容的情况下返回任何内容。

此外，我们可以使用代理来验证分配给对象属性的值。例如，我们可以用它来验证一个有效的美国电话号码是否分配给了代理对象的一个属性:

```
const handler = {
  set(obj, prop, value) {
    const validPhone = /^\d{3}-\d{3}-\d{4}$/.test(value);
    if (prop === 'phoneNumber') {
      if (!validPhone) {
        throw new Error('Invalid phone number');
      }
    } obj[prop] = value;
    return validPhone;
  }
};let person = new Proxy({}, handler);person.phoneNumber = '555-555-5555'; // valid
console.log(person.phoneNumber);
person.phoneNumber = 'abc'; // throws an error
```

在上面的例子中，我们通过检查给定的正则表达式来检查所分配的实际上是一个有效的美国电话号码。如果`phoneNumber`属性被赋值，那么我们根据`set`函数的参数中给出的`value`检查正则表达式，如果`validPhone`是`false`，那么我们抛出一个错误。否则，我们将该值设置为对象的`phoneNumber`属性。最后，我们返回给定`value`的验证状态。第一项任务:

```
person.phoneNumber = '555-555-5555';
```

这应该可行，因为它匹配`set`函数中的正则表达式。然而，第二个赋值会抛出一个错误，因为它不匹配给定的正则表达式。

我们可以给`handler`对象添加`construct`函数来扩展`target`对象的构造函数。例如，我们可以通过将基本对象的原型设置为超类来用超类扩展基本对象，然后用具有`construct`和`apply`函数的`handler`对象创建一个新的代理来控制构造函数的行为和基本对象的`apply`函数。例如，我们可以写:

```
function extend(sup, base) {
  const descriptor = Object.getOwnPropertyDescriptor(
    base.prototype, 'constructor'
  );
  base.prototype = Object.create(sup.prototype);
  const handler = {
    construct(target, args) {
      const obj = Object.create(base.prototype);
      this.apply(target, obj, args);
      return obj;
    },
    apply(target, that, args) {
      sup.apply(that, args);
      base.apply(that, args);
    }
  };
  const proxy = new Proxy(base, handler);
  descriptor.value = proxy;
  Object.defineProperty(base.prototype, 'constructor', descriptor);
  return proxy;
}let Person = function(name) {
  this.name = name;
};let Boy = extend(Person, function(name, age, gender) {
  this.name = name;
  this.age = age;
  this.gender = gender;
});let Joe = new Boy('Joe', 13, 'M');
console.log(Joe.gender);
console.log(Joe.name);
console.log(Joe.age);
```

这将创建一个以`base`对象为目标的代理。`handler`具有`constructor`和`apply`函数，分别修改`base`对象和`apply`函数的构造函数的行为。

`construct`函数通过将原型设置为作为`obj`对象的超类的`sup`对象来创建一个新的对象`obj`，在 JavaScript 中它与原型相同。这是一个模板对象，`base`对象继承了它的成员。然后`this.apply(target, obj, args);`使用`args`对象中传递的参数运行构造函数，然后返回`obj`对象。`handler`中的`apply`函数运行`sup`和`base`对象的构造函数来构造基础对象。

然后在最后，创建代理对象并用`descriptor.value = proxy;`行将其设置为构造函数的值。然后，我们通过运行`Object.defineProperty(base.prototype, ‘constructor’, descriptor);`来设置`base`对象的原型的构造函数，并返回代理对象，让我们用一个超类对象来扩展基本对象的构造函数。

在`extend`函数下面，我们创建了一个`Person`构造函数，让我们设置`Person`实例的`name`属性。然后我们用`Person`对象调用`extend`函数，并传递一个新的构造函数给`name`、`age`和`gender`的参数来设置这些属性。然后我们得到一个新构造的对象，它具有:

```
let Joe = new Boy('Joe', 13, 'M');
```

然后，当我们记录属性时，我们为`gender`得到‘M ’,为`name`得到‘Joe ’,为`age`得到 13。

![](img/421d744c0270e8b8c1e61bfdf3681693.png)

乔安娜·科辛斯卡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当我们设置代理对象的一个属性时，我们可以同时修改该对象的另一个属性。例如，如果我们有一个由带有`people`属性的`target`对象构成的`room`代理对象，它有一个在同一个房间里的人的名字的数组，并且我们想在设置代理对象的`lastPerson`属性时将它推送到`people`数组。我们可以通过下面的代码做到这一点:

```
let room = new Proxy({
  people: ['Joe', 'Jane']
}, {
  get(obj, prop) {
    if (prop === 'lastPerson') {
      return obj.people[obj.people.length - 1];
    }
    return obj[prop];
  },
  set(obj, prop, value) {
    if (prop === 'lastPerson') {
      obj.people.push(value);
      obj[prop] = value;
      return true;
    }
    return true;
  }
});console.log(room);
room.lastPerson = 'John';
console.log(room.people);
console.log(room.lastPerson);room.lastPerson = 'Mary';
console.log(room.people);
console.log(room.lastPerson);
```

在`get`函数中，我们指定属性`lastPerson`的值将是`people`数组的最后一个元素。因此，当我们在`room.lastPerson`上运行`console.log`时，我们总是得到`room.people`数组的最后一个元素。否则，我们按原样设置对象。在`set`函数中，当`lastPerson`属性被修改时，我们也将被设置的值推入`room`代理对象的`people`数组中。因此，当我们运行`console.log`语句时，我们得到:

```
["Joe", "Jane", "John"]
John["Joe", "Jane", "John", "Mary"]
Mary
```

正如我们所见，当我们设置`room`的`lastPerson`属性时，我们也将相同的值推入到`people`数组中。

下面是一个更全面的例子，我们可以在`handler`对象中设置陷阱来控制代理对象的操作行为:

```
const handler = {
  get(obj, prop) {
    return obj[prop];
  },
  set(obj, prop, value) {
    obj[prop] = value;
    return true;
  },
  deleteProperty(obj, prop) {
    delete obj[prop];
    return false;
  },
  ownKeys(obj) {
    return Reflect.ownKeys(obj);
  },
  has(obj, prop) {
    return prop in obj;
  },
  defineProperty(obj, prop, descriptor) {
    Object.defineProperty(obj, prop, descriptor)
    return true;
  },
  getOwnPropertyDescriptor(obj, prop) {
    return Object.getOwnPropertyDescriptor(obj, prop);
  },
}let proxy = new Proxy({}, handler);
proxy.a = 1;
console.log(proxy.a);
console.log(Object.getOwnPropertyDescriptor(proxy, 'a'))
console.log(Object.defineProperty(proxy, 'b', {
  value: 1
}))
console.log('a' in proxy);
console.log(delete proxy.c);
console.log(Object.keys(proxy));
```

在上面的例子中，`handler`对象中的`getOwnProperty`函数控制着`Object.getOwnPropertyDescriptor()`应用于`proxy`对象时的行为。`handler`对象中的`defineProperty`函数控制`Object.defineProperty()`在`proxy`对象上被调用时的行为。`has`函数控制`in`操作符的行为，`deleteProperty`函数控制`delete`操作符以`proxy`对象为操作数运行时返回的值。当我们在`proxy`上使用`delete`操作符时，我们返回了`false`而不是通常的`true`。`ownKeys`函数通过用`Reflect`对象枚举一个对象的键来修改`Object.keys()`方法的行为。

## 包扎

JavaScript 代理是控制对象操作行为的一种有用方式。我们可以控制对象操作符如`in`、`delete`和赋值操作符如何作用于目标对象及其属性。这对于那些操作中的验证非常有用，并且对于修改像`in`和`delete`操作符那样返回值的操作的返回值也很方便。

我们可以用`set`函数修改赋值操作符，在这里我们可以验证被赋值的值，同时还可以修改其他属性。我们可以修改`get`函数的行为，在某些情况下为属性返回不同的值，比如没有为属性设置值。