# Ionic React:使用 Redux 在视图之间共享数据

> 原文：<https://levelup.gitconnected.com/ionic-react-sharing-data-between-views-using-redux-1aca169a4cf2>

![](img/5d8602998139930cc6cce44402c87726.png)

由[卡耶塔诺·吉尔](https://unsplash.com/@cytngl?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

React 类似于狂野的西部。

您可以自由选择多个用户界面选项、路由方法选项，甚至状态存储选项。

Ionic 为其**用户界面**和**路由器**提供了一套现成的设置。这提供了两件事情，对于试图开始构建应用程序的开发者来说，通过提供健壮和严格的路由以及一整套可供选择的用户界面组件，在某种程度上减少了担心。两部分，完成了，不用担心。

但是如何在视图之间共享数据呢？我们如何确保数据从一个视图持续到另一个视图？在视图 A 和视图 b 之间共享数据肯定有更简单的方法。

有几种简单的方法来实现这一点。在本指南中，我将重点介绍一种易于实现的持久化数据的方法，即使用 React-Redux。

**什么是 React Redux？**

> Redux 是一个用于管理和集中应用程序状态的开源 JavaScript 库。

——来自[维基百科](https://en.wikipedia.org/wiki/Redux_(JavaScript_library))

**这是另一个实施 React Redux 的指南吗？**

这一个有一个关键的区别…

本指南基于在 Ionic React 上实现 React Redux，以便创建持久数据状态。没有使用典型的 Create React 应用程序样板。

这是基于在 [React-Redux](https://react-redux.js.org/tutorials/quick-start) 网站上提供的快速入门指南，重点是使用 Ionic React 内置组件。

我们将建立一个 Ionic 应用程序，具有持久的名称状态，将在 2 个不同的视图中使用。

本文中使用的版本

> Ionic CLI 版本 6.17.1

## **步骤 0。设置离子项目**

要启动一个 Ionic 项目，从`ionic start`开始。欲了解更多信息，请访问:

[](https://www.ionicframework.com) [## 跨平台移动应用开发:Ionic 框架

### Ionic Framework 的应用程序开发平台构建了令人惊叹的跨平台移动、web 和桌面应用程序，只需一个…

www.ionicframework.com](https://www.ionicframework.com) 

不要忘记在项目类型选项中选择 Ionic React 项目。然后你可以选择任何项目类型，我推荐选择*空白*。

## **第一步:安装 React-Redux 库和工具包**

在命令行中运行以下命令，安装 reduxjs 工具包和 React Redux 库:

```
npm install @reduxjs/toolkit react-redux
```

## **步骤 2:设置功能并存储**

创建两个文件，并将它们放在相应的文件夹中:

> 1.app/store.tsx
> 
> 2.features/name.tsx

对于 name.tsx 和 store.tsx，文件内容如下:

对于 name.tsx:

第 4 行:这是我们给变量命名的地方，在这里，我们把它命名为‘name’

**第 9 行**:是“reducers”或者可以用来修改“name”状态的动作类型。在这种情况下，我们希望它能够使用“changeValue”来更改值

对于 store.tsx:

**第 6 行**:我们添加了 state 这个名称，因为这是我们希望在所有视图中保持的状态。

## **第三步:在视图或组件中实现**

我们现在可以在相应的视图中实现上面的“名称”状态。我们可以创建一个新的组件，或者在我的例子中，我将使用 Home.tsx 视图并创建一个名为 Next.tsx 的新视图来显示持久数据。

默认情况下，Home.tsx 已经可用。在 */pages/Next.tsx* 处创建一个新文件

在 Home.tsx 中:

**第 6 行和第 7 行:**我们正在导入 *useState* 用于钩子。与此同时，在第 7 行中，我们正在导入*used dispatch*和 *useSelector*

**第 12 行和第 14 行:**这就是神奇发生的地方！这在我们的应用程序中用来获取状态( *useSelector* )并对状态进行更改( *useDispatch* )。如果您注意到了，我们还通过设置状态*名称*初始化了将在第 15 行中使用的来自当前“名称”状态的值

**第 25 行到第 29 行**:这是 IonItem，我们在其中分派要存储在状态中的值，这个值稍后可以在 Next.tsx 中调用。这里的想法是当用户在 IonInput 中输入一个输入，然后按下 Change Name 按钮，这会将用户的输入分派并存储到状态中。相当简单，对吗？

同时，在 Next.tsx 中

**第 6 行**:使用*使用选择器，*我们将*名称*状态从要初始化的变量名称中取出。

**第 20 行**:这里我们使用了前面提到的变量 *name* 来显示状态中存储的内容。这是基于用户在 *Home.tsx* 中输入的持久化数据

## **第四步:将 Next 添加到 App.tsx 中**

**第 5 行:**导入 Next.tsx

**第 38–40 行**:在 IonReactRouter 下添加 Next.tsx

这将使我们能够使用 */next* 导航到 **Next.tsx** 页面

就是这样！完成了。您现在可以更改名称值，并在整个 Ionic React 应用程序中持续使用。

*   更正:

感谢乔纳森·阿斯伯里指出这一点。我忘记在应用程序中添加提供者和商店来实现这个功能。我们需要修改 App.tsx 以包含 Provider 和 store，如下所示:

```
import { Provider } from 'react-redux'; //Add to line 1
import store from './app/store'; //Add to line 2...<Provider store={store}> //Add the above provider in between existing line 29-30 after //<IonApp>
```

## 结论

使用持久数据很重要，因为它会让你更容易构建一个应用程序。React Redux 是 NPM 最流行的用于此目的的库之一。

我在以前的文章中做过的一个类似的方法是在 Ionic Angular 项目中使用 NGXS。你可以在这里阅读:

[](https://javascript.plainenglish.io/the-easy-guide-using-state-management-with-ngxs-in-an-ionic-app-4d4e61c4e78) [## 简单指南:在 Ionic 应用程序中使用 NGXS 的状态管理

### 把一个变量或一个对象的一部分保存在某个地方不是很容易吗，它可以在任何时候被调用…

javascript.plainenglish.io](https://javascript.plainenglish.io/the-easy-guide-using-state-management-with-ngxs-in-an-ionic-app-4d4e61c4e78) 

一定要看一看。

这次到此为止。祝你愉快

*塞拉马特·孟加图拉*