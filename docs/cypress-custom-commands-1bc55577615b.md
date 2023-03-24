# Cypress 自定义命令

> 原文：<https://levelup.gitconnected.com/cypress-custom-commands-1bc55577615b>

## 编程；编排

## 用 Cypress E2E 框架编写你自己的定制命令

![](img/d3f30fde4ee3041541c0d74405a2c4fb.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

Cypress 是一个 JavaScript 端到端(E2E)框架，你可以在其中测试你的应用程序的 UI。在 Cypress 中有一些内置的命令，可以用来做几乎任何事情。但是在某些情况下，你需要有自己的指挥权。因此，您可以执行特定的操作，或者想要重用重复的代码块。因此，我们有“自定义命令”。让我们开始吧。

# 自定义命令

在您的 Cypress 项目中，您会有一个“cypress > support > commands”文件夹。在此文件夹中，您可以创建一个名为“example.js”的新文件，如果您已经有(如果没有，请创建)一个“index.js”文件，请在其中添加以下代码。

```
import './example';
```

现在，在您的示例文件中，我们可以创建自己的 Cypress 自定义命令。像这样:

```
Cypress.Commands.**add**('**example**', **() => {}**);
```

使用上面的代码，您“添加”了一个带有两个参数的命令；第一个是命令名，第二个是回调函数。在这个功能中，你可以施展你所有的魔法。让我们试着添加一些东西来使它工作:

```
**Cypress.Commands.add('example', (pathEnd) => {
  const path = `some-really-long-url-with-a-changing-${pathEnd}`;
  cy.visit(path);
});**
```

在示例函数中，我输入了一个“cy.visit”命令，该命令将您重定向到某个 url(注意:baseUrl 是在配置中设置的)。我们通过我们自己的路径，在这种情况下，这是一条非常长的路径，并且路径的终点经常变化。因此，在这个漂亮的例子中，你可以将路径末端作为参数传递(如果你愿意，你可以添加更多)。

在您的 Cypress 测试中，您现在应该能够使用它了。请参见下面的示例:

```
it('my-test', () => { 
  // some code here **cy.example('path-end-one');** // some other code there
})
```

现在您有了一个可重用的定制命令，您可以在每个测试中使用它！