# 学习 JavaScript:用对象方法计算

> 原文：<https://levelup.gitconnected.com/learning-javascript-computing-with-object-methods-6bd194afd568>

![](img/e677d546fcb1c0470975848836a413ca.png)

乔安娜·科辛斯卡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 有一组内置方法，可以用于用户定义的对象。在本文中，我将讨论其中的几种方法，以及如何在 JavaScript 程序中使用它们。

# 对象.分配

方法用于将一个对象复制到另一个对象中。此方法的语法模板是:

*Object.assign(目标，源)；*

其中*源*是要复制的对象，而*目标*是要复制的对象。如果您想要分配目标对象，此方法将返回它。

下面是一个演示如何使用`Object.assign`的示例程序:

```
function Student(name, id, grades) {
  this.name = name;
  this.id = id;
  this.grades = grades;
}let st1 = new Student("",0,[]);
et st2 = new Student("Jane Doe", 123, [91, 92, 93]);
Object.assign(st1, st2);
print(`${st1.name}, ${st1.id}\n[${st1.grades}]`);
```

这个程序的输出是:

```
Jane Doe, 123
[91, 92, 93]
```

使用这种方法的一个很好的理由是确保新对象拥有旧对象的所有属性和值。当你写自己的方法时，你可能会不小心漏掉一些东西，而`Object.assign`会系统地确保所有的属性和值都被赋给新对象。

# 对象.创建

`Object.create`方法从现有的对象原型创建一个新对象。以下是该方法的语法模板:

*const | let | var object-name = object . create(existing-object)；*

让我们看几个例子，看看这种方法在实践中是如何工作的。第一个例子从一个函数创建一个新对象，然后使用`Object.create`创建第二个对象:

```
function Student(name, id, grades) {
  this.name = name;
  this.id = id;
  this.grades = grades;
}let st1 = new Student("Bob Green", 1234, [81, 77, 92]);
print(`${st1.name}, ${st1.id}\n${st1.grades}`);
let st2 = Object.create(st1);
print(`${st2.name}, ${st2.id}\n${st2.grades}`);
```

这个程序的输出是:

```
Bob Green, 1234
81,77,92
Bob Green, 1234
81,77,92
```

必须编写代码来更改新创建的对象的属性。

您也可以使用现有对象中的`Object.create`,如下例所示:

```
let Point = {x: null, y: null};
print(`x: ${Point.x}, y: ${Point.y}`);
let p1 = Object.create(Point);
p1.x = 1;
p1.y = 2;
print(`x: ${p1.x}, y: ${p1.y}`);
```

这个程序的输出是:

```
x: null, y: null
x: 1, y: 2
```

与`Object.assign`一样，使用该方法创建新对象确保了在创建新对象时使用现有对象的所有部分。

# 对象.条目

`Object.entries`方法以与 for-in 循环相同的方式返回对象的键/值对。以下是该方法的语法模板:

*[key，value]= object . entries(object)；*

下面是枚举对象的键和值的实际方法:

```
let stu1 = {
  name: "Jane Smith",
  id: 1234,
  grades: [88, 91, 77]
}for (let [key, value] of Object.entries(stu1)) {
  print(`${key}: ${value}`)
}
```

以下是该程序的输出:

```
name: Jane Smith
id: 1234
grades: 88,91,77
```

实现同样目的的另一种方法是使用数组解构和`forEach`方法，就像这样:

```
let stu1 = {
  name: "Jane Smith",
  id: 1234,
  grades: [88, 91, 77]
}
Object.entries(stu1).forEach(([key, value]) =>
                             print(`${key}: ${value}`));
```

使用这种方法可以确保对象的所有属性都被访问，尽管一个`for-of`循环也可以做到这一点，因为底层代码使用迭代器来访问对象中的所有属性。

# 对象. is

`Object.is` 方法比较两个对象以确定它们是否包含相同的值。以下是该方法的语法模板:

*布尔值 Object.is(object1，object 2)；*

下面是一个使用`Object.is`方法的示例程序:

```
let stu1 = {
  name: "Jane Smith",
  id: 1234,
  grades: [88, 91, 77]
}let stu2 = stu1;
if (Object.is(stu1, stu2)) {
  print("Same object.");
}
else {
  print("Different objects.");
}
stu2 = Object.assign(stu2, stu1);
if (Object.is(stu1, stu2)) {
  print("Same object.");
}
else {
  print("Different objects.");
}
stu2 = {
  name: "John Smith",
  id: 2345,
  grades: [91, 88, 77]
}
if (Object.is(stu1, stu2)) {
  print("Same object.");
}
else {
  print("Different objects.");
}
```

这个程序的输出是:

```
Same object.
Same object.
Different objects.
```

第一个比较是在通过从另一个对象赋值而创建的对象之间进行的，因此它们显然是同一个对象。第二个比较是在原始的学生对象和使用`Object.assign`方法创建的对象之间进行的，所以这两个对象也是相同的。最后一个比较是在两个对象之间进行的，这两个对象的属性中存储了完全不同的值，因此比较返回的对象是不同的对象。

# 对象.键

`Object.keys`方法返回一个包含对象键的数组。以下是该方法的语法模板:

*关键字数组 object . keys(object-name)；*

第一个示例从对象中提取键并显示结果数组:

```
let stu1 = {
  name: "Jane Smith",
  id: 1234,
  grades: [88, 91, 77]
}
let keys = Object.keys(stu1);
print(keys); // displays name,id,grades
```

虽然这不是特别有效，但我们可以使用这种方法来显示对象中的值，如下所示:

```
let stu1 = {
  name: "Jane Smith",
  id: 1234,
  grades: [88, 91, 77]
}
let keys = Object.keys(stu1);
for (key of keys) {
  print(key + ": " + stu1[key]);
}
```

这个程序的输出是:

```
name: Jane Smith
id: 1234
grades: 88, 91, 77
```

还有其他技术可以用来从对象中提取键，但是这个方法是一个更有效、更可靠的方法。

# 对象.值

`Object.values`方法返回一个包含存储在对象中的值的数组。以下是该方法的语法模板:

*值数组 object . values(object-name)；*

下面是一个演示这种方法如何工作的程序:

```
let stu1 = {
  name: "Jane Smith",
  id: 1234,
  grades: [88, 91, 77]
}
let values = Object.values(stu1);
print(values); // displays Jane Smith,1234,88,81,77
```

如果在不知道对象属性的情况下，要搜索某个对象中是否存储了特定的值，可能需要使用此方法。

# 对象. fromEntries

`Object.fromEntries`方法接受一个键/值对列表，并返回一个包含这些键/值对的对象。以下是该方法的语法模板:

*object Object.fromEntries(键/值对列表)；*

下面是一个演示`Object.fromEntries`如何工作的程序:

```
let point = [['x', 1], ['y', 2]];
let pntObj = Object.fromEntries(point);
print("x: " + pntObj.x + ", y: " + pntObj.y);
```

以下是该程序的输出:

```
x: 1, y: 2
```

当程序最终需要从一开始只是存储在列表中的数据创建一个对象时，这个方法很有用。

# 对象.冻结

`Object.freeze`方法使对象成为只读的，这意味着您可以检索对象的属性值，但不能更改它们。以下是该方法的语法模板:

*Object.freeze(对象)；*

下面的 shell 交互演示了`Object.freeze`的工作方式:

```
js> let point = {x: 1, y: 2};
js> point
({x:1, y:2})
js> point.x = 2;
2
js> point.y = 3;
3
js> point
({x:2, y:3})
js> Object.freeze(point);
({x:2, y:3})
js> point.x = 4;
4
js> point
({x:2, y:3})
js> point.y = 5;
5
js> point
({x:2, y:3})
```

一旦`Object.freeze`方法被应用到一个对象，你就可以访问这些值，但是不能改变它们。您可以从 shell 输出中看到，尝试更改冻结对象的值不会导致错误，但是值不会更改。

# 对象.定义属性

`Object.defineProperty`方法允许你在一个现有的对象上定义一个新的属性。以下是该方法的语法模板:

*object . define property(object，property-name，{value，[descriptor]})；*

其中可选描述符以几种方式定义属性。有关所有可能的描述符的列表，请参见关于 JavaScript 对象的 MDN 参考指南。本文末尾有一个链接。

下面是一个演示`Object.defineProperty` 方法如何工作的程序:

```
let student = {name: "Jane Smith", id: 1234};
print(`${student.name}, ${student.id}`);
Object.defineProperty(student, "grades", {
  value: [88, 71, 92],
  writable: true
});
print();
print(`${student.name}, ${student.id}\n${student.grades}`);
```

以下是该程序的输出:

```
Jane Smith, 1234Jane Smith, 1234
88,71,92
```

我演示了如何通过将属性的`writable`描述符设置为`true`来设置其中一个描述符。

# 更多对象方法

我在本文中介绍的方法只是 JavaScript 中最常见和最有用的一些对象方法。要查看它们并了解它们能做什么，请访问 MDN 对象方法参考页面[这里](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)。

感谢您的阅读，请将您的意见和建议发邮件至 mmmcmillan1@att.net[给我。如果你对我的在线编程课程感兴趣，请访问](mailto:mmmcmillan1@att.net)[https://learningcpp.teachable.com](https://learningcpp.teachable.com)。