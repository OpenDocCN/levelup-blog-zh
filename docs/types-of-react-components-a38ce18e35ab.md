# React 组件的类型

> 原文：<https://levelup.gitconnected.com/types-of-react-components-a38ce18e35ab>

## 功能组件、类别组件、纯组件和高阶组件

![](img/b68c09fff58346768642cfb0641deabf.png)

## 什么是 React 组件？

React 组件是独立且可重用的代码。它们是任何 React 应用程序的构建块。组件的作用与 JavaScript 函数相同，但它们独立工作，将 JSX 代码作为元素返回给我们的 UI。组件通常有两种类型，功能组件和类组件，但今天我们也将讨论纯组件和高阶组件。

## 讨论的 React 组件类型

*   功能组件
*   类别组件
*   纯成分
*   高阶组件

## 功能组件

功能组件是接受道具并返回 JSX 的功能。它们本身没有状态或生命周期方法，但是可以通过实现 React 钩子来添加这种功能。功能组件通常用于显示信息。它们易于阅读、调试和测试。

```
// Functional Component Exampleimport React from 'react';const HelloWorld = () => {
   return (
      <div>
         <p>Hello World!</p>
      </div>
   )
}export default HelloWorld;
```

在上面的代码中，它是一个非常简单的组件，由一个常量变量`HelloWorld`组成，该变量被分配给一个返回 JSX 的箭头函数。功能组件不必是箭头功能。它们可以用常规的 JavaScript 函数来声明。您还可以将`props`传递给函数，并使用它们在 JSX 代码中呈现数据。

## 类别组件

在这四种组件类型中，类组件以前是最常用的。这是因为类组件能够做功能组件所做的一切，甚至更多。可以利用 React、 ***状态*** 、 ***道具*** 、 ***生命周期方法*** 的主要功能。与功能组件不同，类组件由…嗯，一个类组成。

![](img/6c4b3ad5c496919a3ad4ae63c58f098a.png)

```
// Class Component Exampleimport React from 'react';class HelloWorld extends React.Component {
   render() {
      return (
         <div>
            <p>Hello World!</p>
         </div>
      )
   }
}export default HelloWorld;
```

类组件语法不同于功能组件语法。类组件在声明类`HelloWorld`后使用`extends React.Component`，并需要一个`render()`方法返回 JSX 代码。在这个类组件中，可以声明一个`state`，将其设置为一个 JavaScript 对象，使用`props`作为初始状态或者在 ***生命周期方法*** 中改变状态。一些生命周期方法有`componentDidMount()`、`componentDidUpdate()`和`componentWillUnmount()`。除非使用 React 挂钩，否则功能组件无法执行这些操作。

## 纯成分

纯组件就像更好的功能组件。只返回一个渲染函数的组件最适合纯组件发光。然而，你需要理解一个纯组件是做什么的。

![](img/01ce2510369461a885369380befd37a9.png)

纯组件主要用于提供优化。它们是我们能编写的最简单和最快的组件。它们不依赖或修改其范围之外的变量的状态。因此，为什么纯组件可以取代简单的功能组件。

![](img/f4f82981ae027b7af8b51a7ad9c08c2c.png)

但是，常规的`React.Component`和`React.PureComponent`之间的一个主要区别是，纯组件对状态变化执行 ***浅层比较*** 。纯组件自己照顾`shouldComponentUpdate()`。如果前一个`state`和/或`props`与下一个相同，组件不会被重新渲染。

React 组件通常在以下情况下重新呈现:

*   `setState()`被称为
*   `props`值被更新
*   `forceUpdate()`叫做

但在纯组件中，React 组件不会在不考虑更新的`state`和`props`的情况下盲目重新渲染。因此，如果`state`和`props`与之前的`state`和`props`相同，那么组件不会重新渲染。当您不想在`props`和`state`保持不变的情况下重新渲染某个组件时，这非常有用。

例如，您不希望一个组件(比如说音乐播放器)因为另一个组件更新而重新呈现，可能是在您创建播放列表时。您可能希望音乐播放器一直播放一首歌曲，而不是在您更改不同组件的状态或道具时重播。纯组件在这种情况下非常有用。

## 高阶组件

高阶组件(HOC)是 React 中重用组件逻辑的高级技术。它不是 API 中的 React 组件。这是一种从 React 的组合性质中出现的模式。基本上，hoc 是返回组件的函数。它们用于与其他组件共享逻辑。

```
// HOC Exampleimport React from 'react';
import MyComponent from './components/MyComponent';class HelloWorld extends React.Component {
   render() {
      return(
         <div>
            {this.props.myArray.map((element) => (
               <MyComponent data={element} key={element.key} />
            ))}
         </div>
      )
   }
}export default HelloWorld;
```

在上面的代码中，我有一个简单的组件，它简要描述了一个 HOC。`this.props.myArray.map((element) => (<MyComponent />))`是关键的代码片段。这是一个根据数组中元素的数量返回一个或多个分量的函数，也称为 HOC。这个函数获取一个数组，通常是用 ***fetch*** 从后端或者从父组件传递的 props 中获取的状态， ***映射*** 数组中的每个元素，将数组中的每个元素转换成 React 组件。

![](img/267dd055a89cd6723b39817443519280.png)

高阶组件简单缩减:

1.  从 state 或 props 获取数据(应为数组)。
2.  映射数组并为每个元素返回一个 React 组件。

hoc 的一些例子是注释。当用户创建评论时，评论会被保存到后端。然后使用 React，用 ***fetch*** 检索注释，并将数据作为数组放入组件的状态中。然后在呈现这些注释的组件上，映射注释数组并将每个注释转换成注释组件。

hoc 非常有用，可以在几乎所有想要根据给定数据动态呈现多个组件的 React 应用程序中找到用途。

## 结论

对所讨论的组件的简短总结:

*   **函数组件** —返回 JSX 的函数。可以用 React 钩子做更多的事情。
*   **类组件** —可以操纵状态、属性和生命周期方法的类。
*   **纯组件** —自动执行`shouldComponentUpdate()`的功能组件。仅当状态或道具不同于先前的状态或道具时才重新渲染。
*   **高阶分量** —根据数组数据返回一个或多个分量的函数。