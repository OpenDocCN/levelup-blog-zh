# 角度验证引擎

> 原文：<https://levelup.gitconnected.com/angular-validations-engine-a4c06b7d3274>

![](img/e62a8b9fc9cf496b7045d071a43f7a2e.png)

来源:angular.io

大家好！在本文中，我们将讨论反应式表单，以及如何设置一个通用引擎来处理错误，而无需创建重复的条件代码来显示基于反应式表单当前状态的错误消息。

自从我开始使用 angular，特别是反应式表单，我总是有点厌倦，我需要不断地重复相同的代码，只是为了检查特定的 formControl 中是否存在错误，然后有条件地显示错误消息。

有一天，我想知道是否有可能创建一些代码，将普遍适用于我的所有表单，每当 formControl 上发生错误时，它将能够简单地检查错误键并显示相应的错误消息。

所以，让我们开始挖掘它，并把我们的手！

我们的用户应该能够用合适的语言与我们的应用程序进行交互。为了实现我们的多语言逻辑，我们将使用一个名为 ngx-translate 的不错的库。让我们来设置一下:

```
npm install @ngx-translate/core --savenpm install @ngx-translate/http-loader --save
```

在 assets/i18n 文件夹中添加必要的英语和法语翻译文件:

设置应用模块:

现在我们已经有了翻译库，我们需要一个表单来开始工作。让我们把它放在我们的 app.component.html 档案里:

让我们在 app.component.ts 文件中添加必要的逻辑:

一个 html 表单由一个或多个表单控件元素组成，用来设置表单数据，所以对于每个元素，我们都需要一个相应的角度反应式表单控件来处理验证逻辑。所以让我们建立一个角度反应表单组来关联我们的表单和表单控制元素。但是，与通常手工创建不同，让我们使用一个非常好的库 RxWeb，它利用 typescript decorators 来帮助我们生成表单组。

首先让我们安装 RxWeb:

```
npm install @rxweb/reactive-form-validators
```

现在让我们创建表单模型。我们的示例场景将是一个用户注册页面，因此让我们创建一个注册模型:

```
ng generate class models/resgister --skiptests=true
```

让我们用每个属性的 RxWeb 验证器来设置我们的注册模型:

现在让我们一起努力。首先，让我们在应用程序组件的 init 上创建表单组:

让我们将控件应用于表单输入:

我们有一个合适的表单和所有的验证逻辑设置，但我们仍然需要一种方法来监听每当用户与表单元素交互时由我们的反应式表单控件抛出的所有错误。

首先，让我们生成一个单独的角度模块，以保持我们所有的代码与我们的验证引擎相关。这样，我们可以保持解决方案整洁有序:

```
ng generate module validations-module
```

现在，让我们创建一个指令来处理来自控件的错误:

```
ng generate directive validations-module/directives/control-errors/control-errors
```

我们的指令代码:

如您所见，我们正在处理三个可观察到的问题:

*   当用户触摸表单控件时触发的触摸事件
*   用户键入数据时触发的值更改事件
*   当 formControl 验证状态改变时触发的控件状态改变(通常在我们操作验证状态时使用，因为验证是在后端完成的，我们希望用适当的错误消息来呈现)

为了让每个字段有一个合适的名称显示在错误消息上，我们使用一个指令输入来接收一个翻译键。之后，我们将把它和一般的错误信息一起插入。

我们还完成了由基于 destroySubject 的 merge 操作符创建的内部可观察对象，这样我们避免了由未完成的可观察对象导致的内存泄漏。

目前，我们正在监听表单控件抛出的所有验证错误。我们仍然需要获取错误，找到相应的错误消息并显示给用户。为了做到这一点，让我们创建一个静态助手类来处理这个问题:

```
ng generate class validations-module/helpers/error-helper --skipTests=true
```

正如您所看到的，这个 helper 类只有一个公共方法(getError ),它接收两个参数字段名转换和 formControl 错误。它返回 AutoError 或 DefaultError 的实例。自动错误将由我们的验证引擎处理，并将自动向用户显示正确的错误消息。我们的验证引擎不会处理 DefaultErrors，它将像普通的反应式表单验证一样工作。为了显示错误信息，我们需要实现这样的代码。当我们需要以一种定制的方式显示错误时，比如在一个对话框或一条消息中，这是非常有用的。

当处理 AutoErrors 时，如果 error 键匹配一个错误，那么我们创建并返回一个 AutoError 对象的新实例。否则，我们检查它是否已经是 AutoError 或 DefaultError 的实例，并返回它以便以后显示。如果错误既不是 AutoError 也不是 DefaultError 类型，我们抛出一个异常。

助手可以接收并显示由验证器传递的自定义错误消息。我们已经在注册模型的电子邮件验证器属性上设置了一个。

由于 ngx-translate 允许在翻译中插入值，因此该助手还处理从验证错误对象中获取值的逻辑，这些值随后将被插入到消息中。

现在我们来听听当用户触摸或输入一些文本时表单控件元素内的交互。如果它只是简单地提交表单而没有任何交互呢？因此，让我们创建另一个指令来处理这个用例:

```
ng generate directive validations-module/directives/form-submit/form-submit --skipTests=true
```

这里我们使用 shareReplay 操作符，每个表单只有一个事件侦听器，而不是每个控件一个。

我们已经实现了所有的逻辑来观察我们所有的表单控件验证、表单提交动作并生成正确的错误对象和翻译，酷！但是我们仍然需要挑选自动错误，并用一些漂亮的外观和感觉向用户显示正确的错误消息，所以让我们为此创建一个组件:

```
ng generate component validations-module/components/auto-error --skipTests=true
```

如您所见，该组件只处理翻译成用户所选语言的 AutoError 对象的表示。如果您试图在表单完成过程中更改语言，它也会更新错误消息。

现在我们有适当的组件来呈现我们的错误。因此，让我们实现一些代码来动态地创建这个组件并将其注入 DOM。让我们改进我们的控制错误指令:

现在我们的引擎可以使用了。让我们启动我们的应用程序，测试我们的表单验证！

在这里，您可以使用一些额外的自定义验证器来检查完整的工作示例，并使用 window alert 显示一个默认错误:

[](https://stackblitz.com/github/jgomesmv/AngularValidationsEngine) [## 角度验证引擎

### 完整示例

stackblitz.com](https://stackblitz.com/github/jgomesmv/AngularValidationsEngine) 

> 归功于以下人员的出色工作和帮助:
> 
> [https://net basal . com/make-your-angular-forms-error-messages-magically-appear-1e 32350 b7fa 5](https://netbasal.com/make-your-angular-forms-error-messages-magically-appear-1e32350b7fa5)
> 
> [https://medium . com/@ oojhaajay/new-way-to-validate-the-angular-reactive-form-2 C4 Fe 4 f 13373](https://medium.com/@oojhaajay/new-way-to-validate-the-angular-reactive-form-2c4fe4f13373)
> 
> 【https://github.com/ngx-translate/core 