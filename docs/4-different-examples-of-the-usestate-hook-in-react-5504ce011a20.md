# React 中使用状态钩子的 4 个不同例子

> 原文：<https://levelup.gitconnected.com/4-different-examples-of-the-usestate-hook-in-react-5504ce011a20>

![](img/78165755afcdb6db0ea5ed574aa61bfd.png)

阿诺德·弗朗西斯卡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

`useState`是 React 中广泛使用的钩子。在 React 中开发的几乎每个组件都涉及状态。在这篇文章中，我将向你展示`useState`钩子的四个不同的用例。

我假设你对反应和状态有基本的了解。如果没有，请访问[文档](https://reactjs.org/docs/getting-started.html)开始。

# 初始化状态

作为提醒，我将回顾如何初始化状态。首先，导入`useState`，然后用下面的方式声明你的状态变量。

```
import { useState } from 'react'const [name, setName] = useState('kunal')
```

`setName`是用于设置状态的功能，即`name`。它有一个默认值。

不要直接设置状态，总是使用函数。看[这个](https://medium.com/analytics-vidhya/why-we-should-never-update-react-state-directly-c1b794fac59b)就知道为什么了。

现在，我们来看一些用例。

## 1.条件渲染

假设您的应用程序有一个用户，您希望根据该用户是否是管理员来显示一些内容。您可以通过条件渲染来实现这一点。

在这个例子中，我们有一组所有用户都能看到的记录。但是，管理员用户可以选择编辑每条记录。首先，将用户定义为 state。

```
const [user, setUser] = useState({
    name: 'kunal',
    isAdmin: false
}) 
```

现在，呈现记录。`tableData`有要显示的记录。

```
return (
  <div>
      <table>
          <tr>
              <th>id</th>
              <th>First Name</th>
              <th>City</th>
              {user.isAdmin &&  <th>Action</th>}
          </tr>
          {tableData.map(data => (
              <tr>
                  <td> {data.id} </td>
                  <td> {data.first_name} </td>
                  <td> {data.city} </td>
                  {user.isAdmin &&  
                      <td>
                          <button> Edit </button>
                      </td>
                  }

              </tr>
          ))}
      </table>
  </div>
)
```

在这里，普通用户只能看到前三列。但是，管理员用户还会看到包含编辑按钮的第 4 列。

如果`isAdmin`属性是`false`，那么该列将不可见。

我们再举一个例子。您有两个表，并且您只想一次呈现一个。

我们有一个状态`toggleTable`来决定显示哪个表。您可以通过点击按钮在表格之间切换。

```
const [toggleTables, setToggleTables] = useState(false)
```

这两个表显示不同的数据，并且可以通过使用条件表达式根据状态值来呈现。

```
return (
    <div>
        <div>
            <button onClick={handleClick} >
                Show the other table
            </button>
        </div>
        <div>
            {
                toggleTables ? 

                    <table>
                        <tr>
                            <th>id</th>
                            <th>First Name</th>
                            <th>Last Name</th>
                            <th>City</th>
                        </tr>
                        {tableData1.map(data => (
                            <tr>
                                <td> {data.id} </td>
                                <td> {data.first_name} </td>
                                <td> {data.last_name} </td>
                                <td> {data.city} </td>

                            </tr>
                        ))}
                    </table>
                :  
                    <table>
                        <tr>
                            <th>id</th>
                            <th>First Name</th>
                            <th>City</th>
                            <th>Bitcoin Address</th>
                            <th>Credit Card</th>
                            <th>Card Type</th>
                            <th>Currency</th>
                        </tr>
                        {tableData2.map(data => (
                            <tr>
                                <td> {data.id} </td>
                                <td> {data.first_name} </td>
                                <td> {data.city} </td>
                                <td> {data.bitcoin_address} </td>
                                <td> {data.credit_card} </td>
                                <td> {data.card_type} </td>
                                <td> {data.currency} </td>
                            </tr>
                        ))}
                    </table>
            } 
        </div>                 
    </div>
  )
```

这里是我们改变状态的`handleClick`函数。

```
const handleClick = () => {
    setToggleTables(!toggleTables);
}
```

## 2.计数器

状态也可用于实现计数器。这包括跟踪以前的状态。首先，将 count 作为 state，默认值为 0。

```
const [count, setCount] = useState(0) 
```

然后，渲染相同的，有两个按钮用`onClick`处理程序递增和递减。

```
<h2> {count} </h2>
<div>
    <button onClick={() => { setCounter(-1) }} > Decrement </button>
    <button onClick={() => { setCounter(1) }}> Increment </button>
</div>
```

实现处理函数。

```
function setCounter(value) {
    setCount(count+value);
}
```

让我们也有一个输入字段来控制计数器增加或减少多少。

```
<div>
    <form onSubmit={handleSubmit}>
        <label> Enter a value to increment by </label>
        <input type='number' />
        <button type='submit' > Submit </button>
    </form>
</div>
```

实现处理函数。使用相同的`setCounter`功能，根据用户给定的值设置状态值。

```
const handleSubmit = (event) => {
    event.preventDefault();
    setCounter(parseInt(event.target[0].value));
}
```

提交表单时使用`event.preventDefault()`。点击阅读更多相关信息[。](https://www.w3schools.com/jsref/event_preventdefault.asp)

## 3.表单处理

表单处理是`useState`钩子的一个非常重要的用例。你在任何地方都会遇到这种情况。

让我们有一个具有以下字段的基本表单。根据需要填写每个字段。

```
 <form onSubmit={handleSubmit}>
        <input placeholder='Enter name' 
               onChange={(e) => {setName(e.target.value)}} 
               value={name} required/>        
        <br/>
        <input type='email' placeholder='Enter email' value={email}
                 onChange={(e) => {setEmail(e.target.value)}} required/>
        <br/>
        <input type='password' placeholder='Enter password' value={password}
                 onChange={(e) => {setPassword(e.target.value)}} required/>
        <br/>
        <label>Gender</label>
        <select onChange={(e) => {setGender(e.target.value)}} value={gender}
                required>
            <option>Male</option>
            <option>Female</option>
        </select>
        <br/>

        <button type='submit'>Submit</button>

</form>
```

为同一定义以下状态变量。

```
const [name, setName] = useState('')
const [email, setEmail] = useState('')
const [password, setPassword] = useState('')
const [gender, setGender] = useState('')
```

这些状态变量保存表单字段的值。您希望它们随着用户输入而更新。这是使用 input 元素的`onChange`属性完成的。

在实时应用程序中，表单提交通常需要一些时间。因此，向用户显示表单正在被提交是很重要的。你可以使用另一个状态变量。

```
const [isSubmitting, setIsSubmitting] = useState(false)
```

点击提交按钮后，将`isSubmitting`置为`true`，提交后再置为`false`。同时，向用户显示一条消息。在这里，没有什么会阻碍表单提交，所以您看不到消息。

让我们模拟一下这种行为。

```
const handleSubmit = (event) => {
    event.preventDefault();

    setIsSubmitting(true)
    setTimeout(() => {
        setIsSubmitting(false);
        alert("Form submission successful")
    }, 3000);
}
```

使用`setTimeout`在表单提交前等待几秒钟。并在提交按钮的正下方显示一条消息。

```
{isSubmitting && 'Submitting...'}
```

## 4.待办事项列表

`useState`钩子的一个最简单的用例是一个简单的待办事项列表。我将向您展示如何实现添加、更新和删除功能。

首先，有一个输入字段和一个用于添加任务的按钮。将用户输入保持在一个状态。

```
const [taskInput, setTaskInput] = useState('')
```

```
<input type='text' placeholder="Name your task" value={taskInput}
                    onChange={(e) => { setTaskInput(e.target.value) }}/>
<button onClick={handleSubmit} >Add item</button>
```

此外，有一个状态来保存整个待办事项列表。

```
const [todoList, setTodoList] = useState([])
```

现在，实现添加按钮的`handleClick`功能。

```
const handleSubmit = () => {
    const newTodo = {
        id: new Date().getTime(),
        task: taskInput,
        updateFlag: false
    }
    setTodoList([...todoList, newTodo]);
    setTaskInput('')
}
```

创建一个新的 todo 对象，将当前时间戳作为它的`id`。`updateFlag`字段确定当前项目是否正在更新。现在，显示带有更新和删除选项的列表。

```
<ul>
  {todoList.map(todo => (
      <li key={todo.id}>
          {todo.task}
          <button onClick={() => {handleUpdate(todo.id)}}>Update</button>
          <button onClick={() => {handleDelete(todo.id)}}>Delete</button>
      </li>
  ))}
</ul>
```

对于`handleDelete`，使用 filter 方法从基于`id`的状态数组中移除一个元素(保持其唯一性非常重要)。

```
function handleDelete(id) {
     setTodoList(todoList.filter(todo =>  todo.id !== id));
}
```

为了更新元素，用户需要一个输入字段来输入更新后的值。因此，使用`updateFlag`在显示和更新项目之间切换。对于列表中的每个项目，请执行以下操作:

```
{todo.updateFlag == true
    ? <input type='text' defaultValue={todo.task} 
            onChange={(e) => { setUpdateTaskInput(e.target.value) }}/>
    : todo.task
}
```

此外，使用另一个状态变量来存储更新后的输入值。

```
const [updateTaskInput, setUpdateTaskInput] = useState()
```

如果`updateFlag`为真，该项目将显示一个表单以输入新值。

现在，实现`handleUpdate`函数。该函数将`todo`的`id`作为参数。如果更新后的值为空，则返回。

```
function handleUpdate(id) {
    if(updateTaskInput == '') return;
          ...
}
```

这里的更新包括更改对象的单个字段。首先，使用`id`从列表中获取元素，并创建另一个过滤掉该元素的列表。

```
const todo = todoList.find(todo => todo.id === id)
const updatedList = todoList.filter(todo =>  todo.id !== id)
```

现在，如果`updateFlag`为假，将其设置为真以显示输入字段。

```
if(todo.updateFlag == false) {
    updatedList.push({...todo, updateFlag: true})
    setTodoList(updatedList)
    return;
}
```

现在，输入新值后，用户再次单击 update 按钮。在相同的函数中更新任务名称，但条件相反。

```
updatedList.push({...todo, 
    task: updateTaskInput,
    updateFlag: false
})

setTodoList(updatedList);

setUpdateTaskInput('') 
```

你可以在我的 [Git](https://github.com/KunalN25/usestate-examples) repo 里找到代码。

# 结论

在这篇文章中，我向你展示了使用`useState`钩子的不同例子。在 React 项目中，您会经常遇到前三个用例。

第四个是一个非常常见的例子，它向您展示了如何在不同的场景中处理状态更新。如果有任何不正确的地方，一定要让我知道。

如果您无法理解内容或对解释不满意，请在下面评论您的想法。新想法总是受欢迎的！如果你喜欢这篇文章，请鼓掌。**订阅**和**关注**我的每周内容。如果你想讨论什么，请随时在 LinkedIn 上给我发信息。到那时，再见！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)