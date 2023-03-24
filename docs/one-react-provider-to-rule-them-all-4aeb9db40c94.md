# 一个 React 提供者来管理它们

> 原文：<https://levelup.gitconnected.com/one-react-provider-to-rule-them-all-4aeb9db40c94>

## 集中 React 提供者以获得更好的维护和可读性

![](img/5b6d5c5d185b26a3f4effeb56f7dc8ee.png)

Alexey Topolyanskiy 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 介绍

这篇文章将向您展示 React 应用程序中提供者的常见情况，以及如何使它更易于维护和阅读。

# “问题”

我在 React 应用中编写并看到过这样的代码:

```
function App() {//...return (
    <AuthProvider>
      <ReduxProvider store={store}>
        <ThemeProvider theme={theme}>
          <IntlProvider locale={locale}>
            <Router>
              <Switch>
                <Route path="/about">
                    <About />
                </Route>
                <Route path="/">
                    <Home />
                </Route>
              </Switch>
            </Router>
          </IntlProvider>
        </ThemeProvider>
      </ReduxProvider>
    </AuthProvider>
  );
}
```

这本身没有什么错，但可以做得更好。例如，如果我们想要使用 Storybook，我们可能应该这样做，那么很有可能许多 React 组件将需要一些(如果不是全部)这样的提供者。很可能是 IntlProvider 和 ThemeProvider，甚至可能是 ReduxProvider。此外，我们可能需要它们来进行 React 组件的单元测试。如果一些组件使用 React 钩子来访问 React 上下文，那么就保证需要一个提供者。

# **如何让它变得更好**

因为 React 中的几乎所有东西都是一个组件，所以让我们创建一个新的组件来集中应用程序中的所有提供者。它看起来像这样:

```
// file: AppProvider.tsxconst AppProvider = ({ children, store, theme, locale }) => {
  return (
    <AuthProvider>
      <ReduxProvider store={store}>
        <ThemeProvider theme={theme}>
          <IntlProvider locale={locale}>
            {children}
          </IntlProvider>
        </ThemeProvider>
      </ReduxProvider>
    </AuthProvider>
  );
};export default AppProvider;
```

然后使用它:

```
// file: App.tsximport AppProvider from './AppProvider';function App() {

  //... return (
    <AppProvider store={store} theme={theme} locale={locale}>
      <Router>
        <Switch>
          <Route path="/about">
            <About />
          </Route>
          <Route path="/">
            <Home />
          </Route>
        </Switch>
      </Router>
    </AppProvider>
  );
}
```

AppProvider 组件集中了应用程序的所有全局提供者。我想强调这一点；在这里，我们应该只包括对应用程序运行至关重要的提供商。通常，它们是关于本地化、主题化、全局状态管理、路由和认证的。

AppProvider 接受任何其他提供者所需的道具，允许我们针对不同的上下文以不同的方式设置提供者。

例如，如果我们需要 Storybook 中所有可用的提供者，我们可以这样做:

```
// file: SomeComponent.stories.jsexport default {
  component: SomeComponent,
  decorators: [
    (Story) => (
      <AppProvider store={store} theme={theme} locale={locale}>
        <Story />
      </AppProvider>
    ),
  ],
};
```

这种方法的优点是:

*   对于提供者来说，如果需要修改，只需在一个地方修改即可。这也有助于维护。
*   复用性
*   更容易阅读。当我们浏览应用文件时，我们专注于重要的内容。当我们看到 AppProvider 组件时，我们发现它为应用程序提供了一些共享状态和功能。如果我们需要进一步挖掘，只需打开那个文件，只关注提供者。根据我的经验，像这样的可读性优化有助于避免阅读代码时不必要的脑力劳动。最有可能的是，代码库将会增长，可读性和简单性对于维护将会非常重要。

# 警告

作为专业人士，我们工作的很大一部分是利用我们的判断和经验做出好的决定。所以，我们必须练习。如果某些提供程序比解决方案更复杂，那么不要将其包含在 AppProvider 组件中。例如，故事书或测试中很可能不需要像 AuthProvider 这样的东西。也许最好不要把它包含在 AppProvider 里，保留在 App 文件里。这要根据具体情况来决定。

感谢您的阅读，我希望这对您有用。

如果你有什么推荐或建议，请在评论中告诉我。

干杯！