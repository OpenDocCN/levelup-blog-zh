# 您不知道存在的角度元件示意图标志

> 原文：<https://levelup.gitconnected.com/angular-component-schematic-flags-you-didnt-know-exist-eb56f9e93f39>

![](img/10fe25f624eb71b767476705504881cf.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的 [ThisisEngineering RAEng](https://unsplash.com/@thisisengineering?utm_source=medium&utm_medium=referral)

在本文中，我将与您分享角度元件原理图的 8 个选项，或者更普遍的说法是标志，它们非常有用，但许多角度开发人员却不知道。

# 1)类型标志

要指示 CLI 向生成的组件添加后缀，请使用:

```
ng generate component component_name --type = page OR ng g c component_name --type = page
```

它将创建一个 page 类型的新组件。当我想创建基本上是我们可以登陆的页面的角度路由组件时，我个人使用这个标志；这有助于区分不同类型的组件。

# 2)出口标志

```
ng g c component_name --export = true | false
```

*-export 标志也可用于指令和管道。*

现在，这个组件将被添加到生成它的模块的导出数组中，所以这个组件现在已经被导出，其他模块也可以使用它。然而，如果这个组件是在没有导出标志的情况下生成的，那么它只是父模块的声明数组的一部分，而不是导出数组的一部分。因此，只有这个特定模块中的组件能够使用这个新生成的组件。

# 3)内嵌样式标志

```
ng g c component_name --inlineStyle = true | false OR ng g c -s component_name
```

这将生成一个组件，该组件带有一个在 *component_name* 中定义的内联样式数组。 *component.ts* 文件。因此，基本上没有生成外部样式文件。

# 4)内嵌模板标志

接下来，让我们检查内联模板标志，它与内联样式标志非常相似。

```
ng g c component_name --inlineTemplate = true | false ORng g c -t component_name
```

这将生成一个组件，该组件带有一个在 *component_name* 中的组件内定义的内联模板数组。 *component.ts* 文件，您所有的 HTML 代码都可以放在那里。因此，基本上没有外部 HTML 文件生成。

# 5)前缀标志

```
ng g c component_name --prefix = prefix ORng g c --p = prefix component_name 
```

这基本上允许我们命名组件选择器，不是完全命名，但它只是给组件选择器添加了一个前缀。这有助于区分不同的组件，并且名称实际上告诉我们组件是做什么的。

# 6)跳过测试标志

```
ng g c component_name --skipTests = true | false
```

因此，顾名思义，如果您不需要*(更像是 want)* 来为您的 angular 组件编写测试，您就不需要生成 *component.spec.ts* 文件(并保持其为空)，只需使用 skipTests 标志就可以轻松地跳过生成该文件。

# 7)样式标志

```
ng g c component_name --style = css | scss | sass | less | styl
```

现在，不管听起来如何，这种*风格的*旗与我们之前看到的*直列式*旗有很大不同。*样式*标志让我们明确定义我们想要的组件的样式文件的类型。我们知道，默认情况下样式文件的类型是 CSS。但是，我们可以使用 style 标志从大量可用的样式选项中进行选择。

# 8)显示块标志

```
ng g c component_name --display-block
```

默认情况下，我们的组件显示为*内嵌*，如果我们想将其更改为*块*显示，我们只需在创建组件时使用*显示块*标志。

以上就是这些有用的角度标志/选项。我希望本文提供的信息对您有价值。感谢阅读。快乐学习！

*原载于*[*https://www.theimmigrantprogrammers.com*](https://www.theimmigrantprogrammers.com/p/angular-component-schematic-flags)*。*