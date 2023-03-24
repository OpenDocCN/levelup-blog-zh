# React Native 中的本地化和 React 导航

> 原文：<https://levelup.gitconnected.com/localization-in-react-native-with-react-navigation-4f4e2ad2c8d0>

![](img/db24184da5d1482b6dc8389a4a64e69a.png)

照片由 [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我将介绍如何使用 React 导航来国际化您的 React 本地应用程序。它将在现有的[指南](https://reactnavigation.org/docs/en/localization.html)上进行扩展，通过添加选项卡或抽屉导航器所需的步骤，对屏幕的本地化进行反应导航。

如果您想使用它来跟进，下面是完整的代码:

抽屉导航:[https://github . com/mariesta/localization-drawer-react-navigation](https://github.com/mariesta/localization-drawer-react-navigation)

tabs navigator:[https://github . com/mariesta/localization-tabs-react-navigation](https://github.com/mariesta/localization-tabs-react-navigation)

本教程中使用的主要技术:

*   反应原生 0.61
*   反应导航 4
*   i18next 19 & react-i18next 10(但是您可以用它替换您自己的框架)
*   Expo SDK 36(还是个人喜好。不一定要用)

如果你需要的话，我会在底部添加所有的文档。

注意:我将使用 react-i18next 作为我的国际化框架。如果您决定也这样做，请检查您的 React Native 版本。react-i18next (v10)的最新版本使用了仅在 react 原生版本 v0.59 或更高版本上可用的钩子。如果你有一个旧版本的 React Native，不用担心。您可以使用 react-i18next (v9)的遗留版本，它工作得一样好，但是一些函数已经被重命名，所以要小心那些。

# 项目设置

我个人使用 Expo 来创建和运行我的 React 原生应用。要创建一个新项目，只需运行 expo init [project name],然后将 cd 放入您的项目。

用 npm(或 yarn)安装 i18next 和 react-i18next

```
npm install i18next react-i18next
```

如果您需要抽屉导航器的图标，我可以建议您使用 React 本地元素吗？

```
npm install react-native-elements
```

# i18n.js

在我们的项目根目录中，我们将创建一个 i18n.js 文件。同样，这取决于您的国际化框架。

```
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';// the translations
// (tip: move them in separate JSON files and import them)
const resources = {
  en: {
    translation: {
      "screen": "Screen {{ order }}",
      "tab": "Tab {{ order }}",
      "change_language_english": "In english",
      "change_language_french": "In french"
    }
  },
  fr: {
    translation: {
      "screen": "Écran {{ order }}",
      "tab": "Onglet {{ order }}",
      "change_language_english": "En anglais",
      "change_language_french": "En français"
    }
  }
};i18n
  .use(initReactI18next)
  // init i18next
  // for all options read: [https://www.i18next.com/overview/configuration-options](https://www.i18next.com/overview/configuration-options)
  .init({
    resources,
    lng: "en",
    fallbackLng: 'en',
    debug: true,
    interpolation: {
      escapeValue: false
    }
  });export default i18n;
```

首先，我们创建我们的翻译。随着它不断增长，我建议您为每种语言创建一个单独的 JSON 文件，并将它们导入到这个文件中。其次，我们通过设置一些配置来创建我们的`i18n`实例。

# App.js

完成 i18n 文件后，我们将其导入到 App.js 文件中。

```
import React, { useState } from 'react';
...import AppNavigator from './navigation/AppNavigator';
import './i18n';export default function App(props) {
  return (
      <View>
          ...
          <AppNavigator />
      </View>
    );
}
```

# AppNavigator.js

AppNavigator.js 就是我们的`AppContainer`住的地方。

React Navigation 4 有一个非常方便的名为`screenProps`的全局`props`，它允许我们传递所有子导航器都可以访问的道具。这将对我们非常有用，因为它将允许我们翻译标签、抽屉等等中的所有标签。

```
import React from 'react';
import { createAppContainer } from 'react-navigation';
import { withTranslation } from 'react-i18next';import MainNavigator from './MainNavigator';const AppContainer = createAppContainer(MainNavigator);class AppNavigator extends React.Component { render() {
    const { t, i18n } = this.props; return (
      <AppContainer
        screenProps={{
          t,
          i18n
        }}
      />
    );
  }
}export default withTranslation()(AppNavigator);
```

首先，我们导入主导航器，这将在下一步中介绍。第二，我们用它创造我们的`AppContainer`。第三，我们用`withTranslation()`包装我们的`AppNavigator`，这将添加 i18n 实例和`t`函数。i18n 实例用于更改语言(比如说`i18n.changeLanguage(‘fr’)`)，而 t 函数有助于通过传入键来翻译我们的内容，并获得基于当前语言的内容(例如，如果语言是英语，则`t(‘hi’)`将返回“Hello ”,如果语言是法语，则返回“Bonjour”——假设您在 i18n 文件中设置了正确的翻译)。

一旦我们得到这两个，我们就把它加到我上面提到的`screenProps`中。

# 主导航器. js

现在我们添加了这两个，我们如何访问它们呢？

1)选项卡导航器

为了创建我们的`bottomTabNavigator`，我将使用一系列堆栈。

注意:如果你不知道什么是栈，有大量的文档包括这篇特殊的[文章](https://medium.com/async-la/react-navigation-stacks-tabs-and-drawers-oh-my-92edd606e4db)。

本质上，一个`stackNavigator`看起来像这样:

```
const Tab1Stack = createStackNavigator(
  {
    Home: {
      screen: Screen1
    }
    ... // include all the screens that will fall under Tab 1 
  }
);
```

还记得上一步的`screenProps`吗？我们不仅要用它来定义一个标签，还要翻译它。

```
Tab1Stack.navigationOptions = ({ screenProps: { t } }) => (
  {
    tabBarLabel: t('tab', { order: 1 })
  }
);
```

我们从`screenProps`中获取`t`函数，并用它来获取选项卡的正确标签。

如果你想翻译屏幕的标题，你可以使用同样的逻辑，稍微调整一下你的`stackNavigator`。

```
const Tab1Stack = createStackNavigator(
  {
    Home: {
      screen: Screen1,
      navigationOptions: ({ screenProps: { t } }) => ({
        title: t('screen', { order: 1 })
      })
    }
  }
);
```

创建尽可能多的标签堆栈，然后用以下内容结束文件:

```
const tabNavigator = createBottomTabNavigator({
  Tab1Stack,
  Tab2Stack,
  Tab3Stack,
});export default tabNavigator;
```

这里的是 MainNavigator.js 的完整代码

2)抽屉导航器

和 tab Navigator 区别不大。首先，我们有一个堆栈:

```
const Drawer1Stack = createStackNavigator(
  {
    Home: {
      screen: Screen1,
    },
     ... // include all the screens for your first drawer option
  }
);
```

只有当我们为堆栈设置导航选项时，才不设置`tabBarLabel`和`tabBarIcon`，而是设置`drawerLabel`。

```
Drawer1Stack.navigationOptions = ({ screenProps: { t } }) => ({
  drawerLabel: t('screen', { order: 1 }),
});
```

堆栈中特定屏幕标题的`navigationOptions`也是类似的。除了我在左边加了一个`Icon`用来拨动抽屉。

```
const Drawer1Stack = createStackNavigator(
  {
    Home: {
      screen: Screen1,
      navigationOptions: ({ navigation, screenProps: { t } }) => ({
        title: t('screen', { order: 1 }),
        headerLeft:
        <Icon
          name='bars'
          size={18}
          type='font-awesome'
          containerStyle={{paddingLeft: 10}}
          onPress={() => navigation.toggleDrawer()} />
      })
    }
  }
);
```

根据需要创建尽可能多的堆栈，用您的`drawerNavigator`完成您的文件:

```
const drawerNavigator = createDrawerNavigator({
  Drawer1Stack,
  Drawer2Stack,
  Drawer3Stack
});export default drawerNavigator;
```

[这里的](https://github.com/mariesta/localization-drawer-react-navigation/blob/master/navigation/DrawerNavigator.js)是 DrawerNavigator.js 的完整代码

# 改变语言

现在我们已经设置了我们的导航器，我们如何通过改变语言来测试它呢？谢天谢地，`screenProps`不仅仅局限于栈和导航仪。它也可以在屏幕中访问。这允许我们这样做:

```
import React from 'react';
import { Button, View, StyleSheet } from 'react-native';export default class Screen1 extends React.Component {render() {
    let { t, i18n} = this.props.screenProps;
    return (
      <View style={styles.container}>
        <Button
          transparent
          onPress={() => i18n.changeLanguage('en')}
          title={t('change_language_english')}/>
        <Button
          transparent
          onPress={() => i18n.changeLanguage('fr')}
          title={t('change_language_french')}/>
      </View>
    );
  }
}const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    paddingTop: 20
  },
});
```

`i18n`实例和`t`函数现在都可用了，不仅允许我们翻译按钮标题，还允许我们改变语言。

如果你喜欢这篇文章，你可以在 Twitter 上关注我。