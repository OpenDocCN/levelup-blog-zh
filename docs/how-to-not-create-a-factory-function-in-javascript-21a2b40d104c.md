# 如何(不)在 JavaScript 中创建工厂函数

> 原文：<https://levelup.gitconnected.com/how-to-not-create-a-factory-function-in-javascript-21a2b40d104c>

## 数据对象、封装对象、克隆、闭包等等

![](img/f0ad7dc62d3e064eee7291cf1ca556ef.png)

照片由 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [Dariusz Sankowski](https://unsplash.com/@dariuszsankowski?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

在本文中，我们将研究如何用 JavaScript 创建工厂函数(也称为 Crockford 构造函数)。工厂函数是类的替代品，为了更广泛地比较它们，你可以看看[类与工厂函数:探索前进的道路](https://medium.com/programming-essentials/class-vs-factory-function-exploring-the-way-forward-73258b6a8d15?source=your_stories_page-------------------------------------)。

# 数据对象与面向对象对象

在讨论一些例子之前，我们必须区分数据传输对象和 OOP 对象。

数据传输对象是关于公开他们的数据。它们包含原语和其他[对象](https://medium.com/dailyjs/7-differences-between-objects-and-maps-in-javascript-bc901dfa9350)。如果它们包含任何方法，它们都是关于从现有方法中计算派生数据的。

OOP 对象是关于隐藏数据和公开一小部分处理数据的公共方法。

也就是说，让我们看看我遇到的一些有问题的工厂函数的例子并讨论它们。

# 可疑示例 1

您可能会发现的第一个示例如下所示。它从一个[对象](https://medium.com/dailyjs/15-fundamentals-you-should-know-on-javascript-objects-90f57cc9d78d)中提取一些属性，然后创建具有相同属性的新对象。

```
const createPerson = ({ fname, lname }) => ({
  fname,
  lname
});
```

首先要注意的是，这种例子是关于创建一个新的普通数据对象。从我们可以看到，它只是克隆现有的对象或创建一个与初始道具子集的副本。

如果我们想克隆一个简单的对象，我们可以简单地使用扩展操作符。除了函数名之外，我看不出创建一个新函数有什么额外的价值。

```
const obj = { fname: 'Jhon', lname: 'Locke' };
const clone = { ...obj }
```

还要注意，在这种例子中，属性是原语或普通数据对象，而不是函数。

# 可疑示例 2

下面是你可能遇到的另一个类似的例子。

```
function person(fname, lname) {
  const person = {};
  person.fname = fname;
  person.lname = lname; 
  return person;
}
```

这与函数创建普通对象的想法是一样的。

这一次我们没有输入对象，只有一些值，我们想从这些值创建一个新的对象。

这种[对象](https://medium.com/dailyjs/15-fundamentals-you-should-know-on-javascript-objects-90f57cc9d78d)也可以使用对象的简写符号动态完成。下面是一个例子。

```
const fname = 'Jhon';
const lname = 'Locke'const obj = { fname, lname };
```

同样，除了函数名，我看不出使用新函数的价值。我真的不理解这种方法的附加价值，称之为模式。

# 可疑示例 3

这是你可能遇到的另一个例子。

```
function person(fname, lname){
  const fullname = () => `${fname} ${lname}`;
  return {
    fname,
    lname,
    fullname
  }
}const person1 = person('John', 'Locke');
person1.fullname();
//John Locke
```

这一次有所不同，新对象有了方法。这种例子开始增加一些价值。我们正在创建一个新的对象，但也添加了一些方法来检索数据。

请注意，这些方法只不过是计算导数数据。我们仍在构建数据结构。

这些种类的[对象](https://betterprogramming.pub/did-you-know-that-almost-everything-is-an-object-in-javascript-f06c3f69faf1)仍然是关于传输公共数据的。我会说，我们可以添加包含结果的计算属性，而不是在它们上面创建新方法。当这样做时，所有使用数据对象的代码都不必再次计算派生的数据。这些数据传输对象无论如何都应该是不可变的，这样计算出的值就不会改变。

```
function person(fname, lname){
  const fullname = `${fname} ${lname}`;
  return {
    fname,
    lname,
    fullname
  }
}const person1 = person('John', 'Locke');
person1.fullname;
//John Locke
```

# 可疑示例 4

您可能注意到的另一个示例如下所示。

```
function createPerson(name) {
    function getName() {
        return name;
    }

    return Object.freeze({
        name,
        getName
    });
}const person = createPerson('John Locke');
console.log(person.name)
```

在这种情况下,`getName`方法毫无意义。它只不过返回与`name`属性相同的数据。我们仍在构建数据结构。这个例子唯一的好处是，它指出了通过在返回的对象上使用`Object.freeze`，普通的传输对象应该是不可变的。

# 工厂功能

所有以前的例子都遗漏了一些东西。他们正在构建数据结构，这意味着[对象](https://betterprogramming.pub/did-you-know-that-almost-everything-is-an-object-in-javascript-f06c3f69faf1)及其所有的公共属性。

创建 OOP 对象的主要思想是隐藏数据和公开公共方法。开创这种模式的道格拉斯·克洛克福特强调，构造函数返回一个包含函数的对象，而不是数据。

以下是你如何做到这一点。

下面是一个管理人员列表的商店。人是一个数据传输对象。`PersonStore`是工厂功能。它创建了一个[对象](https://medium.com/dailyjs/7-differences-between-objects-and-maps-in-javascript-bc901dfa9350)，隐藏了条目列表并公开了使用它的公共方法。

```
function PersonStore(){
  let items = [];

  function add(person){
    items.push(person);
  }

  function removeById(id){
    items = items.filter(p => p.id !== id);
  }

  function getItems(){
    return Object.freeze([...items]);
  }

  return Object.freeze({
    add,
    removeById,
    getItems
  });
}const store = PersonStore();
store.add({id:1, name: 'John Lock'});
store.add({id:2, name: 'Jack Shephard'});
store.add({id:3, name: 'James "Sawyer" Ford'});store.removeById(3);
store.getItems();
//[
//{id: 1, name: "John Lock"} 
//{id: 2, name: "Jack Shephard"}
//
```

现在我们已经看到了模式，有一些事情需要指出。如前所述，它返回一个包含函数的对象。它不返回包含原语或其他对象的对象。

返回的对象被冻结，所以我们不能改变公共方法的定义。在 JavaScript 中，默认情况下对象是动态的，这意味着我们可以在创建后更改它们的所有属性。该存储区仅公开方法。通过冻结它，我们不能从外部代码中改变这些方法。

```
store.add = function(){ console.log('do nothing'); }
//Cannot assign to read only property 'add' of object
```

`items`变量是私有的。它不能从客户端代码中更改。

```
store.items = [];
//Cannot add property items, object is not extensible
```

在 JavaScript 中，我们可以在其他函数中定义函数。`removeById`、`add`、`getItems`是在`PersonStore`函数中定义的函数。

内部函数可以从父函数中访问变量。事实上，这三个方法都是从它们的父级访问`items`变量的。

即使在父函数执行之后，内部函数也可以访问这些变量。当然，在执行完`PersonStore`之后，这三个方法都会访问`items`变量。

这三个方法`removeById`、`add`、`getItems`都是闭包。

正如您已经注意到的，冻结这个 OOP 对象的返回值是一个好主意。我们这样做是因为一旦引用超出了封装的对象，外部代码就可以用它来改变 OOP 对象的内部状态。本质上，从这种封装对象中检索到的数据应该被冻结并用于只读。`getItems`函数返回隐藏数组的冻结副本。

```
function getItems(){
  return Object.freeze([...items]);
}
```

以上就是我们如何用 JavaScript 创建工厂函数的全部内容。关于为什么使用这种模式的更多信息，你可以查看[类与工厂函数:探索前进的道路](https://medium.com/programming-essentials/class-vs-factory-function-exploring-the-way-forward-73258b6a8d15)和[没有“this”的 JavaScript 看起来像一种更好的函数式编程语言](https://medium.com/programming-essentials/removing-javascripts-this-keyword-makes-it-a-better-language-here-s-why-db28060cc086)。

感谢阅读。