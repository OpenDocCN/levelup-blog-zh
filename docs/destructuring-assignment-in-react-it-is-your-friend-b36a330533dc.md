# React 中的析构赋值:它是你的朋友。

> 原文：<https://levelup.gitconnected.com/destructuring-assignment-in-react-it-is-your-friend-b36a330533dc>

![](img/d7bd451ec94b631a8bd67bb3a7f426c1.png)

有大量的信息可以解释什么是[析构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)以及如何使用它。然而，在本文中，我将给出一个简单的例子来说明它是如何工作的，并展示它在 React 应用程序中的有用性。

简单地说，析构赋值是一种定制如何访问值以供使用的方法。它是一个 JavaScript 表达式，允许从数组中提取值或从对象中提取属性，并将值或属性作为不同的变量进行赋值。下面是一个数组析构的例子:

```
let peanuts = ['Snoopy', 'Linus', 'Lucy']let [snoopy, linus, lucy] = peanutsconsole.log(snoopy) //=> 'Snoopy'
console.log(linus) //=> 'Linus'
console.log(lucy) //=> 'Lucy'
```

首先，声明一个名为`peanuts`的变量，并赋予它一个包含三个字符串的数组:`['Snoopy', 'Linus', 'Lucy']`。第二，声明一个析构表达式，一个包含三个变量的数组:`[snoopy, linus, lucy]`，并赋予名为`peanuts`的变量，该变量本身包含前面提到的字符串数组。第三，变量被记录到控制台并产生它们包含的值:来自`peanuts`数组的字符串。

实际上，`peanuts`数组的单个值被分配给唯一的变量，这些变量可以自主使用，同时仍然引用一个已建立的源。注意，因为析构赋值提供了变量，所以可以使用不同的变量名。例如，`[snoopy, linus, lucy]`可以用`[dog, bestFriend, complicatedCrush]`替换，并产生相同的结果。

析构赋值的一个重要作用是在 React 应用程序中。React 使用一个叫做`props`的便利工具在组件之间发送数据。如果这句话对你毫无意义，那就去了解一下[反应](https://reactjs.org/)！Props 是一个对象，通常从父组件传递到子组件。

假设有一个名为`<Peanuts/>`的父组件和一个名为`<Gang/>`的子组件。为了突出析构赋值所提供的冗长性的减少，这两个组件都是类组件。`<Peanuts/>`将是一个容器组件，`<Gang/>`将是一个表示组件。`<Peanuts/>`将包含一个花生角色列表作为对象，该对象将作为道具传递给`<Gang/>`，T5 将呈现角色的名字。

```
// Peanuts component ///////////////////////////////////////////////import React, {Component} from 'react'
import <Gang/> from './Gang'class Peanuts extends Component { const peanutsGang = {
    aBoy: 'Charlie Brown',
    sister: 'Sally Brown',
    dog: 'Snoopy',
    bestFriend: 'Linus van Pelt',
    complicatedCrush: 'Lucy van Pelt',
    pianoMan: 'Schroeder'
  } render() {
    return (
      <Gang peanutsGang={peanutsGang}/>
    )
  }}export default Peanuts// Gang component //////////////////////////////////////////////////import React, {Component} from 'react'class Gang extends Component {

  render() {
    return (
      <div>
        <h1>The Peanuts Gang!</h1>
        <ul>
          <li>{this.props.peanutsGang.aBoy}</li>
          <li>{this.props.peanutsGang.sister}</li>
          <li>{this.props.peanutsGang.dog}</li>
          <li>{this.props.peanutsGang.bestFriend}</li>
          <li>{this.props.peanutsGang.complicatedCrush}</li>
          <li>{this.props.peanutsGang.pianoMan}</li>
        </ul>
      </div>
    )
  }}export default Gang
```

`<Gang/>`会呈现这样的东西:

花生帮！

*   查理·布朗
*   莎莉·布朗
*   史努比
*   莱纳斯·范佩尔
*   露西·范佩尔
*   施罗德

好悲伤。这可以产生想要的结果，但是`<Gang/>`组件中的代码一点也不枯燥。忽略这个例子，因为它不涉及创建一个花生帮数组，该数组充满了表示每个字符的对象，这些对象随后被映射以呈现每个字符的`<li>`标签。从这个简单的例子中可以看出`this.props.peanutsGang`被重复了很多次。想象一下，一个组件需要更多的道具来以这种方式呈现。在这种情况下，使代码干涸的关键是析构赋值。下面是对`<Gang/>`组件的重构:

```
// Gang component //////////////////////////////////////////////////import React, {Component} from 'react'class Gang extends Component {

  render() { const {
      aBoy, 
      sister, 
      dog, 
      bestFriend, 
      complicatedCrush, 
      pianoMan
    } = this.props.peanutsGang    return (
      <div>
        <h1>The Peanuts Gang!</h1>
        <ul>
          <li>{aBoy}</li>
          <li>{sister}</li>
          <li>{dog}</li>
          <li>{bestFriend}</li>
          <li>{complicatedCrush}</li>
          <li>{pianoMan}</li>
        </ul>
      </div>
    )
  }}export default Gang
```

这是达到相同结果的更干净的方法。在 render 方法中，一个析构表达式被声明为一个对象，该对象填充了表示其值中的键的变量:`this.props.peanutsGang`—一个来自父组件`<Peanuts/>`的对象，它作为一个属性被传递。然后，在每个`<li>`标签中，需要包含的只是变量。注意，析构赋值最常用于从 props 中访问整个对象或函数，*例如* `{peanutsGang} = this.props`，而不仅仅是像这个例子中的内部属性。存在许多可能的用途！

析构赋值初看起来可能不友好，但是一旦在你的头脑中发生了理解的转变，它将是一个受欢迎的朋友，并且将帮助你的代码变得干燥和高效！上面的例子很简单，但是想象一下当应用程序扩展和增加复杂性时，析构赋值的强度。析构赋值也是另一个代码保护程序的组成部分: [React hooks](https://reactjs.org/docs/hooks-intro.html) 。在你自己的花生帮中加入破坏任务！

[github.com/dangrammer](https://github.com/dangrammer)
linked.com/in/danieljromansT5[danromans.com](http://danromans.com/)