# Redux 的元素

> 原文：<https://levelup.gitconnected.com/elements-of-redux-8ae01c259f13>

*在李中清的这篇文章中了解 Redux 的元素，他是一位热情的软件向导，在硅谷一家专注于大数据分析的顶级初创公司工作。*

![](img/30b218c17ed79b9ca4fb3013e9098ead.png)

随着 web 应用程序的流行和公司从传统的基于桌面的系统向基于 web 的系统转型，现在 web 上的应用程序有大量的机会。服务器端脚本(SSR)、客户端脚本、表示逻辑(HTML 和 CSS)和查询语言有多种编程语言。

JavaScript 是网络上最流行的语言之一，它包含了几个框架来帮助它的开发、编译和生产。React 是最流行的 JavaScript 框架之一，由脸书开发和发布。React 有助于构建高度复杂的交互式用户界面，而 Redux 在前端社区的状态管理中却越来越受欢迎。

要理解 Redux，我们需要理解它的组件。Redux 有四个主要元素；让我们逐一讨论它们。

# 行动

动作只是描述应用程序状态变化的 JavaScript 对象。具体来说，它们是将数据从我们的应用程序传输到状态的信息负载。这对你来说没有意义吗？没问题！让我们看一个示例用例。假设我们需要将医生的信息添加到我们的医院管理系统中:

```
const ADD_NEW_DOCTOR_REQUEST = "ADD_NEW_DOCTOR_REQUEST"
```

这不是火箭科学，对吗？只是一个简单的，不变的`ADD_DOCTOR_REQUEST`。现在，让我们创建一个对象:

```
{
 type: ADD_NEW_DOCTOR_REQUEST,
 data: {
   name: ‘Dr. Yoshmi Mukhiya’,
   age: 22,
   department: ‘Mental Health’,
   telecom: ‘99999999’
 }
}
```

这是一个简单的普通 JavaScript 对象，它被称为一个动作。动作必须具有定义要执行的动作类型的`type`属性。在这个用例中，动作是添加一个动作。该类型基本上是一个字符串常量。在任何 web 应用程序中，都需要大量的操作。因此，一般的(也是最常见的)趋势是将这些动作分离到单独的文件中，并将它们导入到所需的位置。

现在，让我们假设我们需要从应用程序中删除一个医生的记录。我们应该能够轻松地创建一个操作对象，如下所示:

```
{
 type: 'DELETE_DOCTOR_REQUEST',
 identifier: 201,
}
```

现在，继续为以下内容创建操作:

1.向医院管理系统添加用户

2.从医院管理系统中删除用户

3.更新用户

# 动作创建者

接受一些参数并返回动作的 JavaScript 函数是**动作创建器**。让我们看一个用于向应用程序添加新医生的动作创建器函数:

```
function addNewDoctor(data) {
 return {
   type: ADD_NEW_DOCTOR_REQUEST,
   data
 };
}
```

现在，您可以考虑删除记录可能需要的函数，如下所示:

```
function deleteDoctor(identifier) {
 return {
   type: "DELETE_DOCTOR_REQUEST",
   identifier
 };
}
```

在我们继续讨论 reducers 之前，让我们再创建一个用于身份验证的 action creator。通常，我们使用电子邮件和密码进行身份验证。因此，为了认证(或不认证)，我们需要定义动作。请注意，我们定义的操作将在我们的医院管理系统项目中使用。我们的身份验证操作可能如下所示:

```
export const authenticate = (credentials) => ({
 type: "AUTHENTICATE",
 payload: credentials
});
export const deauthenticate = () => ({
 type: "DEAUTHENTICATE"
});
```

类似地，让我们为注册用户创建动作创建者。当我们注册一个用户时，我们可能会有一个请求，一个成功，或者一个失败。基于这三种状态，我们可以创建操作创建者，如下所示:

```
export const onRegisterRequest = user => ({ type: REGISTER_REQUEST, user });

export const onRegisterSuccess = user => ({ type: REGISTER_SUCCESS, user });

export const onRegisterFailure = message => ({
  type: REGISTER_FAILURE,
  message,
});
```

# 还原剂

将动作和状态作为输入并返回新状态的 JavaScript 函数是**reducer**。好吧，如果这令人困惑，试着记住动作只描述发生了什么，而不是应用程序状态如何转换。

了解减速器的功能非常重要。让我们考虑一下我们的医院管理系统。我们的应用程序的状态可能如下所示:

```
{
 doctors: [
   {
     name: "John Doe",
     department: "Radiology",
     address: "Kathmandu, 4017, Nepal",
     telecom: "999-999-999"
   },
   {
     name: "Ola Nordmann",
     department: "General Physician",
     address: "Kong Oscarsgate 29, 5017, Bergen, Norway",
     telecom: "111-111-1111"
   }
 ];
}
```

当创建一个 reducer 函数时，重要的是我们要记住 reducer 原则:它必须是一个纯函数。它应该只是采取行动，并返回一个新的状态，没有副作用，没有突变，也没有 API 调用。

让我们考虑另一个内容管理系统的例子。在一个正常的 CMS，我们有职位和类别。因此，实例的状态可能如下所示:

```
{
 posts: [
   { user: 'John Doe', category: 'Practitioner', text: 'This is the first post about Practitioner.' },
   { user: 'Ola Nordmann', category: 'Patients', text: 'This is the first post about Patients.' }
 ],
 filter: ‘Patients’
}
```

这里没什么复杂的，对吧？现在，让我们开始为两个用例编写我们的 reducer 函数:我们的 CMS 用例以及我们的医院管理系统用例。

我们将从定义一个初始状态开始。让我们通过创建一个包含空医生数组的空对象来初始化我们的初始状态:

```
const initialState = {
 doctors: []
};
```

在任何数据库中，都需要创建、更新、读取和删除资源。同样，在医院管理系统中，我们需要读取医生的记录、创建新记录、更新记录或删除记录。因此，正如我们在上一节中提到的，我们很可能定义了多个 action 对象。

这引入了为每个动作处理 reducer 函数的需求。我们可以创建一个 reducer 函数来处理类似的场景，并利用`switch`案例来处理多种动作类型:

```
import {
 ADD_NEW_DOCTOR_REQUEST,
} from './actions'

function addDoctor(state = initialState, action) {
 switch (action.type) {
   case ADD_NEW_DOCTOR_REQUEST:
     return Object.assign({}, state, {
       doctors: [
         ...state.doctors,
         {
           name: action.name,
           age: action.age,
           department: action.department,
           telecom: action.telecom
         }
       ]
     });
   default:
     return state;
 }
}
```

在前面的代码片段中，我们已经在动作中定义了`ADD_NEW_DOCTOR_REQUEST`。我们可以检查删除医生记录的操作类型。继续添加一个 reducer 用例来删除一个医生。

现在，你的任务是检查 CMS 系统的初始状态，并为`CREATE_POST`、`EDIT_POST`和`SET_FILTER`编写 reducer 函数。一旦您完成了 reducer 函数的编写，它应该看起来像下面这样:

```
import { CREATE_POST, EDIT_POST, SET_FILTER } from './actionTypes'

function postsReducer (state = [], action) {
 switch (action.type) {
   case CREATE_POST: {
     const { type, ...post } = action
     return [ ...state, post ]
   }

   case EDIT_POST: {
     const { type, id, ...newPost } = action
     return state.map((oldPost, index) =>
       action.id === index
         ? { ...oldPost, ...newPost }
         : oldPost
     )
   }

   default:
     return state
 }
}
```

# 商店

存储区存储应用程序的所有状态。因此，它有时被称为应用程序的核心。需要注意的最重要的一点是，整个应用程序中只有一个商店。创建一个商店，我们可以使用 Redux 提供的`createStore`函数:

```
import { createStore } from 'redux'
import doctorsReducer from './reducers'
const store = createStore(doctorsReducer)
```

商店的方法将在下面的小节中解释。

## getState()

`getState()`方法给出了任何应用程序的当前状态，它等于应用程序的 reducer 返回的最后一个值。

## 派遣(行动)

顾名思义，`dispatch(action)`只派遣行动。要记住的要点是，这是修改状态的唯一方法。

## 订阅(听众)

`subscribe(listeners)`方法添加了一个变更监听器，它会在任何时候调度一个动作时被调用，并且状态树的某些部分可能已经发生了变更。

## 替换减速器(nextReducer)

`replaceReducer(nextReducer)`方法取代了商店目前用来计算状态的减速器。它是一个高级 API，正常使用情况下可能不需要。

*如果你觉得这篇文章很有趣，可以探索一下* [*Redux 快速入门指南*](https://www.amazon.com/Redux-Quick-Start-Guide-beginners-ebook/dp/B07P9H3C72?utm_source=levelup.gitconnected&utm_medium=referral&utm_campaign=ThirdPartyPromotions) *将 Redux 与 React 等前端 JavaScript 框架高效集成，有效管理应用状态。* [*Redux 快速入门指南*](https://www.packtpub.com/web-development/redux-quick-start-guide?utm_source=levelup.gitconnected&utm_medium=referral&utm_campaign=ThirdPartyPromotions) *遵循测试驱动开发(TDD)方法来开发单页面应用程序，并探索扩展应用程序以进行进一步研究的可能性，包括部署和优化。*