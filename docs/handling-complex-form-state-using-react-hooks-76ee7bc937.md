# 使用 React 钩子处理复杂的表单状态

> 原文：<https://levelup.gitconnected.com/handling-complex-form-state-using-react-hooks-76ee7bc937>

## 使用 **this.setState()** 的替代方法

![](img/be373847515f7929d3fcc30a1b4ba142.png)

每个人都上钩了吗？[图片积分—免费代码营](https://cdn-media-1.freecodecamp.org/images/1*0MgGEfZfLO91g1Oa2h3ebQ@2x.png)

**React hooks** 发布已经有一段时间了，大家都爱上了它们。我明白，因为我也是你们中的一员。钩子让我着迷！

钩子允许我们创建更小的、可组合的、可重用的、更易管理的 React 组件。

您可能使用钩子的一个用例是使用 **useState** 或 **useReducer 管理表单状态。**

让我们考虑一个场景，其中您必须管理具有多个表单输入的复杂表单状态，表单输入可以是几种不同的类型，如*文本、数字、日期*输入。表单状态甚至可以有嵌套信息，例如用户的地址信息，它有自己的子字段，如`address.addressLine1`、*、`address.addressLine2`等。*

也许您还必须根据当前状态更新表单状态，例如切换按钮。

现在，如果您对每个单独的表单字段使用 **useState** ，那么您就有能力根据当前状态计算新的状态。

```
const [modalActive, updateModal] = useState(false)
.
.
.
// new state based on previous
updateModal(prev => !prev)
```

但是，如果你有太多的单个表单字段，比如 100+ ( **YESS！！我管理着 100 多个表单字段**，这种方法并不友好。

想象...

```
const [firstName, setFirstName] = useState('')
const [middleName, setMiddleName] = useState('')
const [lastName, setLastName] = useState('')
.
.
.
.
```

编写单独的使用状态，然后为每个字段使用单独的更新函数是不实际的。我们的另一个选择是钩子。

让我们看一个例子。

```
const initialState = {
  firstName: '',
  lastName: ''
};function reducer(state, action) {
  switch (action.type) {
    case 'firstName':
      return { firstName: action.payload };
    case 'lastName':
      return { lastName: action.payload };
    default:
      throw new Error();
  }
}function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);return (
    <>
      <input
        type="text"
        name="firstName"
        placeholder="First Name"
        onChange={(event) => {
          dispatch({
           type: 'firstName',
           payload: event.target.value
          })
        }}
        value={state.firstName} />
      <input
        type="text"
        name="lastName"
        placeholder="Last Name"
        onChange={(event) => {
          dispatch({
           type: 'lastName',
           payload: event.target.value
          })
        }}
        value={state.lastName} />
   </>
  );
}
```

呃，不太好。您不可能为 reducer 中的那些 **n** 个表单字段编写每个用例。

但是 **useReducer** 中使用的 reducer 函数只是一个普通的函数，返回一个更新的状态对象。所以，我们可以做得更好。

```
function reducer(state, action) {
  // field name and value are retrieved from event.target
  const { name, value } = action

  // merge the old and new state
  return { ...state, [name]: value }
}
```

现在，这看起来像一个更好和更清洁的减速器。

然而，这不允许我们在用回调函数调用更新函数时根据当前状态计算新的状态，就像我们可以用`useState`做的那样:

```
this.setState((prev) => ({ isActive: !prev }))// orconst [modalActive, updateModal] = useState(false)
.
.
.
updateModal(prev => !prev)
```

还有，像`address.addressLine1` *，* `address.pinCode` *这样的嵌套状态更新呢？*

关于使用不太理想的方法来管理复杂的表单状态，我们已经讨论了很多。让我向你展示解决方案。

哒哒！！

这是处理复杂表单场景的完整源代码。

我来稍微解释一下减速器(`**enhancedReducer**` :P)的功能。

reducer 函数接收两个参数，第一个参数是更新前的当前状态。当您调用`updateState`**/**/`dispatch`**函数更新减速器状态时，该参数自动提供。reducer 函数的第二个参数是您调用`updateState` 函数所用的值。它不必是典型的*重复动作对象*采取`{ type: ‘something’, payload: ‘something’ }`的形式。它可以是任何东西，数字、字符串、对象甚至函数。**

**这就是我们正在利用的。如果`updateArg`是一个函数，我们用当前状态调用它来计算新的状态。我们从这个函数返回的任何对象都成为我们的新状态。**

**如果`updateArg`是一个普通的旧 Javascript 对象，那么有两种情况。**

**1: **对象没有** `**_path**` **和** `**_value**` **属性**，因此是一个普通的更新对象，就像我们给`this.setState`的一样。所以，你可以用一个新的对象调用`**updateState**` ，这个新的对象包含了你想要更新的状态片段，它会把它和旧的对象合并并返回新的状态。**

**2: **对象有** `**_path**` **和** `**_value**` **属性——用有这两个属性的对象调用`updateState`函数时有**。我们将此视为特殊情况，其中`**_path**`表示嵌套字段路径。以字符串形式，例如:`'address.pinCode'`或代表路径的数组`[‘address’, ‘pinCode’]`。**

**我们如何利用这样的路径表示来更新对象中的嵌套字段呢？我们将使用**洛达什**的`**set**`方法。它接受两种路径形式作为 update 和 object 的有效输入。**

```
set(objectToUpdate, path, newValue)const state = {
  name: {
   first: '',
   middle: '',
   last: ''
  }
}// and to update, for eg: first name.
// both ways of path are correct.set(state, 'name.first', 'Aditya')
set(state, ['name', 'first'], 'Aditya')
```

**然而，`**set**`方法就地改变对象并且不返回新的副本，但是在 React 世界中，改变检测依赖于**的不变性。**需要在内存中具有新位置的数据的全新副本来触发渲染。**

**为了绕过这一点，我们使用 **immer，**以一种易于使用的形式帮助处理 Javascript 对象的不变性。**

```
import produce from 'immer'produce(state, draft => {
  set(draft, _path, _value);
});
```

**来自immer 的`**produce**`函数把要处理的对象作为它的第一个参数，在我们的例子中它是当前状态，它的第二个参数是一个接收对象的**草稿副本**以进行变异的函数，无论你在草稿状态下在这个函数内部修改什么，都是在**副本**上完成的，而不是实际的输入对象**状态**就地完成的。然后，它自动返回带有**更新的**数据的**新对象**。**

**这是我们的增强型减速器:D**

**仅仅**

```
yarn add lodash immer
```

**尽情享受吧。**

**gist 中的示例可以进一步细化，在`**enhancedReducer**`中处理更多的边缘情况，并且可以通过映射表单规范对象来缩短表单字段代码，以动态创建它，并减少代码重复和其他一些事情。**

**你们中的一些人可能会想，如果我们这么想复制 **this.setState，**那么为什么不让 setState 的第二个参数回调函数在状态更新后执行一些操作呢？嗯，这还不够明确！我们将一步一步地告诉代码，如何做某事。而不是简单的告诉它做什么。我会使用`**useEffect**`而不是回调函数，因为它是声明性的，而*会对变化做出反应。***

**声明式与命令式和函数式编程是另一个完整的话题，我将在以后分享。**

**资源:**

 **[## Lodash 文档

### 编辑描述

lodash.com](https://lodash.com/docs/4.17.15#set)** **[](https://github.com/immerjs/immer) [## immerjs/immer

### 通过简单地修改“年度突破”的当前树获胜者，创建下一个不可变的状态树…

github.com](https://github.com/immerjs/immer)**