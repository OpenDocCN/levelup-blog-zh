# Cypress:对属性值测试的深入理解

> 原文：<https://levelup.gitconnected.com/cypress-a-deeper-understanding-of-testing-for-an-attributes-value-a801d30f80e4>

## [柏树测试](https://tod-gentille.medium.com/cypress-testing-articles-b2f24fa9b61b?sk=041f4e3d5b1a6a187cf26e475b6b802e)

## 我们不仅要看两种测试具有特定值的属性是否存在的方法，还要寻找隐藏在语法背后的不可见命令。

![](img/3cc1b3f9233b3373610975bf70fb1e2f.png)

有时候很难看出 Cypress 语法到底在做什么

**警告**:这将是一篇链接很重的文章。我写这篇文章的意图是，你可以从上到下地阅读它，而不用去任何链接，但之后探索它们，以充分了解正在发生的事情和原因。

我使用的是 Pluralsight 设计系统中的[线性进度](https://design-system.pluralsight.com/components/linearprogress#linear-progress)小部件。设置值很简单，但是测试它是否有正确的值就不那么明确了。幸运的是，小部件创建了一个名为`aria-valuenow`的属性，其值代表进度值，这是我**可以**测试的。

# 方法 1

对于这个例子，让我们假设我们的小部件`progress-widget.`有一个`data-cy`属性，如果我们期望小部件的值为 30，我们可以这样编写我们的测试:

```
cy.dataCy(`progress-widget`).should(`have.attr`, `aria-valuenow`, `30`)
```

*(*[*)dataCy 是自定义命令*](/navigating-cypress-custom-commands-412a817b088?sk=f176c6ef524d6fc83ecf309336a7ce06) *为* `*get(`[data-cy=...])*` *)*

这看起来很清楚，但是对我来说，这显然不是查找属性和检查其值的正确语法。即使是现在，它似乎仍然有点神奇。例如，为什么我把`have.attr`作为字符串传递，而不是写`.should.have.attr('aria-valuenow').eql('30')`。

我们在测试中使用的方法被称为使用一个[隐式主题](https://docs.cypress.io/guides/core-concepts/introduction-to-cypress#Implicit-Subjects)。我们还利用了 Cypress 引入其他库的优势，让我们可以使用它们，而不需要额外的导入。在这种情况下，`'have.attr'`正在利用 [chai-jquery 库](https://www.chaijs.com/plugins/chai-jquery/)中的`attr()`断言。这些文档向您展示了`attr()`命令需要一个参数作为属性的名称，一个可选参数作为测试的值。在我们的`should()`调用中，我们将这些作为第二个和第三个字符串传递。如果我们想测试一个特定的主题没有特定的属性，我们可以用`should(not.have.attr...)`来完成。

# 方法 2

虽然这是我测试属性的首选方式，但是你也可以用`invoke()`命令来完成。该测试的代码如下所示:

```
cy.dataCy(`progress-widget`).invoke(`attr`,`aria-valuenow`).should(`eq`, `30`)
```

在这个例子中，我们不再使用 chai-jquery，而是直接使用 [jQuery](https://api.jquery.com/attr/) `[attr()](https://api.jquery.com/attr/)`命令。我们调用`attr()`来获取属性列表，然后第二个参数返回感兴趣的特定参数，我们测试该参数是否等于特定值。现在，如果你阅读 [invoke()文档](https://docs.cypress.io/api/commands/invoke)，它们会更有意义。

如果你只是想知道如何测试一个属性，我希望你已经学会了。如果你想知道更多关于引擎盖下发生的事情，我希望我也有所帮助。如果你只能跟一个链接，我建议你看一下关于[隐性主语](https://docs.cypress.io/guides/core-concepts/introduction-to-cypress#Implicit-Subjects)的简要说明。

本系列还有[其他文章](https://tod-gentille.medium.com/cypress-testing-articles-b2f24fa9b61b?sk=041f4e3d5b1a6a187cf26e475b6b802e)。
代号安安。