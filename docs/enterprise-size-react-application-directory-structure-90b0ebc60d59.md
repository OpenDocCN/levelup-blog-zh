# 组织 React 应用程序

> 原文：<https://levelup.gitconnected.com/enterprise-size-react-application-directory-structure-90b0ebc60d59>

当你开始编写一个新的应用程序时，如何组织它总是一个大问题。我将向您描述理想的文件结构和最佳实践，它几乎可以在任何场景中使用，并且易于使用。

![](img/a4168f6e7a65a5506660834672446e1e.png)

[https://i1 . WP . com/www . interesting only . com/WP-content/uploads/0-986 . jpg](https://i1.wp.com/www.interestingonly.com/wp-content/uploads/0-986.jpg)

# 项目结构的优势是什么？

1.  团队成员不必争论在哪里放置特定的功能
2.  可以描述，这样你就不用每次都给新人解释了
3.  随着更多的前端项目具有相同的结构，很容易交换团队成员
4.  您可以专注于功能，而不是考虑结构

# 一个好的结构应该实现什么？

1.  容易
2.  灵活性
3.  复用性
4.  提高生产率

# 组件使用的最佳实践

## 组件文件命名

*   组件目录名 **PascalCase**
*   组件文件名 **PascalCase**
*   不要重复父母的名字
*   **坏:***src/Banners edit/Banners editform . tsx*
*   **好:***src/Banners/Edit/form . tsx*

## 组件命名

组件的名称应该复制组件的路径，例如

1.  *src/pages/log in/index . tsx*是 **< Login / >**
2.  *src/pages/log in/form . tsx*是 **< LoginForm / >**

这将保证所有组件都有唯一的名称。

## 容器组件命名

如果您想通过 *React.memo()* 或 *compose()，*将 ***组件*** 后缀添加到您的类定义中，并导出一个名称不带后缀的 *const* 。

## 避免创建依赖于属性状态的组件——初始值除外

你需要遵循真理的单一来源原则，这意味着将数据从道具复制到状态不是解决方案，因为道具可以改变。你可以根据属性初始化状态，但是只能在构造函数中。在任何其他生命周期方法中这样做是不可接受的，因为您将使用*。setState()* 触发重新渲染。

## 反应组件— setState()

> setState()将始终导致重新呈现，除非 shouldComponentUpdate()返回 false

这意味着你应该使用 *setState()* 来改变组件状态，而不要直接改变状态。我们还需要知道，当我们使用复合类型时，我们是在共享引用。

示例:

这里我们直接在 state 中修改`propertyOne`的属性。

没有新库的解决方案:

*延伸阅读:*

*   [*https://medium . freecodecamp . org/handling-state-in-react-four-immutable-approachs-to-consider-d1f5c 00249 D5*](https://medium.freecodecamp.org/handling-state-in-react-four-immutable-approaches-to-consider-d1f5c00249d5)
*   [*https://it next . io/updating-properties-of-an-object-in-react-state-af 6260 D8 e9 f 5*](https://itnext.io/updating-properties-of-an-object-in-react-state-af6260d8e9f5)

## 避免在 render()中创建新的引用—使用处理函数

示例—创建一个新对象和一个匿名函数，并将它们作为道具传递:

这将导致每次当`ParentComponent` 重新渲染时，`ChildComponent` 也重新渲染。

**为什么？这是因为在`ParentComponent`的每一个渲染周期中，我们都有一个新的对象和函数创建，而你将这些新创建的实体传递下去，所以道具引用总是变化的。**

## 如何编写事件处理函数以避免新的引用

在我们喜欢只使用一个原始值的情况下:

在我们喜欢使用非原始值(对象、数组)的情况下:

## 使用 React。PureComponent 和 React.memo

**为什么？**这是因为`React.PureComponent`和`React.memo`默认实现浅道具和状态比较。`React.memo`是功能组件`React.PureComponent`的版本。**浅比较**是指道具和状态(变化前和变化后)只在 0 级上比较。对于基本类型，比较它们的值，对于非基本类型，如对象或数组，只比较它们的引用。

用法:

*文档链接:*

*   *做出反应。pure component*[*https://reactjs.org/docs/react-api.html#reactpurecomponent*](https://reactjs.org/docs/react-api.html#reactpurecomponent)
*   *react . memo*https://reactjs.org/docs/react-api.html#reactmemo

## 避免默认导出

违约出口很快变得一团糟。在大量实体被重新导出的大型项目中，没有办法维护默认导出。

## 使用索引文件重新导出组件

在组件目录中，使用索引文件来重新导出组件。这是有帮助的，因为当你想导入它时，你不必写

```
import { MyComponent } from './components/MyComponent/MyComponent';
```

但是写出来就够了

```
import { MyComponent } from './components/MyComponent';
```

# 期待已久的结构

![](img/ea5d46ab60fb3642bd021a2d89624876.png)

[https://thumb or . Forbes . com/thumb or/960 x0/https % 3A % 2F % 2Fblogs-images . Forbes . com % 2 frajatbhageria % 2f files % 2f 2017% 2f 09% 2f code-copy-1200 x 1200 . jpg](https://thumbor.forbes.com/thumbor/960x0/https%3A%2F%2Fblogs-images.forbes.com%2Frajatbhageria%2Ffiles%2F2017%2F09%2Fcode-copy-1200x1200.jpg)

我将用例子来描述每个目录。你可以在文章末尾找到整个结构。

> 在示例中，粗体文本代表我们的特殊目录或文件。其他示例文件或目录用方括号括起来。

# 1.核心

```
**src**
|**__core**
   |__[contextConfig].ts
   |__[httpClient].ts
   |__[...]
```

包括第三方库的配置文件。它可以是你的 http 客户端的配置，可能是 Axios，或者是用于设置 Jest 这样的测试框架。

# 2.共享组件

```
**src**
|**__sharedComponents**
   |__[header]
   |  |__[Menu.tsx]
   |  |__[UserPanel.tsx]
   |  |__[Header.tsx]
   |  |**__index.ts** -> re-exports needed components
   |  |__[...]
   |
   |__[form
   |  |__[Button.tsx]
   |  |__[Form.tsx]
   |  |__[Input.tsx]
   |  |**__index.ts** -> re-exports needed components
   |  |__[...]
   |
   |__[Sidebar.tsx]
   |__[...]
```

顾名思义，这里是你共享组件的地方，在你的页面之间共享。请记住，这里只允许使用**表示性的**组件。那些没有副作用或者内部状态巨大的。当我说巨大的内部状态时，我指的是来自后端等的数据。如果你保存选择框的状态，无论它是打开还是关闭，都没问题。这就是我不谈论无状态组件的原因。

# 3.主索引. tsx

```
**src**
|**__index.tsx**
```

按照习惯，在 ***index.tsx*** 中，你应该将你的主要组件呈现给 DOM。

# 3.页

```
**src**
|**__pages**
   |__[login]
   |  |**__store**
   |  |  |**__reducers.ts**
   |  |  |**__actions.ts**
   |  |  |**__selectors.ts**
   |  |  |**__effects.ts**
   |  |  |**__types.ts**
   |  |
   |  |**__queries**
   |  |  |__[withUser.ts]
   |  |  |__[withUserType.ts]
   |  |  |__[...]
   |  |
   |  |**__context**
   |  |  |__[withUser.ts
   |  |  |__[withUserType.ts
   |  |  |__[...]
   |  |
   |  |**__utils**
   |  |  |__[emailValidator.ts
   |  |  |__[...]
   |  |
   |  |**__constants**
   |  |  |__[userTypes.ts
   |  |  |__[...]
   |  |
   |  |**__services**
   |  |  |__[loginService].ts
   |  |  |__[...]
   |  |
   |  |__[Login].tsx
   |  |__[Box].tsx
   |  |**__index.ts**
   |  |__[...]
   |
   |__[products]
   |  |__[list]
   |  |  |__[*helper directories*]
   |  |  |__[*state management directories*]
   |  |  |__[List].tsx
   |  |  |__[Item].tsx
   |  |  |__[BaseInfo].tsx
   |  |  |**__index.ts**
   |  |  |__[...]
   |  |
   |  |__[detail]
   |  |  |__[*helper directories*]
   |  |  |__[*state management directories*]
   |  |  |__[*component directories/files*]
   |  |
   |  |__[...]
   |
   |**__routes.tsx**
   |__[...]
```

*   该目录代表您的 web 应用页面，例如 ***页面/产品*或*页面/产品/列表***
*   每个子目录都有自己的 react 组件，只在页面上使用
*   它们是相互独立的，所以如果您需要编辑用户页面，只需要在 ***【用户】*** 目录树中进行修改
*   使用***shared components***目录可以在两个页面之间实现组件共享
*   ***routes.tsx*** 是你 app 中主路由器的位置，这是你页面的入口点

# 3.1.状态管理目录

> 以下示例主要使用高阶组件。如果你喜欢更多的 React 挂钩，你可以有相同的结构，但带有自定义挂钩。

> 唯一的规则是每个页面都应该有自己独立的状态

## [商店]

```
**store
  |__reducers.ts
  |__actions.ts
  |__selectors.ts
  |__effects.ts
  |__types.ts**
```

我们将它命名为 ***store*** 是因为用于存储管理的库可以因项目而异。为了给你一个例子，我将描述一下 **Redux 的结构。**

**actions . ts**

*   包含纯函数，它接收准备按原样保存到存储区的数据
*   这里不做任何数据修改

**effects . ts**

*   包含 sagas 或 thunk 函数或 observables，取决于你将使用什么库的副作用-如果你不使用任何，有没有这个文件的需要
*   商店(动作)的所有数据准备都在这里完成

***reducers . ts***

*   包含带有减速器的存储结构
*   接收准备好的数据进行保存
*   这里不做任何数据修改

***选择器. ts***

*   包含接收存储状态作为参数并返回所需数据的函数
*   这里可以进行任何组合或数据修改，以获得进一步工作所需的数据结构
*   使用一些像**重新选择**这样的库来记忆是很好的

**types . ts**

*   仅包含常量的定义，它代表操作类型

在这种情况下，如果你不喜欢上面例子中的普通名称，你可以用特定的名称作为前缀，例如***user selectors . ts****，****user reducers . ts***等。

## [查询]

```
**queries**
  |__[withUser].ts
  |__[withUserType].ts
  |__[...]
```

对于使用 graphQL 的人来说，这是查询和变异的目录。

*   ***同【QueryName】。ts*** —查询专用
*   ***带【QueryName】突变. ts*** — HOC 为突变

对于这个例子，我使用的是 **react-apollo。**

## [背景]

```
**context**
  |__[withUser].ts
  |__[withUserType].ts
  |__[...]
```

如果你更好地使用 React Context API，这是你可以放置文件的地方。

*   ***同【HocName】。tsx***

特设示例:

之后，您可以使用以下助手功能。

*   **withProviders() —** 用上下文提供者包装你的组件

*   **withConsumers() —** 将所需消费者的所有价值映射到您的组件属性

# 3.2.助手目录

```
[login]
  |**__utils**
  |  |__[emailValidator].ts
  |  |__[...]
  |
  |**__constants**
  |  |__[userTypes].ts
  |  |__[...]
  |
  |**__services**
     |__[loginService].ts
     |__[...]
```

## 常数

*   你应该在这里输入你正在使用的所有常量和枚举
*   文件应该以描述性的方式命名，例如 ***【登录页面 URL】。ts***
*   不要把所有的常量放在一个文件中，把它们分开

## 实用工具

*   包括分离到文件和目录的函数/类
*   ***【实用程序名|实用程序组名】。ts*** *—* 代表一个功能或一组功能
*   例如 *myValidator.ts*
*   例如*validators/my validator . ts*

## 服务

*   主要是 API 调用、基本 HTTP 请求(使用 axios、fetch 等。)
*   ***服务/【服务名】。ts***

下一个例子显示了如何配置 **axios** 。它还包括来自浏览器 cookie 的访问令牌初始化。

用户服务定义示例:

# 3.3.反应组分

```
[products]
   |__[Login].tsx
   |__[Box].tsx
   |__[ComponentComposedWithSmallComponents]
   |  |__[Component1].tsx
   |  |__[Component2].tsx
   |  |__[Component3].tsx
   |  |**__index.ts**
   |
   |__[...]
```

当特定的 pages 目录由于内部组件过多而失去可读性时，请将这些组件分离到不同的目录中。然后 ***index.ts*** 文件重新导出你的组件。在这种情况下，您需要将您的组件视为构建块。它们只公开了一个可以从外部使用的组件，但是对于外部世界来说，它是一个黑盒。

# 一起

```
src
|__core
|  |__context.ts
|  |__httpClient.ts
|  |__[...]
|
|__sharedComponents
|  |__header
|  |  |__Menu.tsx
|  |  |__UserPanel.tsx
|  |  |__Header.tsx
|  |  |__index.ts
|  |  |__[...]
|  |
|  |__form
|  |  |__Button.tsx
|  |  |__Form.tsx
|  |  |__Input.tsx
|  |  |__index.ts
|  |  |__[...]
|  |
|  |__Sidebar.tsx
|  |__[...]
|
|__pages
|  |__login
|  |  |__store
|  |  |  |__reducers.ts
|  |  |  |__actions.ts
|  |  |  |__selectors.ts
|  |  |  |__effects.ts
|  |  |  |__types.ts
|  |  |
|  |  |__queries
|  |  |  |__withUser.ts
|  |  |  |__withUserType.ts
|  |  |  |__[...]
|  |  |
|  |  |__context
|  |  |  |__withUser.ts
|  |  |  |__withUserType.ts
|  |  |  |__[...]
|  |  |
|  |  |__utils
|  |  |  |__emailValidator.ts
|  |  |  |__[...]
|  |  |
|  |  |__constants
|  |  |  |__userTypes.ts
|  |  |  |__[...]
|  |  |
|  |  |__services
|  |  |  |__loginService.ts
|  |  |  |__[...]
|  |  |
|  |  |__Login.tsx -> <Login />
|  |  |__Box.tsx -> <LoginBox />
|  |  |__index.ts -> re-exports the specific page component
|  |  |__[...]
|  |
|  |__products
|  |  |__list
|  |  |  |__[*helper directories*]
|  |  |  |__[*state management directories*]
|  |  |  |__List.tsx -> <ProductsList />
|  |  |  |__Item.tsx -> <ProductsItem />
|  |  |  |__BaseInfo.tsx -> <ProductsBaseInfo />
|  |  |  |__index.ts -> re-exports the specific page component
|  |  |  |__[...]
|  |  |
|  |  |__detail
|  |     |__[*helper directories*]
|  |     |__[*state management directories*]
|  |     |__[*component directories/files*]
|  |     |__[...]
|  |
|  |__routes.tsx
|  |
|__index.ts
```

非常感谢你的阅读！