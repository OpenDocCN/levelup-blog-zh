# 介绍反应原生旋风

> 原文：<https://levelup.gitconnected.com/introducing-react-native-whirlwind-1c3ad9ffd4a5>

设计 React 本机应用程序样式的现代方法

![](img/00d5c0aaeaa405fa3d0bd014548d76a5.png)

罗伯特·谢尔比年科拍摄的照片；通过 Unsplash

# 为什么是另一个样式框架？

我非常喜欢在我的 React web 项目中使用[风格的组件](https://styled-components.com/),因为它们提供了一种简单有效的方式来创建完全风格的、可重用的 React 组件。然而，当使用 React Native 时，样式突然变得非常有限。您不能设置子组件的样式或选择同级元素。不能用`:hover`或者`:focus`这样的伪类。您不能使用媒体查询。不能用`:before`和`:after`、`:first-child`和`:last-child`，也不能用`:nth-child`伪类。这些限制使得定义可重用的样式变得繁琐和逐字逐句，而且许多在响应式网站上学到的东西根本不适用于 React Native。原因是 React Native 使用的是 [Yoga](https://yogalayout.com/) 布局引擎，而不是像网页一样使用 CSS。任何样式框架都需要从一开始就考虑这些限制。

虽然有许多用于 web 的 CSS 框架，但我发现只有少数是专门为 React Native 设计的。几年前，我偶然发现了用于在 React 和 Vue 中设计网页样式的 [Tailwind CSS](https://tailwindcss.com/) 框架。从概念上讲，这似乎准确地捕捉到了我对 React Native 中样式系统的需求。然而，各种 React Native(重新)实现并没有说服我: [tailwind-rn](https://github.com/vadimdemedes/tailwind-rn) 和[tailwind-React-Native-class names](https://github.com/jaredh159/tailwind-react-native-classnames)都使用自定义字符串来模拟 tailwind 的 CSS 类系统。不幸的是，两者都没有在 Visual Studio 代码中提供自动完成提示，这使得它们很难使用，并且对于团队中不熟悉 Tailwind 术语的新开发人员来说容易出错。我更赞成用 [react-native-tailwindcss](https://github.com/TVke/react-native-tailwindcss) 实现的样式类系统。但是缺乏对 Typescript 的支持对我来说是一个直接的阻碍。尽管如此，我今天要介绍的框架 **React Native Whirlwind** 的一些概念和源代码都是直接来源于[React-Native-tailwindcss](https://github.com/TVke/react-native-tailwindcss)。

# 那么，什么是反应原生旋风呢？

先说 **React 原生旋风**不是什么:它不像 Material UI 或者 [React 原生元素](https://reactnativeelements.com/)那样是一个成熟的 UI 框架。事实上，你可以将 React Native Whirlwind 与 React Native Elements 结合使用，这是我在许多 React Native 项目中正在做的事情。它也不是一个新的组件样式框架，比如[样式组件](https://styled-components.com/)。但是你可以用 React 原生旋风来[T21 你的样式组件](https://arabold.github.io/react-native-whirlwind/core-concepts/theming)！

**React Native Whirlwind** 是专门为 [React Native](https://reactnative.dev/) 设计的实用至上的 CSS 框架。它在很大程度上受到了[超光速粒子](https://tachyons.io/)和[顺风 CSS](https://tailwindcss.com/) 的启发，并使用低级构建模块来快速构建定制设计。

一些核心设计原则是:

*   **可读**👀—所有类都遵循简单、一致的命名约定
*   **轻量级** 🪶 —不依赖第三方
*   **可组合**🧱——用于快速成型的可组合类
*   **表演**🚀—没有不必要的计算，没有不必要的字符串解析，只有纯粹而快速的静态样式
*   可重用的♻️——提高团队的可重用性，减少代码库中的冗余
*   **先反应原生和打字稿**🥇—专为 React Native 构建，100%以 TypeScript 编写，提供一流的开发人员体验

# 给我看看！

React Native 有一个强大的特性，它允许你传递一个样式数组，而不仅仅是一个对象到组件的属性。这可以用来继承和扩展样式。React Nativ Whirlwind 依靠这个机制来提供一个简单的、可组合的 API 来构建定制设计。

## “传统”方式:定制设计需要定制风格

传统上，您会使用 React Native 的`StyleSheet`类来定义您的样式，为您设计的每个元素定义一个样式类:

## 旋风方式:使用实用程序类而不使用编写风格

**Whirlwind** 不使用语义 CSS 类，而是鼓励使用自由组合的实用类来设计您的最终外观:

实用程序类是可重用的构建块，带有一个内置的主题，您可以很容易地更改它。在上述设计中，我们使用了:

*   `t.mT9`和`t.mT2`定义一个顶部边距。我们的默认主题分别将其初始化为 48 像素和 4 像素。
*   `t.sansBold`，一个选择无衬线字体的实用程序类，
*   和`t.font3Xl`使用更大的字体。
*   最后，`t.textPrimary`用我们的原色渲染文本，这可以在我们的主题中定义。

这种方法允许我们实现完全自定义的组件设计，而无需定义一个单独的自定义样式类。通过在整个应用程序中重用相同的构建模块，您可以确保一致的外观和感觉，同时还可以通过 Whirlwind 的主题引擎提供很大的灵活性。

实用类的使用听起来仍然很糟糕吗？相信我，很快就会感觉很自然，**但你可能必须尝试一下，看看效果如何**！

## 活生生的例子

这里有一个 React Native Whirlwind 的简单实例，你可以在手机或浏览器上试试:

[](https://snack.expo.dev/@arabold/react-native-whirlwind) [## 反应-本地-旋风-小吃

### 在你的手机上尝试这个项目！使用 Expo 的在线编辑器进行更改并保存您自己的副本。

零食. expo.dev](https://snack.expo.dev/@arabold/react-native-whirlwind) 

# 好吧，我们开始吧！

**React 原生旋风**目前正在公测。然而，这并不意味着它还不能用于生产。事实上，它已经在 Google Play 和 iOS 应用商店的几个不同的应用程序中使用，总计有几十万活跃用户。测试版指的是 API 还没有完全成熟，仍然缺少一些文档、操作指南等等。但是底层实现只是简单的、静态的 React 本机样式定义。这使得它既快如闪电，又非常坚固。

**第一步:使用`npm`(或者`yarn`，安装 React Native Whirlwind】的最新版本:**

```
npm install react-native-whirlwind
```

**第二步:创建你的第一个主题定义**

这将自定义你的应用程序的主要和次要颜色。输出的常量`t`作为旋风风格系统的入口点。您可以使用任何变量名，但我建议使用简短易记的名称，因为它将在您的应用程序中大量使用。换句话说:只要坚持使用`t`😉

第三步:使用你的主题！

将您的`theme.jsx`导入到您的应用程序中，并根据需要导入组件:

**就是这样！真的没有更多的了。**

查看 React Native Whirlwind 的[文档，获取所有可用样式类的列表，以及如何进一步](https://arabold.github.io/react-native-whirlwind/)[定制你的主题](https://arabold.github.io/react-native-whirlwind/core-concepts/theming)。

# 语义 CSS、功能 CSS 和“关注点分离”

在我结束这篇文章之前，我想写下一些关于使用语义和功能 CSS 的想法，因为我以前听说过一个问题:对于许多开发人员来说，使用实用优先的 CSS 类似乎是一个坏主意，违反了“关注点分离”的概念。多年来，我们已经知道 HTML 应该只包含内容，而所有的样式细节都应该在 CSS 中专门定义。CSS 在整体设计组件样式方面非常强大，它可以有效地为子组件、兄弟组件等定义样式。许多工程团队遵循这种分离 HTML 和 CSS 的策略，有些人虔诚地这样做。

然而，这种态度似乎又开始转变，公用事业优先和混合解决方案[最近变得更受欢迎](https://wptavern.com/state-of-css-2020-survey-results-tailwind-css-wins-most-adopted-technology-utility-first-css-on-the-rise)。Adam Wathan， [Tailwind CSS](https://tailwindcss.com/) 的作者在这里写了他创建实用优先 CSS 类[的动机。因此，我不会在这里重复他的所有观点，但我会尝试给出一个简短的总结:](https://adamwathan.me/css-utility-classes-and-separation-of-concerns/)

相似的组件应该使用相似的样式。然而，很多时候你会发现自己已经有了一个先前创建的 CSS 类——例如，一个`.author-bio`类来设计你网页上的图书作者的信息卡。现在您想重用这种风格来为不同的书籍呈现信息卡。所以，你要么将你的`.author-bio`类重命名为一个更通用的、与内容无关的、在两种情况下都可以使用的类，最终导致一个`.card`或`.content`类。或者，将您的`.author-bio`样式复制粘贴到您的新书组件中，导致代码重复。

基本上你有两个选择:

1.  以你的样式所对应的组件命名你的样式(比如`.author-bio`)，创建依赖于 HTML 的 *CSS。在这个模型中，您的 HTML 是可重新设计的(您只需更改 CSS 和组件更新)，但是您的 CSS 是不可重用的。*
2.  以内容不可知的方式命名你的样式，比如`.card`或`.content`，在这种情况下你的 *HTML 依赖于你的 CSS* 。在这个模型中，你的 CSS 是可重用的，但是你的 HTML 是不可重用的。

**那么你更喜欢哪一个**:可重构的 HTML 还是可重用的 CSS？

根据我的经验，我创建的任何网站或应用程序都没有利用第一个选项提供的灵活性。专业网站不会经常改变，当他们改变时，通常是因为有许多新页面和组件的重大重新设计，而不仅仅是样式上的简单改变。这就是为什么我一般推荐第二个选项:可重用性，而不是重新设计我的 HTML。实用类将这种选择推向了极端:类变得越来越简单，以至于一个 CSS 类只能做一件事情。

这种方法给我们带来了几个好处:

*   **不再担心类名**。不要浪费大脑能量在`.author-bio`、`.author-bio-wrapper`、`.author-bio-inner-container`、`.author-bio-title`、`.author-bio-title-wrapper`和其他愚蠢的名字上，因为那只是一个 flexbox。
*   **让您的设计更加稳健**。再也不用担心在一个地方改变设计会破坏另一个地方的设计。跨多个页面和组件重用复杂的 CSS 类会带来隐藏的、难以管理的依赖性。更改公共类通常会导致无法预料的后果。但是有了实用程序类，更新组件的设计就像切换出必要的类一样简单，而且没有副作用。
*   **车载工程师更快**。Whirlwind 的实用程序类已经定义好了，您可以马上开始使用它们。他们总是以特定的方式行动。使用一致的颜色和间距方案可以使整个应用程序保持一致。

我希望我能概述我创作**反应原生旋风**的理由，并能说服你在你的下一个项目中尝试一下。

[](https://github.com/arabold/react-native-whirlwind) [## GitHub-arabold/react-native-旋风

### Whirlwind 是一个实用至上的 CSS 框架，专门为 React Native 设计。它深受超光速粒子的启发…

github.com](https://github.com/arabold/react-native-whirlwind)