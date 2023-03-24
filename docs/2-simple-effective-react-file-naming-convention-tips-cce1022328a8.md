# 2 个简单有效的 React 文件命名约定提示

> 原文：<https://levelup.gitconnected.com/2-simple-effective-react-file-naming-convention-tips-cce1022328a8>

![](img/a16abed406ab6a8e8bcb60dd14356a51.png)

照片由[阿玛多·洛雷罗](https://unsplash.com/@amadorloureiroblanco?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 有效的文件命名约定可以帮助您在处理多个文件时避免混淆，可以帮助其他人识别文件是做什么的或者是什么，并且允许基于关系的简单提示。

无论您是否已经有了一个可靠的文件命名约定，或者您正在寻找更多关于如何命名的提示，我已经列出了一个超级简短的提示列表，可以帮助您优化文件名。

下面的提示和技巧都围绕着一个简单而有效的方法:根据它们是什么以及它们之间的关系来命名你的文件。

## 1.将与组件相关的文件与组件名称匹配

因为您的组件名称已经遵循了 PascalCase 命名约定(是的，不是吗…？)，让我们继续下去:

```
├── components
│   ├── SomeComponent
│   │   ├── SomeComponent.test.jsx
│   │   ├── SomeComponent.jsx
│   │   └── SomeComponent.styles.js
```

上面的例子保持组件的文件夹和相关的文件都有相同的名字，只是文件扩展名不同。

## 2.根据非组件文件是什么或做什么来命名它们

与组件无关的文件更容易处理或使用，如果它的名字能给你一些提示的话。这将主要影响你的行动、减少者、提供者、挂钩等。理解以这种方式命名文件的价值的最简单的方法是通过下面的例子来说明，其中一个例子为编写代码的人提供了上下文，而另一个例子则没有:

**提供上下文**

```
├── actions
│   ├── anotherAction
│   │   ├── anotherAction.test.ts
│   │   └── anotherAction.ts
│   └── someAction
│       ├── someAction.test.ts
│       └── someAction.ts
```

**不提供上下文**

```
├── actions
│   ├── another
│   │   ├── another.test.ts
│   │   └── another.ts
│   ├── some
│   │   ├── some.test.ts
│   │   └── some.ts
```

这是一个简单的例子，显示了在文件名周围添加上下文比没有上下文要容易得多。如果文件夹中有同名文件，浏览文件或理解文件结构的位置只会变得更加困难。想象一下，如果您也有一个`reducers`文件夹，其中的文件夹与上面示例中的`actions`文件夹中的文件夹完全匹配…您会因为 IDE 没有提供足够的上下文而感到厌恶！幸运的是，像 [VSCode](https://code.visualstudio.com/) 这样的编辑器已经开始在文件打开时添加面包屑，以提供一点理智。

在为 React 应用程序编写代码时，您最不需要担心的就是命名文件。文件命名约定可以大大减少浏览[文件结构](https://medium.com/javascript-in-plain-english/solving-the-file-structure-problem-in-react-apps-4b5b5dca775b)的时间，并帮助避免不可避免的代码编辑错误，因为在不同的文件夹中有多个同名文件。文件命名约定还可以帮助你识别*是什么*，以及它对其他文件负责或做什么。

请随意建议您认为对 React 应用程序中的文件命名约定有用的其他技巧！