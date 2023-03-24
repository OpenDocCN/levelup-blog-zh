# JavaScript:数组与对象

> 原文：<https://levelup.gitconnected.com/javascript-arrays-vs-objects-6551efddfa03>

![](img/12a438957bddb53b6b64fc9de78f836b.png)

在学习 JavaScript 的时候，我们都会偶然发现“数组只是 JavaScript 中的对象”这句话。今天，我们将把这句话放在显微镜下观察。

```
let groceries = ["apples"]
console.log(typeof groceries) // Logs "object"
```

看一下代码，很明显数组的类型是`object`。但是，这意味着什么呢？

> 如果你不熟悉`*typeof*`操作符，请点击链接[阅读关于`*typeof*`操作符的](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/typeof)。

# 遗产

为了理解对象和数组之间的区别，让我们快速浏览一下 JavaScript 中的继承。原型继承本身就是一个很大的话题，值得单独发表一篇博文。然而，为了这篇文章的目的，我将保持事情非常基本。

JavaScript 中的每个对象都有一个对其父(原型)对象的引用。当一个方法被调用时，JavaScript 会在你正在使用的对象上寻找它。如果没有找到，它会查看原型。它将继续沿着原型链向下，直到找到属性，或者到达根对象。

```
var person = {
  name: "Mr. Frontend Mayhem",
}//toString method comes from the prototype because it's not defined on the person object.
console.log(person.toString()) // Logs "[object Object]"person.toString = function() {
  return this.name
}//person object has a toString method of it's own now, which will be used.
console.log(person.toString()) // Logs "Mr. Frontend Mayhem"
```

在上面的例子中，创建了一个 person 对象，它拥有一个名为`name`的属性。当调用`toString`方法时，首先检查 person 对象，然后查找其原型，即`Object.prototype`。使用原型提供的实现，它具有返回`[object Object]`的默认行为。

接下来，在 person 对象本身上创建了`toString`方法，当从该点开始调用`toString`时，将使用该方法。

# 对象和数组之间的区别

尽管只是幕后的对象，数组的行为与常规对象非常不同。原因是`Array.prototype`对象，它拥有所有的`Array`具体方法。每个新数组都从`Array.prototype`继承了这些额外的方法。

需要注意的一个关键点是`Array.prototype`的`prototype`属性的值是`Object.prototype`。这意味着两件事:

1.  数组只是对象，但是有一些额外的方法。
2.  没有一个对象能做的事情是数组做不到的。

让我们来看看实际情况。

```
let grocery = ["apple"]
// Prototype of an array is "[]"
console.log(grocery.__proto__) // Logs []
// Looking down the chain, prototype is "{}"
console.log(grocery.__proto__.__proto__) // Logs {}// Arrays are instance of both Array & Object'
console.log(grocery instanceof Array) // Logs true
console.log(grocery instanceof Object) // Logs true
// Objects aren't instance of array
console.log({} instanceof Array) // Logs false// Array.prototype contains Array specific methods such as .push
console.log(typeof Array.prototype.push) // Logs function// Returns undefined because Object
// prototype doesn't have the push method
console.log(typeof Object.prototype.push) // Logs undefined
```

# 古怪

像 JavaScript 中的所有东西一样，数组也有自己的特点。

**非索引属性**
由于数组只是伪装的对象，所以可以在数组上设置非索引属性。这通常是第一件让人措手不及的事情。在下面的例子中，我在`groceries`数组上设置了两个名为`sorted` & `authored by`的非索引属性。注意:两个点&括号符号都被支持，就像对象一样。

```
var groceries = ["banana", "apple"]groceries.sorted = false
groceries["authored by"] = "frontendmayhem"//Non-indexed properties won't get logged
console.log(groceries) // Logs ["banana","apple"]//But looking at the keys that exist on the groceries array
// it is clear that both of those properties were added
// to the array.
console.log(Object.keys(groceries))
// Logs ["0","1","sorted","authored by"]//non-indexed properties don't affect the length of the array
console.log(groceries.length) // Logs 2//length is not "count" of items
groceries[9] = "chicken"
console.log(groceries.length) // Logs 10
```

**长度**
`length`数组的属性是另一个经常引起混淆的属性。它经常被误解为数组中的项数。然而，事实并非如此。值`length`在数值上大于最大数组索引。由于这种行为，非索引属性不会影响数组的`length`，如上面的代码示例所示。

另一个`length`可能导致混乱的场景是当一个条目被添加到一个比当前数组长度更高的索引时。请注意，在上面的代码示例中，在我向数组的索引 9 处添加了第三项之后，数组的长度从 2 跳到了 10。

每当`length`属性的值改变时，索引大于新的`length`的每个元素都被删除。

> 为了获得正确的长度值，您可以使用`*Object.keys(groceries).length*`。请记住，这也包括非索引属性，除非您将它们定义为不可枚举的。`*Object.defineProperty(groceries, "sorted", { value: false, enumerable: false, configurable: true, writable: true });*`

# 我应该用什么？

我试图遵循的一条一般经验法则是，如果需要存储不同类型的属性集合，请使用对象。否则，使用数组。