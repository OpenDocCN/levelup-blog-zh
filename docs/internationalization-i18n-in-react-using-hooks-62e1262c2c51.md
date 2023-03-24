# React 中的国际化(i18n)使用钩子

> 原文：<https://levelup.gitconnected.com/internationalization-i18n-in-react-using-hooks-62e1262c2c51>

包含 i18next 库和单元测试的分步教程

![](img/7d8627c19db573ae0a24e9789c39d1f5.png)

照片由 [1983 年](https://unsplash.com/@unonueveochotres?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

你的客户要求你这么做。我知道，这可能会让人不知所措，尤其是对于经验不足的开发人员来说。

但是，它不是。相信我。

我也经历过同样的问题。

# 这是怎么回事

简单地说，这是一个相当耗时的任务，而不是花时间在逻辑本身上。

但是请记住，对于 i18n，应该有两个条件，您的团队会很欣赏:

*   静态文本应该按语言存储在文件中，而不是存储在代码中。
*   它必须通过单元测试

第一个条件意味着需要很多时间。所以这里有个小建议:你最好尽快开始在 JSON 文件中存储静态文本。如果需要 i18n 支持，您只需添加一个新的翻译文件。

第二种情况在大多数严肃的项目中很常见。你可以通过 React 测试库了解更多关于[单元测试的知识。](/how-to-write-unit-tests-with-react-testing-library-d9624fd2b707)

无论如何，我将向您展示如何使用钩子在 React 项目中处理这样的任务，以及如何在单元测试中处理文本。我们将使用最流行的 i18n 库之一， [react-i18next](https://react.i18next.com/) 。

我将在我的国家应用程序中实现国际化，你可以[从 GitHub](https://github.com/Dromediansk/countries-app-blog/tree/i18n) 克隆或者自己创建一个项目。

# 让我们来配置

首先，您需要安装库及其依赖项:

```
npm install react-i18next i18next i18next-http-backend --save
```

以及用于检测浏览器中的用户语言的[依赖性(可选):](https://github.com/i18next/i18next-browser-languageDetector)

```
npm install i18next-browser-languagedetector --save
```

现在，在您的`index.js`旁边创建一个包含以下内容的新文件`i18n.js`:

配置设置

然后在`index.js`中导入文件:

```
import React, { Component } from "react";
import ReactDOM from "react-dom";
import App from './App';
**import './i18n';**ReactDOM.render(<App />, document.getElementById("root"));
```

*   我们在应用程序上使用 i18next 库，
*   使用`Backend`从`/public/locales`文件夹中加载文件(稍后会添加)，
*   将 i18n 实例传递给`react-i18next`，这将使它对所有组件可用，
*   使用`init()`应用其他配置选项。当然，您可以使用可用选项对它们进行[定制。](https://www.i18next.com/overview/configuration-options)

完成配置后，在`public`文件夹中创建一个新文件夹`locales`。在这里，我们将存储每种语言的文件夹。所以在`locales`中，创建另一个文件夹`en`(或者任何你想使用的语言)并创建一个新文件`translation.json`。

这里有一个完整的英语和德语结构:

```
- public
    -- locales
        -- en
            -- translation.json
        -- de
            -- translation.json
- src
```

# 练习时间

让我们有一个简单的代表性组件，名为`Home`:

这只是一个普通的登陆页面，我们在里面展示了三个句子。现在，根据上面提到的条件，我们应该将这些静态文本移动到 JSON 文件中。

这是英文版的:

```
{
    "welcome": "Welcome",
    "appAbout": "In this app you can check basic data of all      countries in the world.",
    "explore": "Feel free to explore!"
}
```

和德语版本:

```
{
    "welcome": "Willkommen",
    "appAbout": "In diesem App können Sie Grunddata der Länder   ansehen.",
    "explore": "Fühlen Sie sich frei zu erkunden!"
}
```

请记住，在每个键值对中，`key`必须是相同的。它应该是简洁的和描述性的，因为我们将在我们的代码库中使用它。

然而，在大项目中，我推荐基于章节、组件等的单独文本。(取决于和你团队的约定)。有了 i18next 库，你完全可以做到这一点。

# useTranslation 挂钩的实现

让我们重新构建我们的`Home`组件:

在 JSX，我们调用带有`key`名称作为参数的`t`方法。非常简单。

## 当一个组件使用多个名称空间时，如何使用它？

`useTranslation()`接受字符串作为单个名称空间或名称空间数组。所以在使用 multiple 的时候，应该这样写:

```
const { t } = useTranslation(['<namespace1>', '<namespace2>', '<namespace3>'])
```

在`t`函数中，名称空间由冒号分隔:

```
<h2>{t('<namespace>:<key value>')}</h2>
```

# 切换语言

你可能想选择一种语言，通常放在导航栏的某个地方。在我的例子中，我只是在两种语言之间进行选择，所以一个简单的按钮就足够了。当然，下拉菜单也是合适的。

很简单，对吧？

是的，代码可以被清除。但是重要的是`handleSwitchLanguage`处理程序的逻辑。钩子包含`i18n.changeLanguage()`，接受语言的缩写。

我将选择的语言保存在`currentLang`状态，并相应地进行更改。对于 dropdown，这将是一个非常相似的逻辑。

# 测试呢？

当然，如果我们使用翻译，测试静态文本是没有意义的。

顺便说一下，我们在整个应用程序中使用了`react-i18next`实例，所以如果你试图测试组件，包括翻译，在 React 测试库中你会得到这个警告:

> react-i18next::您需要使用 initReactI18next 传入一个 i18next 实例

那么，我们可以模拟这个实例吗？绝对的！

下面举个例子`Home.test.js`:

在文件的开头，您需要在运行任何测试之前模拟`react-i18next`模块。在我们的例子中，我们模仿了`useTranslation()`钩子的`t`函数，它返回作为参数传递的`key`。

但重要的是，我们不再测试文本。相反，我们正在测试用 JSON 文件编写的密钥。如果代码中声明了名称空间，您也需要检查它！

所以不管哪种语言是活动的，它都会检查正确的键值。

您可以检查、克隆或派生 GitHub repo 的最终版本。

感谢阅读！