# 神秘的 JavaScript 对象的 __proto__ 属性

> 原文：<https://levelup.gitconnected.com/the-mysterious-javascript-objects-proto-property-67b7c6b3140c>

![](img/5c154f9e17a83b4e86b494e72c86ee3e.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

JavaScript 是一种基于原型的面向对象语言。这意味着 JavaScript 对象从原型而不是从其他类继承它们的成员。

在本文中，我们将看看 JavaScript 对象拥有的`__proto__`，以及我们是否应该使用它。

# __proto__ 属性

`Object.prototype`的`__proto__`属性是一个访问器属性。它公开了内部`[[Prototype]]`的属性，可以是一个对象或`null`。它是对象自己的原型，与`prototype`相反，T5 是构造器的原型，用于构造具有`__proto__`属性的对象。

它还可以用于在创建时使用对象文字的`[[Prototype]]`，作为将原型对象传递给`Object.create`的替代方法。

例如，我们可以通过编写以下代码来设置对象的原型:

```
const Square = function() {};
const shape = {};
const square = new Square();shape.__proto__ = square;
```

然后我们可以设置`shape`对象文字的原型。

`__proto__`之前 ES6 以神秘的方式工作。在 ES6 之前，没有标准的方法来改变现有对象的原型，但是我们可以用`Object.create`用给定的原型创建一个新对象。

例如，我们可以写:

```
const Square = function() {};
const square = new Square();
const shape = Object.create(square);
```

然后 Firefox 让我们用非标准的`__proto__`属性改变现有对象的原型。

我们可以用它来改变对象的原型，就像我们在上面的代码中所做的那样。

# 通过`__proto__ Before ES6`子类化`Array`

在 ES6 之前，无法在 JavaScript 中对内置对象进行子类化。例如，要在 ES6 之前子类化`Array`对象，我们必须编写一个函数来返回我们的子类的实例。通过直接设置`__proto__`属性来创建`Array`子类的实例，如下所示:

```
function CustomArray() {
  var instance = new Array();
  instance.__proto__ = CustomArray.prototype;
  return instance;
}CustomArray.prototype = Object.create(Array.prototype);
CustomArray.prototype.foo = function() {};
```

构造函数`CustomArray`在将其`__proto__`属性设置为`CustomArray`的`prototype`后，显式返回`instance`，该`CustomArray`具有构造函数的成员。

这样做的问题是，它把对象级和元级混在了一起。`__proto__`应该是一个元级属性，但是我们将`CustomArray`的`prototype`直接设置为它，就好像它是一个对象的常规属性一样。

很容易意外地将`__proto__`用作对象级属性。

有了 ES6，这就容易多了:

```
class CustomArray extends Array {
  foo() {}
}
```

用类语法创建像`Array`这样的内置对象的子类没有问题。

# `__proto__`在 ECMAScript 6

ES6 有一个通过`Object.prototype.__proto__`实现的 get 和 setter。此外，在对象文字中，我们可以将`__proto__`属性键视为一个特殊的操作符，用于指定所创建对象的原型。

在 ES6 中，我们可以用 object literal 中的值来设置`__proto__`属性，从而设置它的原型的值。

例如，我们可以写:

```
const obj = {
  __proto__: {
    a: 1
  }
};
```

然后当我们跑的时候:

```
Object.getPrototypeOf(obj)
```

那么我们就得到`{ a: 1 }`作为`obj`的原型。

我们也可以将属性名写成字符串。以下代码:

```
const obj = {
  '__proto__': {
    a: 1
  }
};
```

与上例中的`obj`相同。

将字符串`'__proto__'`放入计算的属性不会改变原型。相反，它创建自己的属性。

例如，如果我们写:

```
const obj = {
  ['__proto__']: {
    a: 1
  }
};
```

然后，如果我们记录`Object.getPrototypeOf(obj)`的返回值，那么我们会看到`Object.prototype`被记录。

如果我们不想意外地设置一个对象文字的原型，我们可以使用`Object.defineProperty`或者将字符串`'__proto__'`放在一个计算属性中，那么在 ES6 下，它将被视为一个普通的对象属性。

例如，我们可以写:

```
const obj = {};
Object.defineProperty(obj, '__proto__', {
  a: 1
})
```

让 JavaScript 解释器将`__proto__`视为普通属性。

![](img/7f2ccdccde8702fe6cb0888907051d21.png)

罗伯特·库伦尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 检测对 ES6 风格的支持`__proto__`

我们可以使用下面的`hasOwnProperty`方法来检查`__proto__`属性是否存在:

```
({}).hasOwnProperty.call(Object.prototype, '__proto__');
```

上面的代码检查`__proto__`属性是否作为非继承属性存在于`Object.prototype`中。

我们还可以通过查看将对象文字的`__proto__`设置为`null`是否会生成对象文字的原型`null`来检查`__proto__`的存在，如下所示:

```
Object.getPrototypeOf({__proto__: null}) === null
```

# 结论

`__proto__`属性是一个特殊的属性，它具有对象实例或文字属性的原型。

它在 ES6 中是标准化的。然而，我们不应该用它来设置对象的原型。相反，我们应该使用类或`Object.create`来创建带有对象原型的对象。