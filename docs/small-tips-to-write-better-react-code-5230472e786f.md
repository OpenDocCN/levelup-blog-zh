# 编写更好的 React 代码的小技巧

> 原文：<https://levelup.gitconnected.com/small-tips-to-write-better-react-code-5230472e786f>

## 更好的编程

## 了解如何使用一些 JavaScript 特性来清理代码

![](img/d5f1a464e496c8cd03205b4a1cf5d6aa.png)

照片由[埃米尔·佩龙](https://unsplash.com/@emilep?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/simple-code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

今天我们将讨论一些我最喜欢的技巧，它们非常容易实现或遵循，并且可以让你的 JavaScript 代码更干净。还要记住，我们今天要学习的一些内容通常适用于 JavaScript，尽管本文将重点关注 React。

# 对象析构

首先，我们将回顾对象析构，这实际上是我最喜欢的方法之一，它有助于保持代码短小、整洁和优雅。我非常喜欢这个话题，所以我在这里发了一整篇文章:

[](https://medium.com/better-programming/write-cleaner-code-by-using-javascript-destructuring-cd6b55c25bac) [## 使用 JavaScript 析构编写更简洁的代码

### 通过在 JavaScript 中析构对象和数组，增加代码的清晰度并降低代码的复杂性

medium.com](https://medium.com/better-programming/write-cleaner-code-by-using-javascript-destructuring-cd6b55c25bac) 

析构允许你将复杂的结构分解成简单的部分。让我们来看一个例子:

```
const { title } = props
console.log(title);
```

React 开发人员使用这种技术的一个常见地方是 props。虽然有些人可能会认为在拆分变量时会丢失上下文，但在 React 中，上下文通常是由组件本身继承的。让我们看一个例子来说明我的意思。

首先，让我们编写一个简单的组件在屏幕上显示任务信息:

```
function TaskView(props) {
    return (
        <h1>{props.task.title}</h1>
        <p>{props.task.description}</p>
        <span>{props.task.completed ? 'Completed' : 'Pending'}</span>
    )
}
```

这确实很简单，但是，看看我们是如何一直重复道具的，不是很漂亮。让我们看看实现这一点的另一种方法:

```
function TaskView(props) {
    const task = props.task
    return (
        <h1>{task.title}</h1>
        <p>{task.description}</p>
        <span>{task.completed ? 'Completed' : 'Pending'}</span>
    )
}
```

好一点了，但是我们在任何地方都有任务。现在，一些可能不知道析构的人可能会尝试这样做:

```
const title = props.task.title
const description = props.task.description
```

这给声明增加了太多的开销。现在让我们看看使用析构时组件是什么样子的。

```
function TaskView(props) {
    const { title, description, completed } = props.task
    return (
        <h1>{title}</h1>
        <p>{description}</p>
        <span>{completed ? 'Completed' : 'Pending'}</span>
    )
}
```

现在代码非常简单，我们保持 JSX 与其他部分非常干净，我们仍然在上下文中。完全可以理解的是，当我们说`title`时，我们谈论的是`Task`，因为这是组件的全部内容。因此，保持你的名字干净，组织好你的组件，你会喜欢这个特性的。

# 简化你的条件语句

在这一节中，我想谈谈 3 种不同的场景，它们可以帮助我们提高代码的可读性，这非常简单，尽管很多时候我们会忘记这样做。

## 条件执行

在某些时候，只有当某个条件恰好为真时，我们才需要运行一个语句，这是很正常的。通常是这样的:

```
const isFive = (num) => num === 5
if (isFive(5)) {
    console.log('It is the number five!')
}
```

现在，这段代码本身没有任何问题，但是，它可以简化一点:

```
const isFive = (num) => num === 5
isFive(5) && console.log('It is the number five!')
```

很好，但是它是如何工作的呢？JavaScript 和许多其他语言一样，按照从左到右的顺序读取条件语句，如`&&`或`||`，当它们可以使参数无效时就退出。

让我们看一个所有条件句的例子:

```
const t = 1
t === 1 && t === 2 && t === 3
```

在那个例子中，JS 将首先获取第一个表达式`t === 1`，因为那个表达式是真的，并且我们有一个`and`条件，它需要评估下一个表达式，因为我们需要保证它们都是真的。当它对`t === 2`求值时，它根本不需要对`t === 3`求值，因为我们知道整个语句恰好是`false`。

太神奇了！现在让我们了解更多这方面的知识。这种例子在互联网上很常见，但是，你知道你也可以使用`||`操作符吗？

```
const isFive = (num) => num === 5
isFive(5) || console.log('It is the number five!') // does not execute the console.log
isFive(10) || console.log('It is not the number five!') // it executes the console.log
```

你注意到了吗，我们刚才做的等同于对我们的第一个例子应用一个 not。

```
const isFive = (num) => num === 5
isFive(5) && console.log('It is the number five!') // it executes the console.log
!isFive(10) && console.log('It is not the number five!') // it executes the console.log
```

## 三元运算符

条件(三元)操作符是唯一接受三个操作数的 JavaScript 操作符:一个条件后跟一个问号(？)，然后是条件为 true 时要执行的表达式，后跟冒号(:)，最后是条件为 falsy 时要执行的表达式。

这通常用于根据条件语句向用户显示不同的状态或组件。虽然我并不总是推荐使用三元运算符，但有时这是一种很好的老方法，可以很好地完成工作。它对小东西非常有用。

看看下面的例子:

```
if (completed) {
    return 'Completed'
} else {
    return 'Pending'
}
```

另一种变化，我仍然在周围看到的是:

```
if (completed) { return 'Completed'} else { return 'Pending' }
```

我不是来评判的，但那会变得很混乱。让我们来看看使用三元运算符的一种方法

```
return completed ? 'Completed' : 'Pending'
```

好多了！

## 可选链接

最后但并非最不重要的是，我们有可选的链接(`?.`)，它允许读取位于连接对象链深处的属性值，而不必明确验证每个引用。

简单地说，这有助于避免仅仅为了确保我们在嵌套属性上有一个值而使用一堆`if`语句。让我们看一个例子:

```
const juan = {
    name: 'Juan',
    marriedTo: {
        name: 'Diana'
    }
}console.log(juan.marriedTo.name) // Diana
console.log(juan.divorcedFrom.name) // Cannot read property 'name' of undefined
```

Ups…当我们试图访问与我们离婚的人的名字时，我们得到一个错误，因为`divorcedFrom`在我们的例子中是未定义的。通常我们会这样解决它:

```
if (juan.divorcedFrom) {
    console.log(juan.divorcedFrom.name)
}
```

但是，如果仅仅为了这个目的而增加许多“如果”,那也可能会失去控制。使用可选链接有一个更好的方法。

```
const juan = {
    name: 'Juan',
    marriedTo: {
        name: 'Diana'
    }
}console.log(juan.marriedTo?.name) // Diana
console.log(juan.divorcedFrom?.name) // undefined
```

这适用于多个层面

```
juan.marriedTo?.disvorcedFrom?.kids
```

非常好！让我们继续下一个话题。

# 传播算子

没有不使用 spread 操作符的 react 应用程序，这可能有些夸张，但是 spread 操作符在 React 应用程序中被广泛使用，尤其是在使用 reducers 时，尽管它不仅仅是这样。这是我在文章中广泛涉及的另一个主题:

[](https://medium.com/@bajcmartinez/how-to-use-the-spread-operator-in-javascript-3aff104adb71) [## 如何在 JavaScript 中使用扩展运算符(…)

### ES6 详细传播算子，它是如何工作的，好的，坏的，丑陋的

medium.com](https://medium.com/@bajcmartinez/how-to-use-the-spread-operator-in-javascript-3aff104adb71) 

我真的推荐你读读它，它很酷，而且详细地涵盖了这个话题。

spread 操作符允许您将一个 iterable 对象展开为它的单个元素的列表。让我们来看一些例子:

```
function sum(x, y, z) {
  return x + y + z
}const numbers = [1, 2, 3]console.log(sum(...numbers)) // 6
```

在这种情况下，我们所做的是将一个`array`转换成单独的变量，并传递给我们的`sum`函数。这是一个非常巧妙的技巧。但是我们也可以把它应用到物体上:

```
const obj1 = { foo: 'bar', x: 42 }
const obj2 = { foo: 'baz', y: 13 }const copyObj1 = { ...obj1 } // This copies all the properties of obj1 into a new object.const merged = { ...obj1, ...obj2 } // This merges all the properties of obj1 and obj2 into a new object.console.log(merged) // {foo: "baz", x: 42, y: 13}
```

因为我们可以用它来创建新的对象或数组，所以使用 Redux 是理想的，因为我们可以避免改变原始对象。

# 模板文字

虽然很受欢迎，对初学者也很友好，但是没有它们，任何列表都是不完整的。模板文字基本上是字符串，但不是任何字符串，它们允许嵌入表达式。让我们来看看。

```
console.log(`this is a string literal`)
```

在其更基本的形式中，字符串文字只是一个字符串，但是，注意，要成为字符串文字，它必须使用`\`` instead of` " `or`' `。这是一个小细节，但却有着巨大的影响。

例如，字符串文字支持多行字符串:

```
console.log(`line 1
line 2`)
```

或者你也可以嵌入表达式

```
const a = 10
const b = 25console.log(`a: ${a} and b: ${b} but more importantly a+b = ${a+b}`) // a: 10 and b: 25 but more importantly a+b = 35
```

真的很酷！

# 结论

JavaScript 包含有用的操作符、表达式和技巧，可以增强我们的开发技能，编写更简洁的代码。我提到的一些事情可能是个人判断，这也是事实，但是如果你看一看在非常受欢迎的项目上编写的 React 代码，你会看到他们到处都在应用这些小东西。因此，当您编写下一个 React 组件时，学习和实现它们真的很好。

感谢阅读