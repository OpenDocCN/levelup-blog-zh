# 使用 UI-Router 和 React 实现更好的应用程序路由

> 原文：<https://levelup.gitconnected.com/using-ui-router-with-react-for-better-app-routing-f801e4d6a404>

***(这是我几个月前为早期版本的 UI-Router 写的帖子的修订版。概念是相同的；刚刚更新，以反映一些小的变化，在如何 UI-路由器应实施。)***

React 对其核心产品非常挑剔。这导致了一个更精简的库，并使它非常可扩展，允许开发人员选择和安装最适合他们项目的包。

路由是构建单页 web 应用程序的主要部分。React 没有现成的这种功能。框架没有内置的路由库，你必须安装自己的解决方案。这是 React 给开发人员足够多的自缚手脚的地方。

![](img/fafdcc27de42ef68104400a15ae0ebda.png)

照片由[杰米街](https://unsplash.com/photos/XhzGiOXw8yE?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上的 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

向 React 应用程序添加路由的首选解决方案——可怕的(在我看来) [React Router](https://reacttraining.com/react-router/) 包。我知道我并不孤单。我经常和其他开发者讨论使用它有多令人沮丧。

将路由实现为组件是一件很麻烦的事情，而且看起来也不太美观。如果你有一个有很多路线的应用程序，这是一个很难管理的问题。我开发的应用程序有超过 100 条独特的路线。如果我们决定重建，React 路由器并不是一个实用的解决方案。

我会选择的解决方案是 [UI 路由器](https://ui-router.github.io/react/)。在我从事 AngularJS 项目的时候，我已经接触了很多 UI-Router 并取得了很大的成功。它是框架独立的。然而，它有一个专用的 React 包，所以将它添加到项目中是相当容易的。这是我在 React 项目中使用它的方式。

[看看我放在 Github 上的这个演示，它是一个工作示例](https://github.com/carmichaelize/react-ui-router-demo)。

# 路由器设置

`UIView`组件允许将路线内容加载到应用程序包装组件的一个区域中。像应用程序的页眉和页脚这样的顶级组件可以添加到这里，这样它们就可以显示在每个页面上。

```
// App.jsx
import React, { Component } from 'react';
import { UIRouter, UIView } from '[@uirouter/react](http://twitter.com/uirouter/react)';
import { router } from './router';class App extends Component {render() {
        return (
            <UIRouter router={router}>
                <div>
                    <UIView/>
                </div>
            </UIRouter>
        );
    }}export default App;
```

路由器设置文件是配置和启动路由器实例的地方。路由器对象需要被传递到顶层`UIRouter`(如上所示)。

```
// router.jsx
import { UIRouterReact, servicesPlugin, hashLocationPlugin } from '[@uirouter/react](http://twitter.com/uirouter/react)';// Create router instance + setup
export const router = new UIRouterReact();
router.plugin(servicesPlugin);
router.plugin(hashLocationPlugin);
```

导出路由器对象对于使其在其他项目文件中可用非常重要。在此对象上可以访问路由器的大多数服务。

# 应用程序结构

React 本质上是模块化的，这也是将应用程序组织成路线的好方法。较小的独立模块有助于对相关文件进行分组和组织。模块通常包括一个组件文件以及其他资产文件(CSS、测试等)。).路由器的目的是将它们连接在一起。

模块使代码库更容易导航。这对可能有许多路线的大型应用程序来说是一个真正的好处。确保东西容易(且明显)找到。

UI 路由器是基于状态的，导航由状态机处理。这与 React-Router 不同，React-Router 是由匹配的浏览器 URL 驱动的。UI-Router 确实允许你在不声明 URL 的情况下构建应用。但是，这可能会造成混乱，并阻止您实现 url 深度链接。

app 里的每一条路线都可以用一个状态来形容。UI-路由器嵌入模块化应用程序结构。我在每个模块目录中包含了一个状态定义文件，以便于在项目中找到它。

# 状态定义和注册

状态定义文件概述了状态的关键属性和行为。这里声明了状态名、url 和组件(要加载)。一个简单的状态定义文件如下所示。

```
import ContactsPage from ‘./contacts’;export default {
    name: 'contacts',
    url: '/contacts',
    component: ContactsPage,
}
```

要让应用程序找到州，必须首先使用州注册服务进行注册。路由器设置文件是放置它的理想位置。

```
// router.jsx
import { UIRouterReact, servicesPlugin, hashLocationPlugin } from '[@uirouter/react](http://twitter.com/uirouter/react)';// Import states
import contact from './modules/contact/state';
import contacts from './modules/contacts/state';// Create router instance + setup
export const router = new UIRouterReact();
router.plugin(servicesPlugin);
router.plugin(hashLocationPlugin);// Register each state
const states = [
    contact,
    contacts
];
states.forEach(state => router.stateRegistry.register(state));
```

我喜欢这种方法，因为它使代码库易于导航。更高层次的设置集中在项目中。状态(特定)逻辑仍然是单个模块的一部分。

# 状态参数

状态参数可以被声明为 *url* 值的一部分。如果页面被重新加载或共享，这允许参数被保存在 url 中。

状态参数在 url 中用冒号作为前缀或者用花括号括起来。下面的例子使用冒号语法来声明一个 *contactId* 参数。

```
export default {
    name: 'contact',
    url: '/contacts/:contactId'
}
```

查询字符串也可以用来设置多个不太重要的参数。这种方法的优点是参数是可选的。这意味着无需声明每个参数就可以加载状态。这对于处理分页之类的事情很有用，并且有助于保持 url 的可读性。

```
export default {
    name: 'contacts',
    url: '/contacts?{page}&{perPage}'
}
```

状态组件可以从路由器上的全局对象中访问作为键/值对的参数。这些键将映射在 *url* 值中声明的参数。

# 过渡

触发转变；使用的是州名，而不是 URL。改变状态最简单的方法是使用`UISref`组件，这是一个美化了的锚标记。这包装了一个 html 元素(或文本节点),并将其转换成一个 UI-Router 链接。

```
<UISref to={‘contact’} params={{contactId: 1}}> 
    <a>Joe Bloggs</a>
</UISref>
```

这也可以使用状态服务以编程方式处理。 *go* 方法实现了与函数调用相同的功能。它需要两个参数；一个`name`和一个`params`物体。这对于从回调或另一个函数触发转换很有用。

```
router.stateService.go('contact', {contactId: 1});
```

关于当前状态的信息也可以从状态服务中访问。这包括州名等一般信息。当更新没有转换的状态参数时。我喜欢在使用 go 方法时动态设置 name 参数。这使得一个组件在被映射到多个状态时更加灵活。

```
const stateName = router.stateService.$current.name;
router.stateService.go(stateName, {page: 2});
```

# 过渡挂钩

UI-Router 使得挂钩到一个转换的不同点变得非常容易。过渡服务有许多方法可以实现这一点。我常用的三个如下所示。

```
router.transitionService.onBefore(true, function(trans) {
    // Start transition
});router.transitionService.onSuccess(true, function(trans) {
   // End transition
});router.transitionService.onError(true, function(err) {
    // Transition errored
});
```

这些方法相当强大。它们允许在转换期间的特定点运行回调。对转换的引用也被传递给回调，这意味着如果需要的话，可以截取它。这对于处理身份验证和锁定状态非常有用。

我喜欢在转换开始和结束之间显示一个加载指示器。这给用户一些页面正在加载的视觉反馈。由于它们会在每次转换时触发，因此`before`和`success`挂钩非常适合这种情况。一定要整理好`error`钩子里的动作。如果转换失败，错误应该被捕获、处理并指示给用户。

# 决心

解析是在转换到某个状态之前获取数据的一种很好的方式。在安装状态组件之前，可以加载关键数据并使其可用。下面的示例状态解析了一些静态 JSON 数据。

```
import ContactsPage from ‘./contacts’;export default {
    name: 'contacts',
    url: '/contacts',
    component: ContactsPage,
    resolve: [
        {
            token: 'contacts,
            resolveFn: () => {
                return [
                    {   
                        name: 'Joe Bloggs',
                        age: 21,
                        country: 'Scotland'
                    },
                    {   
                        name: 'Jane Doe',
                        age: 31,
                        country: 'England'
                    },
                    {
                        name: 'John Smith',
                        age: 41,
                        country: 'Wales'
                    }
                ];
            }
        }    
    ]
}
```

`resolves`属性是一个由单个 resolve 对象组成的数组。`token`属性是解析的唯一键。`resolveFn`属性必须是返回数据的函数。

上面的例子使用了静态数据，并将立即可用。`resolveFn`功能也支持返还承诺。这是一个强大的工具，用于处理对 API 的延迟 http 数据请求。UI-Router 将在完成转换之前等待，直到承诺完成。

如果承诺失败，转换也将失败，错误挂钩将被触发。一定要设置一个`error` 钩子(见上图)给用户反馈为什么过渡失败。

当心过度使用解析。它会减慢完成过渡的时间。在挂载状态组件之前，选择需要的关键数据。延迟加载较少的数据。

下面是一个使用基于承诺的数据服务和 url 中声明的参数的示例。

```
import ContactPage from './contact';
import contactsService from './contacts-service'export default {
    name: 'contact',
    url: '/contacts/:contactId',
    component: ContactPage,
    resolve: [
        {
            token: 'contact',
            deps: ['$transition$'],
            resolveFn: (trans) => {
                // Get contactId param
                const contactId = trans.params().contactId;
                // Fetch data
                return new contactsService.get(contactId);
            }
        }
    ]
};
```

转换必须作为依赖项(`deps`)包含在内，以访问`resolveFn` *中的任何状态参数。*这可作为函数*的参数。*可通过 props 在状态组件中访问解析的数据(见下文)。

```
import React, { Component } from 'react';export class ContactPage extends Component {render() {
        return (
            <h1>
                {this.props.contact.name}
            </h1>
        );
    }}export default ContactPage;
```

我喜欢这种方法，因为它将数据加载逻辑移出了组件。通过将状态组件从数据服务中分离出来，这使得状态组件更易于测试。允许数据很容易地通过组件道具被模仿。

(安装组件后，注意状态参数或解析数据的变化。有时需要使用`componentWillReceiveProps`方法来处理变化的状态参数。这通常发生在参数设置为 on 状态时)。

# 概括起来

UI-Router 有一个专用的 React 包，因此入门相当容易。除此之外，我发现它是用 React 构建 web 应用程序的理想路由器。

*   它非常适合模块化的应用程序结构。
*   较高级别的路由配置与各个状态分开处理。
*   可以声明路由参数，允许动态页面和 url 深度链接。
*   可以使用转换挂钩在转换的每个阶段触发自定义回调。
*   解析允许在装载状态组件之前加载关键数据。

检查我在 Github 上的工作演示。如果您对 React Router 或一般的路由感到沮丧，请尝试一下 UI-Router。这是一个强大的工具包，新手也很容易上手。

(如果你正在寻找一个关于 React-Router 和 UI-Router 之间差异的好的概述，Marco Botto 的这篇文章非常值得一读——[http://Marco Botto . com/overview-of-UI-Router-React-and-comparison-with-React-Router/](http://marcobotto.com/overview-of-ui-router-react-and-comparison-with-react-router/)。)

[![](img/ff5028ba5a0041d2d76d2a155f00f05e.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/react) [## 学习 React -最佳 React 教程(2019) | gitconnected

### 前 48 名 React 教程。课程由开发人员提交并投票，使您能够找到最佳反应…

gitconnected.com](https://gitconnected.com/learn/react)