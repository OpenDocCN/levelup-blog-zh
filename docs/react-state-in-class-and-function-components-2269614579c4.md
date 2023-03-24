# 对类和函数组件中的状态做出反应

> 原文：<https://levelup.gitconnected.com/react-state-in-class-and-function-components-2269614579c4>

![](img/49f785154a80e6afddad8ae36aab45f9.png)

由[费伦茨·阿尔马西](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

在 React 16.8 之前，功能组件没有状态或生命周期挂钩。在 16.8+中，函数组件现在可以使用钩子来使用状态，你可以在第一次渲染和每次更新后实现副作用。为了更深入地了解这一点，让我们通过构建两次—[——一次作为](https://darrylmendonez.github.io/barebones-react-todo-list/#/function-component)[类组件](https://github.com/darrylmendonez/barebones-react-todo-list/blob/master/src/components/classComponent.js)，然后再次作为[函数组件](https://github.com/darrylmendonez/barebones-react-todo-list/blob/master/src/components/functionComponent.js)，来看看函数和类组件如何在初始和后续渲染中使用状态和触发函数。

# 安装

当然，类和函数组件之间的第一个显著区别是它们的语法。一个类组件从`React.Component`扩展而来，并设置一个返回 React 组件的渲染函数。

```
import React, { Component } from 'react';class ClassComponent extends Component {
  render() {
    return ();
  }
}export default ClassComponent;
```

而函数组件是普通的 JavaScript，接受 props 作为参数并返回 React 组件。

```
import React from 'react';const FunctionComponent = () => {
  return ();
}export default FunctionComponent
```

# 初始化状态

我们的状态是一个 JavaScript 对象，包含可以绑定到呈现函数输出的数据。每当状态属性更新时，React 都会相应地重新呈现组件。在类组件中，有两种初始化状态的方法——在构造函数中或者作为类属性。

ES6 中引入的构造函数是类第一次实例化时调用的第一个函数——也就是从类中创建新对象时。在构造函数中初始化状态允许在 React 呈现组件之前创建状态对象。

```
import React, { Component } from 'react';class ClassComponent extends Component {
  **constructor(props) {
    super(props);
    this.state = {
      list: [],
      currentItem: '',
    }
  }**
  render() {
    return ();
  }
}export default ClassComponent;
```

我们还可以使用 Class 属性来初始化状态。一旦类的一个实例在内存中，状态的属性就被创建，并可以被 render 函数读取。

```
import React, { Component } from 'react';class ClassComponent extends Component {
  **this.state = {
    list: [],
    currentItem: '',
  }**
  render() {
    return ();
  }
}export default ClassComponent;
```

这两种方法的净输出是相同的，所以这完全取决于偏好。为了这篇文章，我将坚持使用构造函数的方法，继续我们的比较。

在 React 16.8 中，函数组件现在可以使用状态。在此之前，来自状态的数据必须作为道具从类组件传递到函数组件，或者必须将函数组件转换为类组件。现在，我们可以使用 React 钩子，要使用 state，我们可以使用`useState`钩子。通过使用数组析构来声明状态变量和设置状态变量，而不是将状态声明为对象并一次设置所需数量的属性。

```
import React, { useState } from 'react';const FunctionComponent = () => {
  **const [list, setList] = useState([]);
  const [currentItem, setCurrentItem = useState('');**
  return ();
}export default FunctionComponent
```

# 渲染组件

很好，现在我们已经初始化了我们的状态，让我们渲染我们的组件。我将在`state`中的`list`属性中添加一些项目，这样我们就有一些数据要呈现了。请注意，`list`中的每一项都是一个具有三个属性的对象——一个`id`、一个`task`和一个`completed`属性，这三个属性表明我们的待办事项列表中的任务是否已经完成。我还将[安装](https://www.npmjs.com/package/react-flexbox-grid)并导入`[react-flexbox-grid](https://roylee0704.github.io/react-flexbox-grid/)`，这样我们就有了一个易于使用的网格系统。

## 类别组件:

```
import React, { Component } from 'react';
**import { Grid, Row, Col } from 'react-flexbox-grid';**class ClassComponent extends Component {
  constructor(props) {
    super(props);
    this.state = {
      **list: [
        {
          id: 1,
          task: 'Create tasks',
          completed: false,
        },
        {
          id: 2,
          task: 'Read tasks',
          completed: false,
        },
        {
          id: 3,
          task: 'Mark complete',
          completed: false,
        },
        {
          id: 4,
          task: 'Delete tasks',
          completed: false,
        },
      ],
      currentItem: '',**
    }
  }
  render() {
    return (
      **<Grid fluid>
        <Row>
          <Col xs={6} md={3}>
            <h3>Things to do:</h3>
            <ul>
              {this.state.list.length ? (                this.state.list.map( item => (
                  <React.Fragment key={item.id}>
                    <li>
                      {item.task}
                    </li>
                  </React.Fragment>
                ))
              ) : (null)
              }
            </ul>
          </Col>
        </Row>
      </Grid>**
    );
  }
}export default ClassComponent;
```

## 功能组件:

```
import React, { useState } from 'react';const FunctionComponent = () => {
  const [list, setList] = useState([
    **{
      id: 1,
      task: 'Create tasks',
      completed: false,
    },
    {
      id: 2,
      task: 'Read tasks',
      completed: false,
    },
    {
      id: 3,
      task: 'Mark complete',
      completed: false,
    },
    {
      id: 4,
      task: 'Delete tasks',
      completed: false,
    },**
  ],);
  const [currentItem, setCurrentItem] = useState('');
  return (
    **<Grid fluid>
      <Row>
        <Col xs={6} md={3}>
          <h3>Things to do:</h3>
          <ul style={{listStyleType: 'none'}}>
            {list.length ? (
              list.map( item => (
                <React.Fragment key={item.id}>
                  <li>
                    {item.task}
                  </li>
                </React.Fragment>
              ))
            ) : (null)
            }
          </ul>
        </Col>
      </Row>
    </Grid>**
  );
}export default FunctionComponent
```

# 设置状态

现在我们已经初始化了状态并呈现了组件，让我们添加一些操作状态数据的功能，比如添加任务、标记任务完成和删除任务。这将让我们看到类和函数组件中状态更新方式的不同。

在类和函数组件中，当更新状态时，不应该直接更新它。对于类组件，提供的方法是`setState`。对于函数组件，我们可以使用在使用 React 钩子`useState`初始化状态时声明的 set 状态变量。

## 在类组件中添加任务:

我想添加一个文本输入字段，允许用户将任务添加到他们的待办事项列表中。我将把`value`属性设置为`this.state.currentItem`，这是我们刚刚在`state`对象中初始化的，我还将创建一个`handleChange`函数，它将通过`input`标签的`onChange`属性触发。`handleChange`函数将使用`setState`来更新`state`的`currentItem`属性。当输入值由 React 而不是 HTML 控制时，这被称为*受控组件—* 。现在，当用户在输入域中输入时，`currentItem`会随着每次击键而更新。

```
handleChange = e => this.setState({currentItem: e.target.value})
```

注意，`setState`接受一个对象，该对象用该对象中相应的一个或多个属性更新状态。

我还将用`form`标签包装`input`标签，并将`onSubmit`属性设置为`handleSubmit`函数。这将在`list`数组中添加另一个项目。该项将是一个具有我们前面提到的三个属性的对象。在阻止了刷新页面的默认行为`onSubmit`之后，我们需要生成一个惟一的`id`，然后设置状态以将新的项目推送到列表中。我将再次使用`setState`，但这一次，我将传入一个返回对象的函数，而不是传入一个对象。因为我们的新状态依赖于从前一个状态获取数据，所以我们可以传入前一个状态来实现这一点。我们的`handleChange`函数更新了`currentItem`，所以现在我们的`handleSubmit`需要获取`currentItem`的值，并将其设置为新项目的`task`属性。最后，对于第三个属性，我们将把`completed`设置为`false`。此外，通过传入函数，我们可以在返回对象之前运行一些代码。我将声明一个`const newItem`并将它设置为一个具有我们三个属性的对象。现在我们可以返回一个对象，将`list`属性设置到一个数组中，该数组将`list`从前一个状态展开，并将`newItem`添加到数组的末尾。我们也可以将`currentItem`设置为空字符串。这将把输入字段重置为空白，使用户更容易输入另一个任务。

```
import React, { Component } from 'react';
import { Grid, Row, Col } from 'react-flexbox-grid';class ClassComponent extends Component {
  .
  .
  .
  **handleChange = e => this.setState({currentItem: e.target.value})** **handleSubmit = e => {
    e.preventDefault()** ***// generate an unused id*
    let newId = 1;
    let sortedListByIds = this.state.list.slice().sort((a, b) => (a.id - b.id))
    for (let i = 0; i < sortedListByIds.length; i++) {
      if (newId === sortedListByIds[i].id) {
        newId++
      }
    }****this.setState(prevState => {
      const newItem = {
        id: newId,
        task: prevState.currentItem,
        completed: false,
      }
      return {
        list: [...prevState.list, newItem],
        currentItem: '',
      }
    })
  }**render() {
    return (
      <Grid fluid>
        <Row>
          <Col xs={6} md={3}>
            .
            .
            .
          </Col>
          **<Col xs={6} md={6}>
            <h4>Add task:</h4>
            <form onSubmit={this.handleSubmit}>
              <input type="text" autoFocus value={this.state.currentItem} onChange={this.handleChange} />
              <button type="submit">+</button>**
            **</form>
          </Col>**
        </Row>
      </Grid>
    );
  }
}export default ClassComponent;
```

## 在功能组件中添加任务:

对于函数组件,`form`和`input`标签将与我们在类组件中设置它们的方式完全相同。`handleChange`和`handleSubmit`将使用我们通过`useState` React 钩子初始化状态时声明的设置变量。回想一下，当我们为`currentItem`设置数组析构时，第二项是`setCurrentItem`。这是我们用来更新我们的`currentItem`的。

```
const handleChange = e => setCurrentItem(e.target.value)
```

对于`handleSubmit`函数，我们仍将阻止默认行为，我们仍将生成唯一的`id`，但我们将使用`useState` React 钩子更新状态。与我们可以一次更新两个属性的类组件不同，使用`useState`我们必须为每个属性分别更新。要将新项目添加到我们的`list`，就像在类组件中一样，我们需要访问之前的`list`。`useState`也可以接受一个函数作为参数，我们可以向该函数传递一个参数，在我们的例子中是`prevList`，以获取之前的数据。为了重置输入字段，我们可以在`setCurrentItem`中传递一个空字符串…

```
const handleSubmit = e => {
  e.preventDefault() *// generate an unused id*
  let newId = 1;
  let sortedListByIds = list.slice().sort((a, b) => (a.id - b.id))
  for (let i = 0; i < sortedListByIds.length; i++) {
    if (newId === sortedListByIds[i].id) {
      newId++
    }
  }**setList(prevList => {
    const newItem = {
      id: newId,
      task: currentItem,
      completed: false,
    }
    return [...prevList, newItem]
  })
  setCurrentItem('')**
}
```

## 切换完成状态和删除类组件中的任务:

对于类和函数组件，用于将项目标记为完成或未完成的 jsx 和用于删除任务的按钮的 jsx 是相同的。单击列表项会给它一条删除线，表示任务已完成，再次单击它会删除表示任务未完成的删除线。每个列表项还会有一个从列表中删除该项的按钮。

```
.
.
.
<ul style={{listStyleType: 'none'}}>
  {list.length ? (
    list.map( item => (
      <React.Fragment key={item.id}>
        <li onClick={() => toggleCompleteStatus(item.id)} style={{textDecoration: item.completed ? 'line-through' : 'none'}}>
          {item.task}
        </li> <button onClick={() => deleteTask(item.id)}>x</button>
      </React.Fragment>
    ))
  ) : (null)
  }
</ul>
.
.
.
```

现在让我们把这些连接到一些函数上。对于`toggleCompleteStatus`，我们将传入一个`id`并使用`setState`返回一个具有`list`属性的对象。我们将使用`map`方法映射当前的`list`并找到具有匹配`id`的项目，并将该项目的完成状态从`false`切换到`true`，反之亦然。

```
toggleCompleteStatus = id => {
  this.setState(() => {
    return {
      list: this.state.list.map( item => item.id === id ? {...item, completed: !item.completed} : item)
    }
  }
}
```

对于`deleteTask`函数，我们也将传入一个`id`。我们还将声明一个变量`let filteredList`，我们将使用`filter`方法来搜索`list`中的每一项，寻找与参数不匹配的`id`。所有不匹配的条目都将在一个新的数组中返回，忽略被删除的条目。然后我们可以使用`setState`返回一个属性为`list`的对象，该对象等于一个展开了`filteredList`的数组。

```
deleteTask = id => {
  let filteredList = this.state.list.filter( item => item.id !== id)
  this.setState({
    list: [...filteredList]
  }
}
```

## 切换完成状态和删除功能组件中的任务:

在函数组件中，这些函数看起来非常相似，除了我们将再次使用我们用 React 的`useState`声明的 set state 变量来更新我们的数据，而不是使用`setState`。我们使用的数组方法——`map`和`filter`——使用方式完全相同。

```
const toggleCompleteStatus = id => {
  **setList**(list.map( item => item.id === id ? {...item, completed: !item.completed} : item))
}const deleteTask = id => {
  let filteredList = list.filter( item => item.id !== id)
  **setList**([...filteredList])
}
```

# 生命周期和使用效果挂钩

类组件可以使用生命周期挂钩在组件生命周期的特定点触发功能。例如，从 api 获取数据的最佳时机是在组件输出呈现到 DOM 之后运行的`componentDidMount`钩子期间。然而，函数组件没有生命周期挂钩，但仍然可以在第一次渲染和每次更新后产生副作用。所以只在第一次渲染时产生副作用相当于在`componentDidMount`钩子上产生一个函数。这是使用 React 钩子`useEffect`完成的。

为了展示这两者的例子，让我们使用`localStorage`作为一种使数据持久化的方法。这也将使这个待办事项应用程序成为一个真正的功能性应用程序，因为即使用户刷新页面、关闭浏览器或关闭计算机，数据也会持续存在。

## 用 setState 的回调函数保存列表项:

`setState`函数接受第二个参数，这是一个回调函数。因此，对于`handleSubmit`、`toggleCompleteStatus`和`deleteTask`，我们可以添加一些代码，将`list`数据保存到用户设备的本地。`localStorage`的`setItem`方法将数据保存为用户浏览器中的键值对，其中的值是 JSON 格式。在我们的例子中，我们将保存我们的`state`的`list`属性的值。`setItem`方法有两个参数。第一个是字符串，用作我们的键，第二个是 JSON 对象，用作我们的值。我们将使用方法`JSON.stringify`并传入我们的`list`来将其转换成 JSON 对象…

```
localStorage.setItem('list', JSON.stringify(this.state.list))
```

所以现在我们可以把它附加到`handleSubmit`、`toggleCompleteStatus`和`deleteTask`的`setState`回调函数上。

```
**handleSubmit** = e => {
  .
  .
  .
  this.setState(prevState => {
    const newItem = {
      id: newId,
      task: prevState.currentItem,
      completed: false,
    }
    return {
      list: [...prevState.list, newItem],
      currentItem: '',
    }
  }**, () => localStorage.setItem('list', JSON.stringify(this.state.list))**
  )
}**toggleCompleteStatus** = id => {
  this.setState(() => {
    return {
      list: this.state.list.map( item => item.id === id ? {...item, completed: !item.completed} : item)
    }
  }**, () => localStorage.setItem('list', JSON.stringify(this.state.list))**
  )
}**deleteTask** = id => {
  let filteredList = this.state.list.filter( item => item.id !== id)
  this.setState({
    list: [...filteredList]
  }**, () => localStorage.setItem('list', JSON.stringify(this.state.list))**
  )
}
```

`setState`是一个异步函数，它的第二个参数回调函数在状态更新后触发。这意味着您可以确信传递到开发工具的本地存储中的数据将是最新的。

您可以通过打开您的开发工具并单击 Application 选项卡来查看本地存储数据。

![](img/440f169af87821c6270d6d837a6ea6f1.png)

## 保留具有 useEffect 的列表项:

我们一直在使用我们的“set”函数来更新函数组件中的状态。虽然`setState`允许回调函数作为类组件中的第二个参数，但是我们的 set state 函数没有同样的便利。然而，我们可以使用 React 钩子`useEffect`在特定状态更新时触发副作用。让我们像这样导入使用效果…

```
import React, { useState**, useEffect** } from 'react';
```

我们没有将代码添加到我们当前的函数中，而是将一个匿名函数作为第一个参数传递给`useEffect`，并将一个变量数组作为第二个参数。每当第二个参数数组中的一个变量被更新时，匿名函数中的代码块将被触发。

```
useEffect(() => {
  localStorage.setItem('list', JSON.stringify(list))
}, [list])
```

注意第二个参数有一个包含变量`list`的数组。由于`handleSubmit`、`toggleCompleteStatus`和`deleteTask`都更新`list`状态，那么每当这些函数更新`list`时，这个 useEffect 函数将被触发。

## **获取 componentDidMount 上的本地数据:**

酷，所以现在我们可以在类和函数组件中设置数据到我们的本地存储中，但是我们仍然需要获取数据，这样它就可以呈现到 DOM 中。

对于我们的类和函数组件，我们确实有我们的`list`项的初始状态，但我们也想检查是否有本地数据，所以如果用户已经使用了这个应用程序，他们可以看到他们的任务列表，就像他们上次加载站点时一样。

获取类组件数据的最佳时间是在类组件呈现后的生命周期挂钩期间。我们将使用`localStorage`的`getItem`方法，它接受一个参数，这个参数是存储我们数据的键的名称字符串——在我们的例子中是`'list'`。我们还必须使用`JSON.parse()`将我们的 JSON 字符串转换成一个数组。如果用户没有本地数据，那么它将返回`null`。如果它是`null`,那么我们不需要设置我们的状态，我们在构造函数中设置的初始状态将会保留并呈现。如果不是`null`，那么我们将设置我们的`state`来用我们刚刚获取的数据更新`list`属性。

```
componentDidMount() {
  let localList = JSON.parse(localStorage.getItem('list'));
  if (localList !== null) {
    this.setState(() => {
      return {
        list: localList
      }
    })
  }
}
```

## **使用 useEffect 获取初始渲染的本地数据:**

我们现在知道，只要我们的一个状态变量更新，我们就可以激发副作用。但是如果功能组件没有生命周期挂钩，我们怎么能在初始渲染时产生副作用呢？为此，我们所要做的就是传入一个空数组作为第二个参数。在这种情况下，useEffect 只会在初始渲染时触发，就像`componentDidMount`一样。

```
useEffect(() => {
  let localList = JSON.parse(localStorage.getItem('list'));
  if (localList !== null) {
    setList(localList)
  }
}, []) *// empty array as second argument will behave exactly like componentDidMount*
```

# 结论

就是这样！我们现在有了一个全功能的待办事项列表应用程序，它是通过两种不同的方式构建的——通过类组件和通过函数组件。我们有一个列表项的初始状态，用户第一次使用这个应用程序时会加载它。他们可以添加和删除任务。他们可以将任务标记为完成或未完成。数据保存在浏览器的本地存储器中，因此，如果他们关闭浏览器，稍后回到这个网站，他们的数据仍然可以查看，并根据需要进行更新。无需保存到云或登录帐户。他们数据的隐私和他们设备的隐私一样好。这是一个基本的实现，所以我把 CSS 交给你了。我的希望是，您已经了解或巩固了状态如何通过 React 中的类和函数组件初始化和更新，以及如何使用生命周期钩子和`useEffect`的区别。感谢您阅读并坚持到现在。做好人！

查看 app:[https://darrylmendonez . github . io/bare bones-react-todo-list/#/](https://darrylmendonez.github.io/barebones-react-todo-list/#/)

查看回购:[https://github.com/darrylmendonez/barebones-react-todo-list](https://github.com/darrylmendonez/barebones-react-todo-list)