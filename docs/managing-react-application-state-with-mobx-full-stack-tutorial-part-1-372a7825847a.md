# 使用 Mobx 管理 React 应用程序状态—全栈教程(第 1 部分)

> 原文：<https://levelup.gitconnected.com/managing-react-application-state-with-mobx-full-stack-tutorial-part-1-372a7825847a>

![](img/8119f71bae771753e387607843ab4319.png)

SPA 的 fullstack 教程，前端是 React JS + MobX，后端是 Django-rest-framework，受基于令牌的认证保护。

# 介绍

React 是前端开发人员在各种应用程序中使用的最流行的 UI 库之一。React 的优势在本文以及网上的各种博客中都有介绍。在我以前的文章中，我试图不关注 React 本身，而是关注一些用于状态管理、数据流实现、路由、动作分派等的强大工具。我从非常简单并且不太出名的[回流](https://medium.com/front-end-hacking/react-and-reflux-usage-in-real-time-applications-based-on-websockets-part-1-introduction-12fcc7cc3590)库开始，通过与 Redux-Thunk 和 React-Router-Redux 的结合，我开发出了著名且强大的 [Redux](/enhancing-your-react-graphql-app-with-redux-and-redux-thunk-90c556aff1c5) 。

在本文中，我将重点关注众所周知的 [MobX](https://mobx.js.org/) 库，它是为可伸缩的状态管理而设计的。在第一部分中，我们将开始在客户端使用 React + Mobx 编写单页面应用程序(SPA ),并为后端使用受 Python 开发人员欢迎的框架 [Django-rest-framework](http://www.django-rest-framework.org/) (使用 Django 版本 [1.11](https://docs.djangoproject.com/en/1.11/) )。该应用程序将负责`Users`和`Todos`列表，与之前的[文章](/enhancing-your-react-graphql-app-with-redux-and-redux-thunk-90c556aff1c5)中相同，并将通过某种身份验证来保护。在本文中，我们将重点关注以下几个部分:

*   新用户的注册
*   使用令牌的身份验证
*   显示用户列表
*   在列表中执行搜索

在接下来的部分中，我们将继续 todos 的实现。您可以在[库](https://github.com/KilroggD/Todos-python)中找到工作代码示例。

重要警告:本文中使用的注册和认证并不能保证 100%安全和生产就绪，尽管它使用非常安全的工作流在数据库中存储会话。此外，本教程没有经过生产测试，因为它的目的是展示 django + MobX + React JS 的全栈教程，并举例说明主要的 MobX 特性。

# 服务器端应用程序

![](img/2a300cbd13979f88b7f565028d924c4b.png)

要让服务器端在您的计算机上工作，只需运行

```
pip install -r requirements.txt
```

要安装所有 python 依赖项，请运行以下命令

```
python manage.py runserver
```

(除了像`django`和`django-rest`这样的必选项目之外，你可以随意跳过其中的一些)

服务器端 python 应用程序的源代码可以在[这里](https://github.com/KilroggD/Todos-python/tree/master/todos)找到。它没有什么特别之处——它只是一个带有 sqlite 示例数据库的`django-rest`后端应用程序。

对于认证，我们将使用 [django-rest-auth](http://django-rest-auth.readthedocs.io/en/latest/) 。相对于使用 JWT 进行用户会话，它更安全，因为它将会话令牌存储在服务器端 ID 数据库中，并使您能够验证服务器从客户端接收的令牌。要启用这个库进行用户验证，我们需要将它包含在已安装的应用程序列表中，并定义 URL。我们将在主 [urls.py](https://github.com/KilroggD/Todos-python/blob/master/tutorial/urls.py) 中为 API 调用和身份验证定义不同的 URL 路径

```
url(r'^api/', include('todos.urls')),
url(r'^rest-auth/', include('rest_auth.urls')),
```

我们将使用来自`rest_auth`库的默认登录视图，并为用户注册编写简单的视图

```
class Registration(generics.GenericAPIView):
    serializer_class = UserRegistrationSerializer
    permission_classes = (AllowAny,)def post(self, request, *args, **kwargs):
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        user = self.perform_create(serializer, request.data)return Response({}, status=status.HTTP_201_CREATED)def perform_create(self, serializer, data):
        user = serializer.create(data)
        return user
```

在本文中，我不会关注后端部分，因为它是一个非常基本的 django 应用程序，带有一个简单的 sqlite 数据库。我们将添加一些功能来扩展默认用户模型管理器(例如，使用搜索表单帖子参数过滤用户的搜索方法)

```
def search(self, **kwargs):
        qs = self.get_queryset()
        if kwargs.get('first_name', ''):
            qs =  qs.filter(
                first_name__icontains=kwargs['first_name'])
        if kwargs.get('last_name', ''):
            qs = qs.filter(
                last_name__icontains=kwargs['last_name'])
        if kwargs.get('department', ''):
            qs = qs.filter(department__name=kwargs['department'])
        if kwargs.get('country', ''):
            qs = qs.filter(country__name=kwargs['country'])
```

您可以自由探索存储库中的其他特性。

# 客户端应用程序

![](img/936d577cec3bfde34e08414db2cbefa9.png)

本教程的前端部分是基于[上一个](/enhancing-your-react-graphql-app-with-redux-and-redux-thunk-90c556aff1c5)的 React 和 Redux。源代码可以在存储库中的[客户端文件夹](https://github.com/KilroggD/Todos-python/tree/master/client)中找到。它使用标准的`create-react-app`样板文件和一些附加的包:

```
"mobx": "^3.5.1",
"mobx-react": "^4.3.5",
```

对于 React 应用中的 Mobx 支持，我们添加了以下插件:

```
"babel-plugin-transform-class-properties": "^6.24.1",
"babel-plugin-transform-decorators-legacy": "^1.3.4"
```

这些为 MobX 中使用的 decorator 语法增加了类属性支持。

如果你仍然面临编译前端的任何问题，我建议使用[自定义反应脚本](https://www.npmjs.com/package/custom-react-scripts)而不是反应脚本。你可以从项目的开始(create-react-app my-app-scripts-version custom-react-scripts)开始，或者如果你已经设置了 create-react-app 样板文件，你可以按照[这条线索](https://github.com/kitze/custom-react-scripts/issues/30)删除 react-scripts 并添加自定义脚本:

```
npm remove react-scripts
npm i -s custom-react-scripts
```

然后创造[。您的 app 文件夹中的 env](https://github.com/KilroggD/Todos-python/blob/master/client/.env) 文件，包含以下内容:

```
REACT_APP_BABEL_STAGE_0=true
REACT_APP_DECORATORS=true
```

或者如果你只是下载教程，你会没事的，因为我更新了 package.json 来使用自定义脚本:)

要运行前端部分，首先使用以下命令安装软件包:

```
npm install
```

然后使用以下命令启动应用程序:

```
npm start
```

哪个在您的本地机器上运行开发服务器

这个应用程序拥有超级用户**admin@tutorial.com**，密码为 **q1w2e3r4。**随意创建另一个具有注册功能的用户，命令行，或者直接在 sqlite DB 文件中。

我们将使用良好的旧 REST API 来服务 JSON 对象。我们需要前端的 API 路由，类似于 [API 路由](https://github.com/KilroggD/Todos-python/blob/master/client/src/api.js)。因为这些 URL 是用于 API 的，所以我们将路由放在一个单独的[文件](https://github.com/KilroggD/Todos-python/blob/master/client/src/routes.js)中。

保持[服务](https://github.com/KilroggD/Todos-python/tree/master/client/src/services)类与 API 端点调用、本地存储 API 等交互是很方便的。请注意 [ApiService](https://github.com/KilroggD/Todos-python/blob/master/client/src/services/ApiService.js) 已经被更改为使用 REST 端点。我们还需要一个新的[历史](https://github.com/KilroggD/Todos-python/blob/master/client/src/services/history.js)存储，以便能够从 MobX 存储中以功能方式执行重定向，因为没有像 react-router-redux 这样的包来实现开箱即用的功能(如果有，请告诉我:)

```
import createHistory from 'history/createBrowserHistory'export default createHistory()
```

关于文件夹结构，我们将保持与 Redux 教程中相同的结构。我们将有服务文件夹来保存上述服务

我们将把组件分成[容器](https://github.com/KilroggD/Todos-python/tree/master/client/src/containers)——保持“智能”组件连接到数据存储并能够触发动作，[表单](https://github.com/KilroggD/Todos-python/tree/master/client/src/forms) —保持组件与用户输入数据相关，最后，[组件](https://github.com/KilroggD/Todos-python/tree/master/client/src/components) —保持无状态的表示组件，这些组件可以从 redux 教程中复制过来，或者在将来的任何示例中重用。

然后让我们把重点放在本教程中使用的 MobX 特性上。

# MobX 功能

![](img/a8745a7364b525e72acd372bc86a32ee.png)

MobX 的一个主要概念是商店。与 Redux 概念相比，store 可以提供 Redux 中 reducers 和 actions 所提供的相同功能。商店的一个简单例子是 [UserStore](https://github.com/KilroggD/Todos-python/blob/master/client/src/stores/UserStore.js) ，它负责根据搜索参数加载用户列表。

从顶部开始—用途之一是可观察的(或“@observable”)

```
@observable isLoading = true
@observable isFailure = false
@observable users = []
```

这个装饰器定义了 react 组件应该观察的值(可以是 JS 原语、引用、普通对象、类实例、数组和映射)。当这个值改变时，组件将识别它并相应地更新(类似于 redux 中的 mapStateToProps)。

另一个有用的特性是计算属性，该属性的值基于另一个值，可通过 getter 方法访问。

```
@computed get total() {
     return this.users.length
 }
```

Python 开发人员可能对这个特性很熟悉，因为在 Python 中广泛使用了“@property”修饰符。

因为 MobX 不像 Redux 那样为动作指定任何显式位置，所以您也可以使用“@ action”decorator 在商店中定义动作。你也可以在商店之外以更“类似 redux”的方式定义动作——这在一篇关于从 Redux 迁移到 MobX 的非常有用的文章[中有完美的描述。您还可以使用 Javascript ES7 中引入的 async/await 语法来定义异步操作](https://www.robinwieruch.de/mobx-react/)

```
[@action](http://twitter.com/action) async getUsers(params) {
        try {
            const data = await ApiService.search_users(params)          
            runInAction(() => {
                this.isLoading = false
                this.users = data.users
                if (Object.keys(params).length && data) {
                    //save successful request
                    StorageService.setSearchData(params)
                }
            })
        } catch (e) {
            runInAction(() => {
                this.isLoading = false
                this.isFailure = true
                this.users = []
            })
        }
    }
```

在这个动作中有一点语法上的好处，它获取一段代码并执行它，这是一个匿名动作。如上面的代码示例所示，这对于异步操作非常方便。

当然，您可以使用简单的同步操作来修改您的存储状态，并执行一些副作用(例如 [localStorage update](https://github.com/KilroggD/Todos-python/blob/master/client/src/stores/SearchStore.js)

```
[@action](http://twitter.com/action) loadStoredData() {
        //load form data from local storage
        const data = StorageService.getSearchData() || {}
        //dispatch an action        
        this.loadForm(data)
    }[@action](http://twitter.com/action) clearForm() {
        StorageService.removeSearchData()
        this.searchData = {}
    }[@action](http://twitter.com/action) clearForm() {
        StorageService.removeSearchData()
        this.searchData = {}
    }
```

如果我们需要使用声明的方式从一个动作执行重定向，我们可以使用本教程前面描述的历史服务:

```
[@action](http://twitter.com/action) async logout() {
        await ApiService.logout(StorageService.getToken())
        StorageService.removeToken()
        runInAction(() => {
            this.isAuthenticated = false
            this.isFailure = false
            this.currentUser = null
            this.isLoading = false
            history.push('/')
        })
    }
```

为了给商店注入令人惊叹的功能，我们有几种选择:

*   通过商店作为道具
*   通过 *this.store = new MyStore()* 在构造函数中实例化 store
*   使用[注入](https://mobx.js.org/refguide/api.html)功能包装器
*   使用注入装饰语法

让我们选择最后一个的[，因为在我看来，它看起来更‘复古’和干净。](https://github.com/KilroggD/Todos-python/blob/master/client/src/index.js)

首先，我们能够像下面这样组合我们的商店:

```
const stores = {
    authStore,
    userStore,
    searchStore,
    registerStore,
};
```

然后，我们使用 Redux-like Provider 模式将组合商店传递给应用程序

```
ReactDOM.render(
    <Provider { ...stores }>
        <App>
            <Router history={history}>
                <Switch>
                    <Route exact path="/" component={Home} />
                    <Route path={routes.login} component={LoginContainer} />
                    <Route path={routes.sign_up} component={RegisterContainer} />
                    <Route path={routes.users} component={UserListContainer} />
                </Switch>
            </Router>
        </App>
    </Provider>,
    document.getElementById('root')
);
```

不要忘记定义我们需要的所有导入:

```
import { Provider } from 'mobx-react'
import { Router, Switch, Route } from 'react-router'
import history from './services/history'
import authStore from './stores/AuthStore'
import userStore from './stores/UserStore'
import searchStore from './stores/SearchStore'
import registerStore from './stores/RegisterStore'
```

然后，我们可以使用“@inject”和“@observer”装饰器轻松地将我们需要的存储插入到每个组件中(例如，参见 [UserListContainer](https://github.com/KilroggD/Todos-python/blob/master/client/src/containers/UserListContainer.js)

```
[@inject](http://twitter.com/inject)('userStore', 'searchStore') [@observer](http://twitter.com/observer)
class UserListContainer extends React.Component {
```

通过容器中的 props 可以访问注入的存储，因此我们可以使用存储中的属性和动作作为我们的“共享状态”

```
return <div className="user">
            <UserForm data={searchStore.searchData}
                submitHandler={this.getUsers}
                changeHandler={searchStore.loadForm.bind(searchStore)}
                clearHandler={searchStore.clearForm.bind(searchStore)} />
            {userStore.users && <UserList users={userStore.users} />}
        </div>;
```

或者在异步操作的情况下，我们甚至可以在生命周期方法中使用它们

```
async componentDidMount() {
        this.props.searchStore.loadStoredData()
        await this.props.userStore.getUsers()
    }
```

在 [LoginContainer](https://github.com/KilroggD/Todos-python/blob/master/client/src/containers/LoginContainer.js) 或 [RegisterContainer](https://github.com/KilroggD/Todos-python/blob/master/client/src/containers/RegisterContainer.js) 的情况下，我们可以只注入我们需要检查认证用户的存储，并在必要时进行重定向:

```
async componentWillReact() {
        if(this.props.authStore.isAuthenticated) {
            await this.props.authStore.fetchProfile()
            history.push('/')
        }
    }
```

这是一个新的生命周期挂钩，当组件由于观察到的数据发生变化而需要重新呈现时，就会触发这个挂钩。

# 运行生产版本

您可以使用 webpack 和 python dev-servers 全面测试这些代码，但是如果您想使用“类似生产”的构建来运行它，您可能需要这些有用的提示。

首先，您需要通过转到客户端文件夹并运行以下命令来构建 fronend:

```
npm run build
```

然后，您需要在您的 [url.py](https://github.com/KilroggD/Todos-python/blob/master/tutorial/urls.py) 文件中定义适当的 url，以便从 django 提供它:

```
url(
    r'^$',         
    csrf_exempt(TemplateView.as_view(template_name='index.html'))
),
url(
    r'^(?P<path>.*)/$',     
    csrf_exempt(TemplateView.as_view(template_name='index.html'))
),
```

默认情况下，这两种模式强制所有路径服务于 index.html 模板。

```
url(r'^api/', include('todos.urls')),
url(r'^rest-auth/', include('rest_auth.urls')),
```

另外两个定义了到我们的 todos API 和 rest-auth API 的链接

最后一个需求将服务于 fronend 构建中的 service-worker.js:

```
url(
        r'^service-worker.js', cache_control(max_age=2592000)(
            TemplateView.as_view(
                template_name="service-worker.js",
                content_type='application/javascript',
            )
        ),
        name='service-worker.js'
    ),
```

我们还需要在您的[设置](https://github.com/KilroggD/Todos-python/blob/master/tutorial/settings.py)中定义静态文件中模板的路径

```
TEMPLATES = [
    { 
# ......
        'DIRS': ['client/build/'],
        'APP_DIRS': True,# ......
    },
]
# .....
# Static files (CSS, JavaScript, Images)
# [https://docs.djangoproject.com/en/1.11/howto/static-files/](https://docs.djangoproject.com/en/1.11/howto/static-files/)
CORS_ORIGIN_ALLOW_ALL = True
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    'client/build/static',
]
```

所有这些准备工作完成后，我们就可以在运行后看到我们的主页了

```
python manage.py runserver
```

并导航到`localhost:8000`(或不同的主机/端口，如果已定义)

# 我对 MobX 的看法

![](img/a1efe7d66c0937a66b898cd52abdd278.png)

首先— MobX 和 React—MobX 不能认为是 React—Redux 的替代品。这是一件有点不同的事情。MobX 很好，是一个相对较小的库，用于执行可伸缩的状态管理。与 Redux 相比，它只提供了一个明确定义的模式——商店，但是您可以非常灵活地使用它。

关于动作、简化器和中间件没有严格的符号，但这并不意味着你不能自己定义它。

与 Redux (redux-thunk、redux-saga、react-router-redux 等)相比，MobX 没有太多的附加组件和外部库，其社区也没有那么大。然而，它在不断成长和改进。

在使用 MobX 和 Redux 一段时间后，我可以说 MobX 更加“轻量级”,更容易学习，但在为您提供适当的状态管理工具方面非常强大。虽然它主要关注商店层，但它的工作做得很好。Redux 在我看来还是比较强大的库。它为你提供了一个完整的关于存储和动作的架构模式，并且，如果你使用额外的东西，比如 thunk 和 react-router，它还为你提供了中间件，并把它自己注入到路由工作流中。然而，在我看来，这有点难学，而且你并不总是需要它——对于相对较小的项目来说，它可能过于复杂了。如果你从一个小应用开始，并期望它成长——不要担心，Redux 和 MobX 都是完全可扩展的。

# 未完待续…

在本教程的以下部分中，我们将介绍:

*   添加待办事项存储和查看待办事项列表的功能
*   添加向用户分配任务、更改任务状态等功能
*   使用 React 和 Mobx 添加单元测试示例