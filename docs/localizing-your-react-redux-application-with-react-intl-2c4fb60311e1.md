# 用 React-Intl 本地化 React-Redux 应用程序

> 原文：<https://levelup.gitconnected.com/localizing-your-react-redux-application-with-react-intl-2c4fb60311e1>

![](img/14535d8750f794c20fc7da6c54016232.png)

这个主题在教程中并不经常出现，也不总是你在开始一个新项目时要做的事情，如果你作为一名开发人员在你的职业生涯中足够幸运，能够在一个新项目的开始阶段就在那里。但是本地化一个应用程序，让用户可以用他们的母语阅读文本，这是我曾经参与过两次的事情。

第一次是本地化遗留的 Winforms 应用程序，我将在以后的文章中讨论。第二个是用它最喜欢的合作伙伴 Redux 本地化新的 React 应用程序。

([https://github.com/yahoo/react-intl](https://github.com/yahoo/react-intl))是一个帮助你完成大量繁重的翻译和数字格式化工作的库。在这篇文章中，我将重点介绍翻译以及如何更新 Redux store，以便根据用户交互来改变 UI 文本语言。

我假设您知道如何设置 React 和 Redux，并且您知道什么是 reducers 和 action creators。

要做的第一件事是安装`react-intl`,使用 npm 可以做到这一点:

```
npm i react-intl
```

# 多余的部分

有几个来自`react-intl`的移动部件帮助完成所有这些工作:

IntlProvider —它位于应用程序 JSX 的根，在组件树中将有翻译的文本。本质上，可以认为它类似于 react-redux 提供程序或 React-Router 路由器对象。

**addLocaleData** —添加在应用程序初始化期间设置的语言环境数据的函数。

一个普通的旧 JSON 对象(稍后将详细介绍)，但本质上它将保存基于地区代码的键，例如英语的“en”或日语的“ja”，然后保存翻译的值。

**FormattedMessage** —这个对象是我们将用来包装翻译的对象。它有两个属性，第一个`id`是 JSON 对象中包含的翻译的关键字，第二个是`defaultMessage`，如果在显示翻译时出现问题。

# 给我看看代码

让我们从应用程序的根开始，通常您会看到这样的内容:

`ApplicationTree`在这种情况下，是应用程序的其余部分，无论它被 Redux 提供者包装成什么样。

为了继续使用`ApplicationTree`，它看起来应该是这样的:

这里有几件事情正在进行，首先是我们引入了我们希望稍后在我们的 JSX 中使用的组件(`IntlProvider`和`addLocalData`)，然后我们导入了我们的`LanguageObject`(我们的 JSON 翻译数据)并引入了我们的`HomeComponent`，这将是一个连接到 react-redux 的页面组件，并将包含一些文本，这些文本将以我们选择的语言在页面上更新。

接下来，我们在第 9–10 行引入我们的语言环境，然后将这些数据添加到第 12–13 行的`IntlProvider`的上下文中。

我们还有`mapStateToProps`,它设置组件的`props.locale`属性，并在选择不同的语言环境时更新应用程序`IntlProvider`。

`IntlProvider`需要知道区域设置是什么，这是通过它的“区域设置”属性来完成的，我们已经将它设置为组件的`props.locale`，最后，它使用它的“消息”属性来获取一个消息对象，该属性也是根据 props.locale 来设置的。

上面提到的 messages 对象是一个 JSON 对象，它包含将在整个应用程序中使用的关键字和翻译，这可以根据您的需要分解为单独的文件，我们的示例在一个文件中，如下所示:

`props.locale`将是一个字符串,‘en’或‘ja ’,并将被用作选择包含翻译的内部对象的键。

我们的`HomeComponent`连接到 react-redux，包含`FormattedMessage`组件，如下所示:

为了简单起见，我直接从`props.dispatch`导入了动作创建器和直接调度。有三个`FormattedMessage`组件，一个包含标题和两个按钮，这两个按钮将执行一个 action creator 函数，每个按钮接受一个区域设置字符串(' en '或' ja ')并将它们发送到 reducer。

正如您所看到的，如果更新中有任何错误，或者错误的密钥被发送到一个组件，那么将会显示默认消息。

动作类型和动作创建者非常简单，如下所示:

当然，最后一部分是我们的区域设置缩减器，它将包含一个动作类型并创建一个新状态，该状态在更新时将更新`ApplicationTree`组件，该组件将向下过滤并更新包含`FormattedMessage`组件的其他组件，缩减器的代码如下所示:

当然，在创建您的商店时，您一定不要忘记将 reducer 添加到`combineReducers` Redux 方法中。

# 包扎

这就是为 react Redux 应用程序添加基本本地化的基本内容，在您可能添加到应用程序的任何新功能中，您都可以找到基本的活动部分，即 reducers、action creators 和组件，它们都与应用程序的流程一起工作。

如果您碰巧安装了 Redux chrome 扩展并将其安装到您的应用程序中，您可以看到应用程序更改状态，并看到 Redux 存储中的区域设置是什么。

我希望本指南提供了一个很好的介绍，并让您开始使用 react-intl。