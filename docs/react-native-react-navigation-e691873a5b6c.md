# 反应本地:反应导航

> 原文：<https://levelup.gitconnected.com/react-native-react-navigation-e691873a5b6c>

## 我们深入 React 导航，并探索如何创建带有标签、堆栈、抽屉导航器及其组合的应用程序。

![](img/5f56478f20d8e5fb771a3e124047701f.png)

在上一篇文章中，我们看了一下 React Native，并创建了我们的第一个应用程序。今天，我们将使用导航器从单屏幕应用程序过渡到多屏幕应用程序。

# 什么是导航仪？

导航器允许您定义应用程序的结构。通过使用路线和场景(或者在这种情况下是屏幕)，他们设置如何向用户显示您的应用程序。导航器还呈现常见元素，如标题和标签栏。

# 反应导航

在为您的应用程序选择导航器时，有许多选项可供选择。对于本文，我们将重点关注 React 导航。这是一个由 React 本地社区支持的解决方案，可以与 Android 和 iOS 的本地导航组件一起工作。

React 导航与 React 本机框架的“一次学习，随处编写”方法保持一致。它也非常容易使用。

# 内置导航器

React 导航带有三个内置的导航器:StackNavigator、TabNavigator 和 DrawerNavigator。我们将通过一个例子来探究每一个，然后看看如何通过嵌套来组合它们。首先让我们建立一个新的应用程序。

打开一个终端窗口，并导航到您想要这个新应用程序驻留的位置。在那里，调用 React Native CLI 来设置名为“ReactNav”的新应用程序。

```
react-native init ReactNav
```

项目设置完成后，导航到项目文件夹。在那里调用节点包管理器(NPM)来安装最新版本的 React 导航。

```
npm install --save react-navigation
```

很好，现在我们已经初始化了一个新的应用程序并安装了 React 导航。在我们开始之前，让我们做一些修改，以便我们的代码可以用于 iOS 和 Android。

打开应用程序的“index.ios.js”和“index.android.js”文件。将两者中的内容更改为以下内容。

接下来，我们需要在与“index.ios.js”和“index.android.js”文件相同的文件夹中创建文件“App.js”。在里面，我们将写出应用程序的代码。添加以下内容作为占位符。

让我们在同一个文件夹中创建一个名为“Styles.js”的文件来保存应用程序的样式表。将以下内容添加到“Styles.js”文件中。

刷新应用程序，您应该有一个简单的应用程序，在中心显示“Hello World”。

## 屏幕

屏幕是 React 组件，在应用程序中定义单独的视图。让我们用颜色来区分它们。将以下内容添加到您的“App.js”文件中。

要真正赋予它们颜色，请将以下内容添加到“Styles.js”文件的样式列表中。

太好了。这样我们就有了四个屏幕:绿色、红色、蓝色和紫色。让我们添加一些导航。

## 堆栈导航器

我们将从 React 导航中包含的 StackNavigator 开始。该导航器提供了一种在屏幕之间切换的方式，其中新屏幕放置在现有屏幕上。就像一叠卡片。

你可能对这种导航方式很熟悉，因为它在 iOS 和 Android 中都很常见。在 iOS 系统中，新屏幕从右边滑入，而在 Android 系统中，新屏幕从底部淡入。让我们试一试。

首先从 react-navigation 包中导入 StackNavigator 组件。在应用程序中打开“App.js”文件，并将以下内容添加到导入列表中。

现在，我们将通过在 PurpleScreen 组件和 AppRegistry 之间添加以下内容来调用 StackNavigator 的实例。

然后修改 ReactNav 类以呈现新的 StackNav 函数。

我们所做的是创建一个 StackNavigator，在它的导航树中将我们的屏幕注册为 routes。绿屏被设置为“绿色”路线的“屏幕”等等。刷新您的应用程序应该会弹出带有灰色标题的绿色屏幕。

让我们给标题添加一个标题。为此，我们将调用 GreenScreen 类的导航选项。为此，调用类中的静态方法。

在其他屏幕上也添加类似的标题。刷新后，我们看到一个绿色屏幕，带有灰色标题和标题“绿色”。很好，现在让我们添加一些到其他屏幕的链接。

我们将进行设置，使绿屏成为我们的登录页面。从那里，一个按钮将导致红色屏幕。

我们将通过添加一个可触摸的高光来实现这一点。在 TouchableHighlight 内部，“onPress”方法将调用“this . props . navigation . navigate(' Red ')”一个用于加载红色屏幕的帮助器方法。

打开“App.js”，将 TouchableHighlight 添加到 react-native 导入列表中，然后将 TouchableHighlight 添加到 GreenScreen 类的 render 方法中。

另外，在“Styles.js”的样式列表中添加一个“button”项。

刷新你的应用并试用。按下按钮会出现红色屏幕。它还在红色屏幕标题的左侧添加了一个按钮，可以将您导航回绿色屏幕。这是基本的 StackNavigation，其他就没什么了。让我们改变这一点。

我们要稍微修改一下红屏。新屏幕将有一个按钮，返回到主视图中的绿色屏幕，并有可触摸的对象来打开标题中的蓝色和紫色屏幕。首先，返回按钮。打开“App.js”，向 RedScreen 类添加一个 TouchableHighlight。

添加的按钮使用“onPress”方法中的“this.props.navigation.goBack()”，它是内置在 StackNavigator 中的一个帮助器。调用时，“goBack()”关闭活动屏幕并在导航列表中后退一条路线。简单有效。

除了添加按钮之外，我们还从 RedScreen 类中删除了“静态导航选项”。我们这样做是因为我们不能直接访问 props 来导航到静态声明中的另一个屏幕。相反，我们将在类本身下面单独定义“navigationOptions”。

headerRight 和 headerLeft 元素分别定义显示在标题右侧和左侧的项目。在这种情况下，添加了按钮和导航到紫色和蓝色屏幕的方法。还将“Button”添加到 react-native 导入列表中，以便应用程序可以运行。

刷新应用程序，并根据需要尝试使用按钮和后退方法在不同屏幕之间导航。现在，您已经有了一个设置了堆栈导航的应用程序，以及一个嵌入了导航链接的自定义标题。

使用 StackNavigator 组件还可以做更多的事情，我鼓励您进一步研究[文档](https://reactnavigation.org/docs/navigators/stack)。

## 选项卡导航器

首先，将 TabNavigator 组件添加到 react-navigation 导入列表中。

然后调用常量函数“TabNav”中的 TabNavigator，并像使用 StackNavigator 一样设置路由。这里将有一点额外的代码来管理“tabBarOptions”的样式。

然后，将 ReactNav 类的 render 方法中的“StackNav”替换为“TabNav”。

刷新应用程序，您应该会看到一个 TabNavigator，它使用与之前的 StackNavigator 相同的路线。如[文档](https://reactnavigation.org/docs/navigators/tab)中所述，存在各种选项来修改 TabNavigator。同样，我鼓励您查看一下，并相应地使用 TabNavigator。

## 抽屉导航器

和前面一样，将 DrawerNavigator 添加到 react-navigation 导入列表中。

之后，将使用常量函数“DrawerNav”调用 DrawerNavigator，并像在其他两个导航器中一样设置路线。

然后在 ReactNav 类的 render 方法中用“TabNav”替换“DrawerNav”。

此时，如果你刷新应用程序，你会看到没有标题的绿色屏幕。屏幕上的按钮会像以前一样将您导航到红色屏幕，但仅此而已。您会注意到红色屏幕也缺少标题。DrawerNavigator 不会自动呈现它们。

您还会注意到，您还无法导航到其他两个屏幕(蓝色和紫色)。我们需要一种打开抽屉的方法，以便使用它进行导航。

为此，使用“onPress”方法向每个彩色屏幕添加 TouchableHighlights，该方法调用辅助方法“props . navigation . navigate(' open drawer ')”。

现在刷新你的应用程序，试用你的 DrawerNavigator。与其他导航器一样，有许多选项可用于自定义 DrawerNavigator 及其内容。详见[官方文档](https://reactnavigation.org/docs/navigators/drawer)。

## 组合/嵌套导航器

虽然每一个导航器单独使用都很有用，但是在处理应用程序中的导航时，通常你会想要组合几个不同的导航器。让我们来探索如何通过嵌套来实现这一点。

在本例中，我们将设置应用程序，使其像这样工作。绿色屏幕将是“登陆/登录”屏幕。它将有一个按钮，将导航到红/蓝屏，这将作为应用程序的“登录主页”。从那里，选项卡将允许在蓝屏和红屏之间切换。反过来，这两个按钮都有一个打开“应用程序菜单”的按钮，这是一个抽屉导航器，可以在红/蓝和紫色屏幕之间导航。

为此，首先声明一个名为“NestedNav”的新 const 来保存导航器。从一个 StackNavigator 组件开始，设置绿屏的初始路径及其将我们带到蓝/红屏的能力。在这个导航器中，我们将把初始路径声明为普通路径，但是第二个名为“Drawer”的路径将包含一个 DrawerNavigator 方法。

在 DrawerNavigator 中添加“家”的路线，其屏幕将是一个 TabNavigator。该选项卡导航器将保存“红色”(红屏)和“蓝色”(蓝屏)的航路。我们还将包括一些格式选项，使标签像以前一样更清晰。

关闭 TabNavigator，并将“紫色”路线添加到抽屉导航器中，以访问紫色屏幕。

然后，修改绿屏和红屏上的可触摸高亮显示，以反映它们的新路由，并从绿屏上放下“打开抽屉”按钮。

现在用 ReactNav 类中的“NestedNav”替换掉“DrawerNav”。

刷新你的应用程序并体验一下。你现在应该有一个包含三种不同导航类型的应用程序。

谢谢你留下来！目前就这些。下次加入我们，获得更多的开发教程、技巧和诀窍。

# Github Gists 和 Repo

本教程的所有 Github 要点可以在[这里](https://gist.github.com/ridgeO/95d0f697035879fe989fded99997b1c3)找到，回购是[这里](https://github.com/PlatypusIndustries/ReactNav)。

[![](img/ff5028ba5a0041d2d76d2a155f00f05e.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/react-native) [## 学习 React Native -最佳 React Native 教程(2019) | gitconnected

### 10 大 React 原生教程。课程由开发者提交并投票，使您能够找到最好的…

gitconnected.com](https://gitconnected.com/learn/react-native)