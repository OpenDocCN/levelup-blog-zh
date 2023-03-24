# 6 个用于解决常见问题的 JavaScript 代码片段

> 原文：<https://levelup.gitconnected.com/6-javascript-code-snippets-for-solving-common-problems-33deb6cacef3>

![](img/39dae15e1363fd5186e840106a7b27ae.png)

Joan Gamell 在 [Unsplash](/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

像许多编程语言一样，JavaScript 有其缺陷和怪癖。你认为应该有的内置函数或语法并不存在。即使是最基本的输入输出也可能近乎疯狂(什么是`true + true`？).即使是像循环这样的基本东西，其行为也可能与预期不同。

尽管 JavaScript 有时表现得很奇怪，但它仍然是当今使用最广泛的语言之一。有时候，我们需要的只是对每天重复的简单任务的一点帮助。我列出了一些常见的问题，以及它们在 JS 中相应的解决方案。让我们开始吧。

*注意:这些例子中有许多使用了 ES6，并不是普通的 JavaScript。根据您正在使用的框架，您可能会也可能不会使用 ES6 语法。查看* [*此链接*](https://www.w3schools.com/Js/js_es6.asp) *获取关于 ES6 的更多文档。*

## 1.在对象数组中查找特定对象

这可以说是你在 JS 世界中需要完成的最常见的任务之一。遍历对象数组以找到特定的一个。`find`方法是我们这里的朋友。只需使用匿名函数作为参数插入选择标准，就万事俱备了:

```
let customers = [
  { id: 0, name: 'paul' },
  { id: 1, name: 'jeff' },
  { id: 2, name: 'mary' }
];let customer = customers.find(cust => cust.name === 'jeff');console.log(customer);--> { id: 1, name: 'jeff' }
```

## 2.在对象的键和值上循环

有时，您的数据结构可能是一个包含一组键/值对的复杂对象。根据你习惯的语言，对每一对进行迭代乍一看有点奇怪，但是一旦你习惯了使用`Object`的函数，这就很简单了。

在你抓取了对象的键之后，你可以同时遍历这些键和值。在这个例子中，你可以在循环中使用`key`和`value`变量访问每一对。

```
let myObject = { one: 1, two: 2, three: 3 };Object.keys(myObject).forEach((key, value) => {
  //...do something
  console.log(key, value);
});
```

## 3.基于条件筛选对象数组

如果您有大量数据，并希望根据特定条件过滤出项目，那么您可以简单地使用`filter`函数。在这个例子中，我们有一个文件路径数组。有些文件在*【目录 1】*中，而有些文件在*【目录 2】*中。假设我们只想过滤特定的目录:

```
let data = [
  "files/dir1/file",
  "files/dir1/file2",
  "files/dir2/file",
  "files/dir2/file2"
];let filteredData = data.filter(path => path.includes('dir2'));console.log(filteredData);--> [ 'files/dir2/file', 'files/dir2/file2' ]
```

在上面的路径数组中过滤特定的目录很容易。通过指定路径字符串必须包含字符串*‘dir 2’*，您将过滤掉任何不包含*‘dir 2’*的路径。请记住，无论您传递给 filter 的是什么函数，都必须返回`true`,以便将该项包含在结果中。

## 4.析构变量赋值

从一个数组中一个接一个地分配变量既费时又愚蠢。只需使用[析构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)来更快更容易地完成这项工作:

```
let profile = ['bob', 34, 'carpenter'];let [name, age, job] = profile;console.log(name);--> 'bob'
```

## 5.将关键帧指定给具有相同名称的对象

向对象分配键时，如果键与保存要分配的值的变量同名，则可以完全忽略值分配。这可以防止你不得不重复自己，这是我们都讨厌做的事情。让我们来看一个例子，它使用了上一个例子中的一些相同数据:

```
let name = 'bob';
let age = 34;
let job = 'carpenter';// instead of this
let myObject1 = { name: name, age: age, job: job };// do this
let myObject2 = { name, age, job };console.log(myObject2);--> { name: 'bob', age: 34, job: 'carpenter' }
```

## 6.使用扩展运算符

spread 操作符允许您完全“展开”一个数组。这可以用来将一个数组转换成一个参数列表，甚至可以将两个数组组合在一起。你也可以用它来形成一个函数的参数列表。看看这个:

```
let data = [1,2,3,4,5];console.log(...data);--> 1 2 3 4 5let data2 = [6,7,8,9,10];let combined = [...data, ...data2];console.log(...combined);--> 1 2 3 4 5 6 7 8 9 10console.log(Math.max(...combined));--> 10
```

在第一个例子中，我们展示了 spread 操作符如何在一个数组上工作，并将每一项变成一个单独的元素。第二个示例通过创建一个包含两个内容的新临时数组，将两个数组的内容组合在一起。最后一个例子说明了 spread 操作符如何将一个数组转换成一个函数的参数列表。`Math.max`返回传递给它的参数列表中最大的数字。其中一个争论是最高的`10`。

*感谢阅读！我希望您喜欢学习这些常见的 JavaScript 片段，以便更快地处理那些频繁且通常令人厌烦的任务。*

[](https://skilled.dev) [## 编写面试问题

### 掌握编码面试的过程

技术开发](https://skilled.dev)