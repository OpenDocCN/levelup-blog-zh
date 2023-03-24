# 使用“ngOnChanges”功能实时更改您的角度项目

> 原文：<https://levelup.gitconnected.com/get-live-changes-in-your-angular-project-using-ngonchanges-function-7d74a541bdf6>

![](img/0081af4f14119d63ca73477db3c35a2a.png)

克里斯·劳顿在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我遇到了一个非常独特的问题，我需要我的 Angular web 组件对它接收到的输入变化做出反应。经过一番浏览后，我发现了 ngOnChanges，老实说，Angular 网站上的指南有点令人困惑。

所以这里有一个简单的指南，告诉你如何在 Angular web 组件中使用 ngOnChanges。

**0。假设你用角度 10+来建筑。设置一个有角度的 web 组件**

1.  **从@angular/core 导入输入、OnChanges 和 SimpleChanges】**

在你的组件文件的第 1 行，在这种情况下，我将构建一个名为 TestComponent 的组件，我们需要从@angular/core 导入输入、OnChanges 和 SimpleChanges 到我们的项目中

```
import {Component, Input, HostBinding, **Input**, **OnChanges, SimpleChanges**} from '@angular/core
```

**2。在类名附近，将** `**implements OnInit**` **替换为** `**implements OnChanges**`

虽然可以同时使用两者，但是为了这个例子，让我们单独使用`implements OnChanges`。

在我们的 TestComponent.ts 文件中，我们将修改导出类名行，如下所示。同时，我冒昧地声明了一个输入，该输入将从该组件的调用者处传入。

```
export class TestComponent implements OnChanges {@Input('theCode') theCode:string;constructor(){}...}
```

**3。编写 ngOnChanges 函数**

在 TestComponent 内部，我们需要编写 ngOnChanges 函数。每次变量发生变化时，都会执行该函数。

```
ngOnChanges(changes:SimpleChanges){ const theValues=changes['theCode']; if(theValues.previousValue != theValues.currentValue){ //The value changed, do something
 }else if(theValues.previousValue == theValues.currentValue){ //The value did not change, do something
 } }
```

在上面的例子中，我已经声明`theValues`有`changes[‘theCode’]`的值。变量`changes`将提供 3 样东西:

*   `previousValue` —这将告诉我们最近更改的前一个值
*   `currentValue` —这将告诉我们最近更改为的当前值
*   `firstChange` —这将告诉我们该变更是否是首次变更

在本例中，如果`theCode`有变化，将执行 `ngOnChange`。我使用`changes`变量来检查在变更事件发生后`previousValue`和`theCode`的`currentValue`是否有差异。

如果有区别，我可以很容易地执行 if…else if 语句中的任何逻辑。

就是这样，如果`theCode`被更改，您应该能够检查任何更改。如果有重要的变化，只需使用`ngOnChange`就可以帮助您执行特定的逻辑。

**结论**

现在，如果变量和输入发生变化，这是一个简单快捷的解决方案，可以解决在执行逻辑时出现的问题。这不是解决这个问题的唯一方法，也可以使用 setters、getters 和 event emitters，但是如果记录以前的值、当前值以及是否第一次进行更改很重要，这可能是正确的解决方案。

希望这个解决方案能对你的角度编程冒险有用。

*塞拉马特·孟加图拉*