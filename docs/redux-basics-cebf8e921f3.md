# Redux 基础

> 原文：<https://levelup.gitconnected.com/redux-basics-cebf8e921f3>

![](img/f90c35636c10bee5167aa38425b91218.png)

在 Redux 的世界里有很多术语。动作、动作创建者、动作类型、归约者、根归约者……这些都是什么？在所有复杂的术语背后，有一些简单的代码极大地改进了我们组织和编写 React 应用程序的方式。在这篇文章中，我将尝试解释一些基本的 Redux 概念。我们还将了解一些与 React 和 Redux 一起使用的常用 npm 包。

为了说明和解释 Redux 基础知识，我们将使用一个超级简单的 React 应用程序。我们将构建一个组件，该组件返回一个按钮元素，用户可以单击该元素来切换和显示一些文本。就像我说的，超级简单！

[*在这里查看代码。*](https://github.com/catedm/super-basic-redux-example)

```
redux-example
  node_modules
  public
  src
    actions
      actions.js
      types.js
    components
      Toggle.js
    reducers
      toggle.js
    App.js
    index.js
    rootReducer.js
    serviceWorker.js
.gitignore
package-json.lock
package.json
README.md
yarn.lock
```

我们要仔细查看的文件是嵌套在`src`文件夹下的所有文件，还有`rootReducer.js`文件和`App.js`

让我们从分解行动开始。

# 行动

[Redux 文档](https://redux.js.org/basics/actions)将动作定义为“将数据从应用程序发送到商店的信息负载”(先不要担心商店是什么，我们稍后会将所有内容联系在一起)。Redux 文档很好地定义了动作。动作并不复杂；事实上，一个动作只是一个普通的 Javascript 对象。看一下下面的例子:

```
// We'll talk about this line of code a little later
import { TOGGLE_MESSAGE_VISIBILITY } from './types';// A basic Redux action (it's just a Javascript object)
{ type: TOGGLE_MESSAGE_VISIBILITY }
```

上面代码的第 4 行是有效的 Redux 操作。它只是一个 Javascript 对象！Redux 中的动作必须有一个`type`属性来指示正在执行的动作的类型。你可以随便叫他们什么，但他们应该试图解释正在执行的动作。除此之外，您可以自由地将对您的应用程序有意义的任何属性添加到您的特定操作中。但是第 1 行的 import 语句发生了什么呢？您经常会在 React/Redux 应用程序中看到这种模式。请注意，在我们项目的 actions 目录下，有一个名为`types.js`的文件，如下所示:

```
export const TOGGLE_MESSAGE_VISIBILITY = 'TOGGLE_MESSAGE_VISIBILITY';
```

这些被称为*类型动作常量*，程序员喜欢使用它们的原因是为了代码组织(将我们所有的动作类型保存在一个文件中，然后根据具体情况将它们导入到其他文件中)以及防止在我们从 reducers 内部调用动作时出现错误。请记住这一点，稍后我会解释为什么这有助于防止减速器中的错误。

所以我们的动作只是普通的 Javascript 对象。好的。太好了。现在怎么办？现在我们用我们的行动让*成为行动创造者。哦不，更多 Redux 行话*！不要被吓倒。动作创作者也非常简单。让我们来看一个动作创建器，它使用我们上面编写的动作:

```
import { TOGGLE_MESSAGE_VISIBILITY } from './types';// this is our action creator. it's just a function that returns an object (our action!)
export function toggleMe() {
  return {
    type: TOGGLE_MESSAGE_VISIBILITY,
  }
};
```

正如您在上面看到的，我们的 action creator 只是一个返回我们的对象的 plan javascript 函数。它们是创建(返回)动作的函数！我们需要导出我们的动作创建器，因为我们最终将把它导入到我们的`Toggle.js`组件文件中，以使用它来*分派*TOGGLE _ MESSAGE _ VISIBILITY 动作。但是我们不要想太多！接下来让我们看看减速器。前进！

# 还原剂

在 Redux 应用程序中，应用程序状态存储为单个对象。当一个动作被发送到存储库时(我们将很快定义存储库)，reducers 处理状态将如何响应该动作而改变。在应用程序的状态中，我们只跟踪一件事:我们的消息是否可见。让我们看看嵌套在 reducers 目录下的`toggle.js`文件:

```
// Now we will get to why this pattern prevents bugs
import { TOGGLE_MESSAGE_VISIBILITY } from '../actions/types';// Here we define the initial state of our application
// Initially, our message is not visible
const initialState = {
  messageVisibility: false,
}// This is our REDUCER
export default function (state = initialState, action) {
  // here we pluck off action.type and store it in the const type
  // using some es6 destructuring
  const { type } = action;switch (type) {
    case TOGGLE_MESSAGE_VISIBILITY: {
      return {
        ...state,
        messageVisibility: !state.messageVisibility,
      }
    }
    default: {
      return state
    }
  }
}
```

让我们从第一行开始。还记得我告诉过你使用*动作类型常量*将有助于防止我们代码中的错误吗？为了说明为什么会这样，我将在下面展示没有动作类型常量的代码。我将使用基本字符串，而不是使用我们传递给动作创建者和缩减者的动作类型常量。

```
// this is our actions > actions.js fileexport function toggleMe() {
  return {
    type: "TOGGLE_MESSAGE_VISIBILITY",
  }
};// this is our reducers > toggle.js fileconst initialState = {
  messageVisibility: false,
}export default function (state = initialState, action) {
  const { type } = action;

 // Notice the TYPO in "TOGLE_MESSAGE_VISIBILITY" instead of "TOGGLE_MESSAGE_VISIBILITY" below
  switch (type) {
    case "TOGLE_MESSAGE_VISIBILITY": {
      return {
        ...state,
        messageVisibility: !state.messageVisibility,
      }
    }
    default: {
      return state
    }
  }
}
```

在上面的例子中，我们没有使用动作类型常量来构建我们的动作创建器，而是使用了一个基本字符串。然后，在我们的 reducer 中，我们*拼错了*我们想要在 switch 语句中触发的动作(我们稍后将分解 reducer 语法)。上面代码的问题是，它不会做我们想要它做的事情， ***但是它不会产生错误*** 。代码仍然会运行，因为这是一个有效的字符串。然而，如果我们加载一个动作类型常量，然后拼错了*常量，* **编译器将抛出一个错误，并告诉我们哪里出错了。**

现在我们理解了为什么我们使用动作类型常量，但是这个文件的其余部分是什么呢？我们从指定我们想要的初始状态开始。对我们来说，我们希望我们的信息最初是隐藏的:

```
const initialState = {
  messageVisibility: false,
}
```

同样，这是一个非常简单的例子。在较大的应用程序中，初始状态下可能会有更多的属性。最后，让我们来分解实际的减速器:

```
export default function (state = initialState, action) {
  const { type } = action;switch (type) {
    case TOGGLE_MESSAGE_VISIBILITY: {
      return {
        ...state,
        messageVisibility: !state.messageVisibility,
      }
    }
    default: {
      return state
    }
  }
}
```

我们从导出我们的减速器函数开始，因为*稍后我们将把我们所有的减速器加载到一个* `*rootReducer.js*` *文件中。我们很快就会谈到这一点。然后，我们将状态参数设置为上面编码的初始状态对象，然后传入我们的操作。在第 2 行，我们取出`action.type`并使用 ES6 析构将其存储在常量`type`中。*

然后，我们开始讨论缩减器的真正内容:switch 语句。我们的 switch 语句查看我们的动作类型，并根据我们希望该动作如何改变我们的状态来设置我们的状态。在我们这个简单的例子中，我们希望将`messageVisibility`从`false`切换到`true`，反之亦然，以显示和隐藏我们的消息。

让我们仔细看看减速器的这一部分:

```
case TOGGLE_MESSAGE_VISIBILITY: {
  return {
    ...state,
    messageVisibility: !state.messageVisibility,
  }
}
```

`…state`是干什么用的？记住，*我们永远不应该在 React 应用程序中直接改变我们的状态*。Javascript ES6 中提供了上述语法。它的作用与下面的代码相同:

```
// This code does the same thing as the code above
case TOGGLE_MESSAGE_VISIBILITY: {
  return {
      Object.assign({}, state, {
          messageVisibility: !state.messageVisibility,
      })
  }
}
```

这将复制我们的状态，并更改我们想要更改的属性。在 Redux 中，我们不能改变我们的状态，而是必须返回它的一个副本。

现在我们已经设置好了我们的 reducer，我们需要看看我们的`rootReducer.js`文件和`App.js`文件，以便将所有这些结合在一起。让我们来分解一下`rootReducer.js`文件:

```
import { combineReducers } from 'redux';import toggle from './reducers/toggle';const rootReducer = combineReducers({
  toggle
});export default rootReducer;
```

我们从从 redux 导入`combineReducers`方法开始(这个例子有点做作，因为我们的`combineReducers` 方法没有多个 reducers，只是滚动了一下)。接下来，我们进口我们的`toggle`减速器。然后我们调用`combineReducers`方法，传入我们的`toggle`缩减器，将结果保存到一个名为`rootReducer`的常量中，我们在最后一行导出这个常量。

现在，我们已经将所有的 reducers 合并到一个 const ( `rootReducer`)中，供应用程序的其余部分使用。这很重要，因为*我们会用这个* `*rootReducer*` *来创建我们的* `*store*` *。*接下来说说店铺。

# Redux 商店

当我们使用 Redux 时，存储是保存我们的应用程序状态的东西。每个 Redux 应用程序将只有一个单一的商店。Redux 中的存储非常棒，因为它允许我们从应用程序中的任何组件访问应用程序的状态，这在只使用 React 构建应用程序时是不可能的。连接我们的 Redux 商店非常简单。让我们看看拿出来的`App.js`文件:

```
import React, { Component } from 'react';import { Provider } from 'react-redux';
import { createStore } from 'redux';import Toggle from './components/Toggle';
import rootReducer from './rootReducer';const store = createStore(
  rootReducer
);class App extends Component {
  render() {
    return (
      <Provider store={store}>
        <Toggle />
      </Provider>
    );
  }
}export default App;
```

我们从导入 react 和 React 中的组件开始。没什么新鲜的。接下来我们从 react-redux 导入`Provider`。react-redux npm 模块帮助我们连接 redux 以轻松做出反应。我们从 redux 导入`createStore`函数，然后导入我们的`Toggle`组件和我们在上一节刚刚创建的`rootReducer`。

记住，我们的`rootReducer`常量将我们应用中的所有减速器组合成一个减速器。我们使用之前导入的`createStore`函数创建我们的商店，并传入我们的 rootReducer。最后，在我们的`render`函数的`return`语句中，我们将我们的应用程序包装在`Provider`中，并将我们的`store`传递给它。

就是这样！对于一个非常小的应用程序来说，这是一个冗长的解释，但是我希望它能够帮助您理解 Redux 的一些基本概念。如果你愿意的话，请就如何让这篇文章更好发表评论，提出更正或建议。

[![](img/9914c5dd23ac08b70eea6f4f9ba6fed2.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/redux) [## 学习 Redux -最佳 Redux 教程(2019) | gitconnected

### 8 大 Redux 教程-免费学习 Redux。课程由开发人员提交并投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/redux)