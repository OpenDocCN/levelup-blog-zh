# ES2020 的新特性—模块、承诺和稳定的数组排序

> 原文：<https://levelup.gitconnected.com/new-features-of-es2020-modules-promises-and-stable-array-sorting-c3581ac740f7>

![](img/e6198e17752ed2c5c9711bb0de62cfb2.png)

照片由[i̇rem·图尔克坎](https://unsplash.com/@turkkanirem?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

自 2015 年以来，JavaScript 发展迅速，每次迭代都有许多新功能出现。JavaScript 语言规范的新版本每年都在更新，新语言特性提案的完成速度比以往任何时候都快。

这意味着新特性正以前所未有的速度融入现代浏览器和 Node.js 等其他 JavaScript 运行时引擎。2019 年，有许多新功能处于“阶段 3”阶段，这意味着它非常接近完成，浏览器现在开始支持这些功能。

如果我们想将它们用于生产代码，我们可以使用 Babel 之类的东西将它们转换成旧版本的 JavaScript，以便在需要时可以在旧版本的浏览器中使用，如 Internet Explorer。在本文中，我们将研究动态模块加载、稳定的排序结果和`Promise.allSettled`方法。

# 动态模块加载

JavaScript 模块可以动态加载。这让我们只在需要的时候加载模块，而不是在应用运行的时候加载所有的模块。

为此，我们使用了返回承诺的`import()`函数。当参数中的模块被加载时，承诺就实现了。

promise 解析为一个模块对象，然后可以被应用程序的代码使用。如果我们在`Person.js`中有了`Person`类，那么我们可以用下面的代码动态导入它:

```
import('./Person')
.then((module)=>{
  const Person = module.Person;
  const person = new Person('Jane', 'Smith');
  person.sayHi();
})
```

或者使用`async`和`await`语法，我们可以把它放在一个函数中:

```
const importPerson = async ()=>{ 
  const module = await import('./Person');
  const Person = module.Person;
  const person = new Person('Jane', 'Smith');
  person.sayHi();
}
importPerson();
```

正如我们所看到的，我们可以在代码中动态地导入一个模块，而不是像现在这样通常的静态导入。有了动态导入，我们可以随心所欲地导入它，而不是像我们通常做的那样，在一切运行之前在模块文件的顶部导入模块。

因为导入代码是静态的，所以我们可以用它来有条件地、迭代地或以任何其他我们想用代码做的方式导入。这是因为动态`import`函数返回一个承诺，我们可以用承诺允许的任何方式使用它们。

使用动态导入，我们还可以从常规脚本中导入模块，而不仅仅是在其他模块中，因此我们可以在脚本标记中编写如下内容:

```
<script type="module">
  const importPerson = async ()=>{ 
    const module = await import('./Person');
    const Person = module.Person;
    const person = new Person('Jane', 'Smith');
    person.sayHi();
  }
  importPerson();
</script>
```

上面代码中的一切都是动态运行的，包括`import`函数。这在`import`语句中是不可能的。由于模块名是以字符串的形式传入的，所以我们也可以传入任何字符串，而不仅仅是静态字符串，就像下面的代码一样:

```
(async () => { 
  const moduleNames = ['Person', 'Animal'];
  for await (const moduleName of moduleNames){
    const module = await import(`./${moduleName}`);
    const Thing = module[moduleName];
    const thing = new Thing('Jane', 'Smith');
    thing.sayHi();
  }
})();
```

在上面的代码中，我们异步遍历模块路径并动态导入它们，然后我们得到我们想要的类，然后用构造函数创建它的一个新实例。在每个迭代器的末尾，我们调用了在每个模块中导出的每个模块的类中可用的`sayHi`方法。

# 稳定排序

在此之前，JavaScript `sort`方法在不同的浏览器中实现是不同的。

这意味着`sort`算法可能会给出不同的排序结果，而这些结果本应是相同的。不同的浏览器对同一个`sort`方法返回不同的结果，并且传入了同一个用于比较的回调函数。

这就导致了一个我们没有预料到的结果。例如，如果我们有以下代码，先按名称，然后按`age`对`people`数组进行排序:

```
const people = [{
    name: 'Joe',
    age: 19
  },
  {
    name: 'Jane',
    age: 19
  },
  {
    name: 'John',
    age: 18
  },
  {
    name: 'Mary',
    age: 22
  },
  {
    name: 'Sam',
    age: 18
  },
];people.sort((p1, p2) => {
  if (p1.name < p2.name) return -1;
  if (p1.name > p2.name) return 1;
  return 0;
});
console.log(people.map(p => p.name));people.sort((p1, p2) => {
  return p1.age - p2.age
});
console.log(people.map(p => p.name));
```

在各种旧浏览器中，我们可能会得到不同的结果。然而，现在我们有不同的浏览器制造商同意他们的`sort`方法的稳定算法，所有的浏览器应该返回相同的排序结果，不管我们放入什么。

此外，我们为 browser 排序的数组的大小应该决定是否使用稳定的排序算法。

所有浏览器应该给出相同的结果，因为不管数组的大小如何，都使用稳定的排序算法，除非在早期的浏览器中，所使用的排序算法可能基于被排序的数组的大小，这增加了排序结果的不确定性。

从`console.log`语句中，我们应该得到:

```
[ "Jane", "Joe", "John", "Mary", "Sam" ]
```

对于第一个`console.log`，还有:

```
[ "John", "Sam", "Jane", "Joe", "Mary" ]
```

对于流行浏览器最新版本中的第二个`console.log`。

![](img/65a9d0b32410fbc851a94f2d3b687585.png)

照片由[莎伦·麦卡琴](https://unsplash.com/@sharonmccutcheon?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# `Promise.allSettled`

`Promise.allSettled`返回一个承诺，该承诺在所有给定的承诺被解决或拒绝后解决。它接受一个带有承诺集合的 iterable 对象，例如，一个承诺数组。返回承诺的解析值是每个承诺最终状态的数组。例如，如果我们有:

```
const promise1 = Promise.resolve(1);
const promise2 = Promise.reject(2);
const promise3 = new Promise((resolve, reject) => {
  setTimeout(() => reject(3), 1000);
});
Promise.allSettled([promise1, promise2, promise3])
  .then((values) => {
    console.log(values);
  })
```

然后，如果我们运行上面的代码，那么我们会得到一个包含 3 个条目的数组，每个条目都是一个对象，具有已履行承诺的`status`和`value`属性，以及一个对象，具有被拒绝承诺的`status`和`reason`属性。

比如上面的代码会记录`{status: “fulfilled”, value: 1}`、`{status: “rejected”, reason: 2}`、`{status: “rejected”, reason: 3}`。记录成功承诺的`fulfilled`状态，记录拒绝承诺的`rejected`状态。

# 结论

JavaScript 模块可以动态加载。这让我们只在需要的时候加载模块，而不是在应用运行的时候加载所有的模块。为此，我们使用了返回承诺的`import()`函数。

这让我们可以编写代码来有条件地导入、迭代导入以及 JavaScript 中异步代码允许的任何其他操作。在此之前，JavaScript `sort`方法在不同的浏览器中有不同的实现。

这意味着`sort`算法可能会给出不同的排序结果，而这些结果本应是相同的。

不同的浏览器对相同的`sort`方法返回不同的结果，相同的回调函数用于传入比较。这导致了我们没有预料到的结果。现在我们再也不用担心这个了。

`Promise.allSettled`返回一个承诺，该承诺在所有给定的承诺被解决或拒绝后解决。

它接受一个带有承诺集合的 iterable 对象，例如，一个承诺数组。返回承诺的解析值是每个承诺最终状态的数组。

现在我们可以等待承诺的解决，然后做我们想做的任何事情。