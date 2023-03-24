# 从 React 开始？受益于这些编码技巧

> 原文：<https://levelup.gitconnected.com/starting-with-react-benefit-from-these-coding-tips-fadfb25eaed7>

![](img/34b4d4fc51a79e1a533e4007fa38763e.png)

## 当你要学习反应时，给自己一个好的开始

你是新来的吗？或者，在您真正开始学习这个 Javascript 库之前，您是否正在寻找更多的信息？你可能不是唯一一个。React 是生态系统中最流行的 JavaScript 前端框架之一。

这篇文章是为那些想开始学习或刚刚开始学习的人写的。但是，建议您至少对 JavaScript 有一些经验，以便能够阅读和理解本文中使用的编码示例。

本文中的技巧应该让您更好地了解某些事情，如事件处理，在 React 中是如何工作的。这将为您成为更好的 React 开发人员提供一个良好的开端。

# 成分

您可能已经知道，React 是基于组件的。随之而来的一个好处是，逻辑只需实现一次，然后就可以在应用程序中的任何地方重用。

一个非常基本的 React 组件可能如下所示:

```
import React, { Component } from 'react'class Car extends React.Component {
  render() {
    return <h1>Model: {this.props.model}</h1>;
  }
}
```

正如你所看到的，汽车组件有一个*渲染*功能。React 组件必须实现这个功能。*渲染*功能决定屏幕上显示的内容。

但是有多种方法来定义一个组件。

## 不同类型的组件

在上面的例子中，我们创建了一个*类组件*。有道理对吗？但是也有另一种方法来定义组件。我们将这类组件*称为功能组件*。

让我们将前面示例中的汽车组件重写为一个功能组件。

```
import React from 'react'const Car = (props) => <h1>Model: {props.model}</h1>
```

## 返回值

随着时间的推移，您的组件可能会变得更加复杂，这导致返回值也变得更加复杂。知道一个组件可以并且应该只有一个父组件是件好事。

```
import React from 'react'const Car = (props) => {
  return (
    <h1>Model: {props.model}</h1>
    <span>{props.price}$</span>
    <a href="#">Buy this car</a>
  )
}
```

上面的例子会导致一个错误，因为没有父节点。 *h1* 、 *span、*和 *a* 标签都在同一层。这在 React 中是不允许的。

有多种方法可以解决这个问题。最明显的方法是将汽车组件的整个输出包装在一个 *div* 中。

```
import React from 'react'const Car = (props) => {
  return (
    <div>
      <h1>Model: {props.model}</h1>
      <span>{props.price}$</span>
      <a href="#">Buy this car</a>
    </div>
  )
}
```

但是可能会有一种情况，你不想要一个包装。别担心，你还有选择。你可以利用碎片。片段可以让您分组一系列子节点，而无需向 DOM 添加额外的节点。

```
import React from 'react'const Car = (props) => {
  return (
    <>
      <h1>Model: {props.model}</h1>
      <span>$ {props.price}</span>
      <a href="#">Buy this car</a>
    </>
  )
}
```

注意`*<> </>*`包装器，它是一个片段。这个元素不会向 DOM 添加额外的节点。

# 班级

当我第一次开始学习 React 时，我不得不习惯的一件事是， *class* 属性没有被使用。类是 Javascript 中的关键字，JSX 是 Javascript 的扩展。这就是 React 使用*类名*而不是*类*的主要原因。

所以如果你想给你的组件添加一些类，你可以使用 *className* 属性，而不是 *class* 。

```
<Car className="suv" />
```

# 呈现多个组件

假设我们想扩展我们之前的例子。我们现在有多辆车。但是我们如何渲染所有这些组件呢？

如果你是一个普通的 Javascript 爱好者，你可能会对此使用 for 循环。但是在 React 中，你可以通过使用*映射*函数来实现。

```
import React from 'react'const CarOverview = (props) => {
  return (
    <div className="cars">
      {props.cars.map((car, key) => {
        return (
          <Car key={key} model={car.model} price={car.price} />
        )
      })}
    </div>
  )
}
```

请注意，我们已经为列表上的项目添加了一个*键*。 *Key* 是一个特殊的字符串属性，您需要在创建元素列表时包含它。关键字有助于识别哪些项目已经更改、添加或删除。每当你在一个数组上循环的时候，应该给所有的元素一个键。这给了元素一个稳定的身份。

# 条件渲染

有时，您会遇到这样的情况，在将某些内容呈现到屏幕上之前，您需要检查一些条件。在我们的例子中，这可以应用于电动汽车。我们想展示一辆电动汽车可以行驶的最大总里程。

```
import React from 'react'const Car = (props) => {
  return (
    <>
      <h1>Model: {props.model}</h1>
      <span>$ {props.price}</span> {props.electric && (
       return <span>Range: {props.range} miles</span>
     )}      <a href="#">Buy this car</a>
    </>
  )
}
```

在其他情况下，您可能想要添加一个 *else* 子句:

```
import React from 'react'const Car = (props) => {
  return (
    <>
      <h1>Model: {props.model}</h1>
      <span>$ {props.price}</span> {props.electric ? 'Electric' : 'Not electric'} <a href="#">Buy this car</a>
    </>
  )
}
```

如果你真的想使用多个 else if 子句，你可以选择将所有的逻辑放在一个函数中。

```
const Car = (props) => { const getType = () => {
    if (props.electric) {
      return 'Electric'
    } else if (props.hybrid) {
      return 'Hybrid'
    }

    return 'Gas'
  } return (
    <>
      <h1>Model: {props.model}</h1>
      <span>$ {props.price}</span> {getType()} <a href="#">Buy this car</a>
    </>
  )
}
```

# 处理事件

在 React 中处理事件非常简单，您可能熟悉这种方法。唯一不同的是一些小的语法问题。

```
import React from 'react'const Car = (props) => { const buyCar = (e) => {
    e.preventDefault() console.log('Someone wants to buy this car')
  } return (
    <>
      ...

      <button onClick={buyCar}>Buy this car!</button>
    </>
  )
}
```

# 在组件中使用状态

我们已经到达了本文将要讨论的最后一个主题。了解状态如何工作是理解 React 的关键。

让我们看看下面的例子:

```
import React, { useState } from 'react';

const Like = (props) => {
  const [likes, setLikes] = useState(props.likes) const like = () => {
    setLikes(likes + 1)
  } return (
    <div>
      <p>This car has been liked {likes} times</p>
      <button onClick={like}>
        Like
      </button>
    </div>
  );
}
```

正如你所看到的，我们引入了一个新的组件，叫做*，就像*。我们使用了 *useState* 钩子来声明一个状态变量。但是在我们进一步讨论之前，什么是状态？

状态只不过是一个存储组件动态数据的 JavaScript 对象。因为状态是动态的，所以它使组件能够跟踪呈现之间不断变化的信息。因此，状态可以用来使组件动态和交互。

状态类似于道具，但不同于道具，它是组件的私有属性，只由组件控制。

但是下面这行代码到底是做什么的呢:

```
const [likes, setLikes] = useState(props.likes)
```

这行代码声明了一个名为*的状态变量 likes* 。为了改变我们的 *likes* 状态变量的值，我们可以使用我们定义的 *setLikes* 函数。所以任何时候我们想要更新我们的 *likes* 变量，我们调用 *setLikes* 函数。最后但同样重要的是，我们传递给 *useState* 函数的参数是 *likes* 变量的默认值，在本例中是通过 props 传递的。

# 后续步骤

现在您已经对 React 的工作原理有了一些了解，是时候开始使用它了。你可以从 Create React App [Github](https://github.com/facebook/create-react-app) 入手。这样，您可以立即开始摆弄 React。

试着更好地理解主要概念。一旦你掌握了这些，尝试更高级的概念，如[上下文](https://reactjs.org/docs/context.html)。不要忘了，学习的唯一方法，就是把你的手弄脏！