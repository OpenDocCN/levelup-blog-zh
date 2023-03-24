# 学习 React.js 中的高阶组件

> 原文：<https://levelup.gitconnected.com/learning-higher-order-components-in-react-js-2245c1a5ff4d>

![](img/885146cc2aee4b5a5b7759bfa45b2a9e.png)

Wojciech Portnicki 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

高阶组件是 React 中使用的一种技术，它允许我们重用组件逻辑。这方面的一个例子是根据用户是否登录来有条件地呈现的组件。

更具体地说，高阶分量是一个函数，它将一个分量作为参数，并返回一个分量。

对于本演练，让我们构建一个简单的 React 应用程序，它将使用高阶组件来保护页面，需要有人登录才能查看它。我们将使用一个按钮来控制登录/退出状态，这样我们就可以专注于 HOC。然后，我们将创建另一个 HOC 来根据是否加载了我们的 API 或数据库内容来保护页面，并且我们将嵌套 HOC！首先，让我们创建一个新的 React 项目:

```
#=> npx create-react-app learning-hoc
#=> cd learning-hoc
#=> code .
```

我们把`App.js`清理出来，改成类组件。我们正在更改它，以便我们可以在我们的演练中以简单的方式使用状态。当我们完成时，它看起来像这样:

```
// App.jsimport React from 'react';
import './App.css';class App extends React.Component {
    render () {
        return (
            <div className="App">

            </div>
        )
    }
};export default App;
```

接下来，让我们快速构建一个组件来保存我们想要保护的内容。出于本演练的目的，这将非常简单:

```
#=> cd src
#=> mkdir components
#=> cd components
#=> touch Content.js
```

在`Content.js`文件中，我们将建立一个简单的类组件，它返回我们的超级机密保护信息，包装在一个`<h1>`标签中:

```
// ./components/Content.jsimport React from 'react';class Content extends React.Component {
    render () {
        return (
            <h1>Super Secret Protected Information</h1>
        )
    }
};export default Content;
```

让我们将它导入到`App.js`中，并使用`npm start`来确保我们可以看到它:

```
// App.jsimport React from 'react';
import './App.css';
import Content from './components/Content.js'class App extends React.Component {
    render () {
        return (
            <div className="App">
                <Content />
            </div>
        )
    }
};export default App;
```

酷，我们可以看到我们的超级秘密信息！但是，我们如何控制对它的访问，要求用户登录才能看到它呢？

让我们构建我们的隐私高阶组件，它将在呈现我们通过它传递的任何组件之前验证用户是否登录。对我们来说，那将是`<Content />`。因此，让我们在`/src/`中创建一个名为 HOCs 的新文件夹，然后在该文件夹中创建我们的`PrivacyHOC.js`文件:

```
#=> cd src
#=> mkdir HOCs
#=> cd HOCs
#=> touch PrivacyHOC.js
```

太好了！让我们开始构建高阶组件吧！我们将从设置骨架开始:

```
// ./HOCs/PrivacyHOC.jsimport React from 'react';export default function PrivacyHOC(WrappedComponent) {
    return (
        class PrivacyHOC extends React.Component {
            render () {
                return <WrappedComponent />
            }
        }
    )
};
```

让我们花一点时间来思考我们正在做的事情。我们的函数`PrivacyHOC`将一个组件作为参数。然后，它返回该组件。现在够简单了，对吧？

那么，我们如何用这个`PrivacyHOC`组件包装我们的`Content`组件呢？让我们跳回`Content.js`，改变我们的出口声明。我们将导出我们的`PrivacyHOC`函数，并通过它传递`Content`，而不是导出`Content`:

```
// ./components/Content.jsimport React from 'react';
***import PrivacyHOC from '../HOCs/PrivacyHOC.js'***class Content extends React.Component {
    render () {
        return (
            <h1>Super Secret Protected Information</h1>
        )
    }
};***export default PrivacyHOC(Content);***
```

酷！检查浏览器，确保它显示的是超级机密信息！好了，现在我们需要构建一些逻辑，在显示`Content`之前查看我们是否登录。让我们考虑一下我们希望如何做到这一点…

首先，在`App.js`中，我们希望用状态中的一个布尔值来跟踪登录状态，然后将它作为道具传递给`Content`组件。因为`Content`通过`Content`的导出语句包装在`PrivacyHOC`中，`PrivacyHOC`将可以访问传递给`Content`的道具。我们可以使用它根据登录状态有条件地呈现`Content`。我们可以在以后添加更多的复杂性，但是让我们设置它以确保它能工作:

```
// App.jsimport React from 'react';
import './App.css';
import Content from './components/Content.js'class App extends React.Component {
    ***state = {
        loggedIn : true
    }***
    render () {
        return (
            <div className="App">
                <Content ***loggedIn={this.state.loggedIn}*** />
            </div>
        )
    }
};export default App;
```

上面，我们已经在`App`级别将`loggedIn`添加到我们的状态中，并将其设置为`true`。我们还将该值传递给了`Content`(以及随后的`PrivacyHOC`)，因此我们可以通过 props 在那些组件中访问它。让我们在`PrivacyHOC`中设置我们的条件渲染:

```
// ./HOCs/PrivacyHOC.jsimport React from 'react';export default function PrivacyHOC(WrappedComponent) {
    return (
        class PrivacyHOC extends React.Component {
            ***isLoggedIn = () => {
                return this.props.loggedIn
            }***
            render () {
                return ***this.isLoggedIn() ?
                    <WrappedComponent {...this.props} /> :
                    <h1>You must be logged in</h1>***
            }
        }
    )
};
```

上面，我们有一个函数，`isLoggedIn`，它返回`this.props.loggedIn`的值，这个值是从`App`的状态传递过来的。在返回组件的呈现中，我们使用了基于`this.isLoggedIn`值的三元语句。如果是`true`，它将返回并呈现`<WrappedComponent />`，在本例中是`Content`。如果是`false`，它会告诉我们必须登录。

*注意，我们把* `*{…this.props}*` *加进了我们的* `*<WrappedComponent />*` *。这将传递和传播所有的道具，所以我们可以在组件中访问它们。当我们将一个特设嵌套在另一个特设中时，这一点尤其重要。*

所以，因为我们的`App`的`state.loggedIn`被设置为`true`，我们应该看到`Content`。请回到您的浏览器进行验证。如果工作正常，将`state.loggedIn`改为`false`，然后再次检查。它应该告诉我们，我们必须登录。

嘿，你猜怎么着？您刚刚构建并实现了一个高阶组件！我现在很想和你击掌。让我们乘着这股浪潮，把我们的项目建设得更好一些。让我们制作一个按钮来切换我们的登录状态，这样我们就可以实时看到变化。之后，我们将根据内容是从数据库还是从 API 加载来控制呈现的内容。

让我们从`App.js`中的一个按钮开始，这个按钮将切换它的`state.loggedIn`布尔值。我们还将在屏幕上显示`state.loggedIn`的当前值:

```
// App.jsimport React from 'react';
import './App.css';
import Content from './components/Content.js'class App extends React.Component {
    state = {
        loggedIn : true
    }
    render () {
        return (
            <div className="App">
                ***{this.state.loggedIn ? <h2>You are logged in!</h2> : <h2>You are logged out :(</h2>}
                <button onClick={() => this.setState({loggedIn : !this.state.loggedIn})}>
                    Toggle Login/Logout
                </button>***
                <Content loggedIn={this.state.loggedIn} />
            </div>
        )
    }
};export default App;
```

上面，我们已经设置了一个三元语句，基于`this.state.loggedIn`的值，它将告诉我们已经登录或者已经注销。下面是一个按钮，用于切换`this.state.loggedIn`的值。现在，我们可以实时看到我们的`PrivacyHOC`如何控制屏幕上显示的内容！

总结一下，现在让我们构建一个`LoaderHOC`来根据内容是否被加载来控制呈现的内容。这样，当我们的 API 或数据库加载和渲染时，我们可以在屏幕上让用户看到一些东西，而不是挂着，冒着用户移动的风险。`LoaderHOC`看起来与`PrivacyHOC`几乎相同:

```
// ./HOCs/LoaderHOC.jsimport React from 'react';export default function LoaderHOC(WrappedComponent) {
    return (
        class LoaderHOC extends React.Component {
            isLoaded = () => {
                return this.props.loaded
            }
            render () {
                return this.isLoaded() ? <WrappedComponent {...this.props} /> : <h1>Content loading...</h1>
            }
        }
    )
}
```

上面，就像`PrivacyHOC`一样，我们将一个组件作为参数，并使用一个三元组来检查`App`的`state.loaded`的状态，以显示我们的`Content`或显示内容正在加载的消息。

让我们在`App.js`把这个连接起来:

```
// App.jsimport React from 'react';
import './App.css';
import Content from './components/Content.js'class App extends React.Component {
    state = {
        loggedIn : true,
 ***loaded : false***    }
    render () {
        return (
            <div className="App">
                ***<div>***
                    {this.state.loggedIn ? <h2>You are logged in!</h2> : <h2>You are logged out :(</h2>}
                    <button onClick={() => this.setState({loggedIn : !this.state.loggedIn})}>
                        Toggle Login/Logout
                    </button>
                ***</div>
                <div>
                    {this.state.loaded ? <h2>Content loaded</h2> : <h2>Content loading...</h2>}
                    <button onClick={() => this.setState({loaded : !this.state.loaded})}>
                        Toggle Content Loaded
                    </button>
                </div>***
            <Content loggedIn={this.state.loggedIn} />
            </div>
        )
    }
};export default App;
```

我们的最后一步是将`LoaderHOC`嵌套在`Content.js`导出语句的`PrivacyHOC`中:

```
// ./components/Content.jsimport React from 'react';
***import PrivacyHOC from '../HOCs/PrivacyHOC.js'
import LoaderHOC from '../HOCs/LoaderHOC.js'***class Content extends React.Component {
    render () {
        return (
            <h1>Super Secret Protected Information</h1>
        )
    }
};***export default PrivacyHOC(LoaderHOC(Content));***
```

现在，`PrivacyHOC`控制着我们的`Content`，不管装没装！相当酷！

随着我们的应用程序的增长，我们可以将这些 hoc 用于任何我们想要的组件——我们所要做的就是将我们的组件通过该组件的导出语句中的 hoc！

我希望这能帮助你更好地理解高阶函数，我很想听听你的经验或想法！