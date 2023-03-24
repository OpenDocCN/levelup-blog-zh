# 我的 5 大 JavaScript 技巧和窍门来编写更干净的代码

> 原文：<https://levelup.gitconnected.com/my-top-5-javascript-tips-tricks-for-writing-cleaner-code-4ee60fef4b8>

![](img/00f6eef8b8219e4ecd5d5044b796dc3f.png)

# 1.解构分配

析构赋值允许将一个或多个对象属性赋给单个表达式中的变量。创建的变量将与属性同名。

```
let myObj = {
  id: 1,
  name: 'My Object'
};// without destructuring assignment
let id = myObj.id;
let name = myObj.name; 
// id = 1, name = 'My Object'// with destructuring assignment
let { id, name } = myObj;
// id = 1, name = 'My Object'
```

当您知道您需要使用一个对象的多个属性，您需要多次使用同一个属性，或者您希望使用的属性深深地嵌套在该对象中时，这是非常有用的。在所有这些情况下，使用析构赋值可以使你从通过链接获取对象属性的混乱中解脱出来，并使你的代码更加简洁易读。

例如，我最近一直在做[传单](https://leafletjs.com/)的工作，这是一个用于构建交互式地图的 Javascript 框架。它是高度可定制的，允许你在地图上给不同的标记分配你自己的属性。然而，访问这些属性会变得有些混乱——我们可以用析构赋值来解决这个问题。

```
// without destructuring assignment
function onEachFeature (feature, layer) {
  if (feature.properties.hasPopup) {
    let popupContent = `<a href="/feature/${feature.properties.id}">${feature.properties.name}</a>`;
    layer.bindPopup(popupContent);
  }
}// with destructuring assignment
function onEachFeature (feature, layer) {
  let { hasPopup, id, name } = feature.properties;if (hasPopup) {
    let popupContent = `<a href="/feature/${id}">${name}</a>`;
    layer.bindPopup(popupContent);
  }
}
```

我们可能添加了一行额外的代码，但是我相信这会让我们更清楚、更容易理解这个函数的意图。

还可以析构数组，这允许您将数组中的一个或多个元素赋给变量。然而，我个人并不经常使用这种语法，所以我不会在这里进一步讨论它。如果您希望了解更多信息，请参见 [MDN 参考](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)。

最后，如果你使用的函数有一个对象作为参数，那么在参数列表中析构这个对象是可能的。这使您不必亲自显式声明变量，并清楚地知道函数需要哪些属性。

```
function logPerson(person) {
  let { name, age } = options;

  console.log(`${name} is ${age} years old`);
}function logPerson({ name, age }) {
  console.log(`${name} is ${age} years old`);
}
```

# 2.短路评估和分配

JavaScript 逻辑运算符 AND (&&)和 OR (||)被称为短路运算符，因为它们只在必要时计算表达式，以确定布尔表达式的结果。

例如，和要求表达式的两端都计算为 true。因此，如果表达式的左侧计算结果为 false，它就不会检查右侧，因为这是浪费时间。

同样，OR 要求表达式中只有一边的计算结果为 true。因此，如果左手边的计算结果为 true，它就不会检查右手边。

这种短路对于给涉及对象的表达式增加一些安全性是有用的。例如，考虑以下函数:

```
function logIfAdult(person) {
  if(person.age >= 18) {
    console.log("Person is an adult");
  }
}
```

这种实现的问题是您不能保证 person 对象不为空。如果使用 null person 运行这个函数，将会得到下面的错误:`Uncaught TypeError: Cannot read property 'age' of null`。

由于短路评估，我们可以像这样增加一些安全性:

```
function logIfAdult(person) {
  if(person && person.age >= 18) {
    console.log("Person is an adult");
  }
}
```

这是因为，如果 person 为 null，它将计算为 false(这是因为 null 是一个“false”值，如果这个概念对您来说是新的，[也请阅读这篇文章](https://samwalpole.com/javascript-short-circuit-assignment))，整个表达式将短路。只有当 person 不为 null 时，表达式才会继续检查表达式的右边，这时我们知道检查是安全的，不会出现任何错误。

我们也可以在分配变量时利用这种短路。例如，考虑以下函数:

```
function logName(person) {
  let name = person && person.name;
  console.log(name);
}logName({ name: 'Sam' });
// logs 'Sam'logName(null)
// logs 'null'
```

这里发生了什么事？在第一个例子中，我们向函数传递了一个有效的 person 对象。因为 person 对象不为空，所以 AND 操作符移到表达式的右边，并将 [person.name](http://person.name) 的值赋给 name 变量。在第二个示例中，person 为 null，因此表达式短路并向 name 变量返回 null。

我们可以进一步扩展它，记录一个默认名称，而不仅仅是 null。这次我们使用 OR 操作符，所以如果 person 对象为空，我们将只使用默认值。

```
function logName(person) {
  let name = person && person.name || 'Default Name';
  console.log(name);
}logName({ name: 'Sam' });
// logs 'Sam'logName(null)
// logs 'Default Name'
```

# 3.可选的链接和无效合并运算符

短路计算和赋值是如此普遍，以至于 JavaScript 中加入了新的更简洁的语法来达到同样的目的。这些是可选的链接和无效合并操作符。我已经决定包括短路和可选的链接/空合并，因为在撰写本文时，后者是较新的特性，可能不完全兼容旧的浏览器。

可选的链接运算符(？。)允许您深入到对象中，而不必显式地检查对象是否为空。如果对象为空，那么表达式将返回 undefined，而不是抛出一个错误。例如，使用可选链接，上面的 logIfAdult 函数可以重写为:

```
function logIfAdult(person) {
  if(person?.age >= 18) {
    console.log("Person is an adult");
  }
}
```

零化合并算子(？？)用于在表达式左侧的值为空时返回默认值。这样，它取代了上面 logName 函数中 OR 运算符的功能:

```
function logName(person) {
  let name = person?.name ?? 'Default Name';
  console.log(name);
}
```

# 4.命名回调函数

匿名函数真的很有用——你可以在任何时候任何地方声明它们，如果你只需要一次性的函数，那就太好了。

```
let people = [
  {
    id: 1,
    firstName: 'Sam',
    lastName: 'Walpole',
  },
  ...
];let viewModels = people.map(p => ({
  id: p.id,
  name: `${p.firstName} ${p.lastName}`,
}));
// viewModels = [{ id: 1, name: 'Sam Walpole' }]
```

但是，因为函数没有名字，所以您将它留给未来的开发人员来解决回调函数中的代码做什么——这在这里是可以的，但是在更长、更复杂的函数中，这可能会浪费不必要的时间。通过首先将函数声明为命名函数，您可以立即使代码更具可读性，并为未来的开发人员提供一些关于函数意图的线索。

```
let people = [
  {
    id: 1,
    firstName: 'Sam',
    lastName: 'Walpole',
  },
  ...
];let toViewModel = p => ({
  id: p.id,
  name: `${p.firstName} ${p.lastName}`,
});let viewModels = people.map(toViewModel);
// viewModels = [{ id: 1, name: 'Sam Walpole' }]
```

# 5.枚举/词典

枚举是将一组常数值存储为类型的一种方式。大多数语言都有对枚举的内置支持，但是在 JavaScript 中，我们必须使用对象自己构建它们。

```
const Color = {
  RED: 'RED',
  GREEN: 'GREEN',
  BLUE: 'BLUE',
};let redCar = {
  make: 'Ferrari',
  model: '812',
  color: Color.RED,
};let greenCar = {
  make: 'Aston Martin',
  model: 'Vantage',
  color: Color.GREEN, 
};
```

枚举与用于流控制的 switch 语句很好地配对:

```
function getHexColor(car) {
  switch (car.color) {
    case Color.RED:
      return '#ff0000';
    case Color.GREEN:
      return '#00ff00';
    case Color.BLUE:
      return '#0000ff';
  }
}
```

然而，有时这可能有点冗长。我们可以使用字典来代替 switch 语句。JavaScript 中的字典的声明方式与枚举非常相似，但是从概念上讲，它们有不同的用途。枚举是一组常量值，字典是键/值对的集合。

```
function getHexColor(car) {
  let hexColors= {
    [Color.RED]: '#ff0000',
    [Color.GREEN]: '#00ff00',
    [Color.BLUE]: '#0000ff',
  };return hexColors[car.color];
}
```

在上面的例子中，我们已经不需要 switch 语句了，因为我们已经创建了一个字典，用 enum 值作为键，用十六进制颜色作为值。通过去除 switch 语句中的所有混乱，我相信这会使代码更容易阅读。

# 结论

在这篇文章中，我提供了 5 个我经常在 JavaScript 中使用的技巧，让我的代码更加简洁易读。我希望您已经发现它们是有帮助的，并将找到机会在您自己的代码中使用它们。

我发布的大部分内容都是关于全栈的。NET 和 Vue web 开发。为了确保你不会错过任何帖子，请关注这个博客并[订阅我的简讯](https://samwalpole.com)。如果你觉得这篇文章有帮助，请喜欢它并分享它。你也可以在[推特](https://twitter.com/dr_sam_walpole)上找到我。

【https://samwalpole.com】最初发表于[](https://samwalpole.com/my-top-5-javascript-tips-and-tricks-for-writing-cleaner-code)**。**