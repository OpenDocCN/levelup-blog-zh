# 角形 14 类表格的 4 个问题及解决方法

> 原文：<https://levelup.gitconnected.com/why-new-angular-14-typed-forms-isnt-good-and-how-to-fix-it-1ffebf193df>

# 1.我们无法从模型中获取控件类型

你不能从你的模型类型中得到`FormGroup`或`FormArray`。但是在现实世界的形式中，与模型绑定。例如，您应该能够从模型中构造控件的类型。

您定义了一些模型:

```
interface Model {
  a: string;
  b: number;
}
```

然后定义类型:

```
type Form: SomeMagicType<Model>;
```

然后你会在我们的`Form`中看到类似的东西:

```
FormGroup<{
  a: FormControl<string>;
  b: FormControl<number>;
}>
```

我知道你可能需要管理你想要的:`FormGroup`或`FormControl`或`FormArray`在某些情况下，它是可能实现的，我会在“让我们修复它”一章中告诉你如何实现。

# 2.fb.group

假设您有表单类型，但是`fb.group<Form>(...)`不支持数组语法，例如:

您有一些表单类型:

```
type Form = FormGroup<{
  a: FormControl<string>;
  b: FormControl<number>;
}>
```

然后你传给 fb.group:

```
const fb = new FormBuilder()const form: Form = fb.group<Form['controls']>({
  a: ['test'],
  b: [42, Validators.required]
})
```

但是如果你尝试了，就不行了，因为 fb.group 不支持那个语法。所以我们不能修复那个 bug，它站在有棱角的开发者一边

# 3.您应该为表单编写无用的样板代码

如果你需要一些表单，并且需要在它初始化之前获取表单类型，那么你应该为它定义类型并定义类似的表单实例。这是样板代码，如果你有很多表格，这将是非常无聊的，当然，当你键入大量无聊的代码时，你可能会犯错误。示例:

你有一个模型:

```
interface User {
  firstName: string;
  lastName: string;
  birthday: Date;
  nickname: string;
}
```

然后你应该定义你的类型:

```
type UserForm = FormGroup<{
  firstName: FormControl<string | null>;
  lastName: FormControl<string | null>;
  birthday: FormControl<Date | null>;
  nickname: FormControl<string | null>;
}>
```

然后，您应该定义您的表单:

```
const userForm: UserForm = fb.group({
  firstName: fb.control<string | null>(null),
  lastName: fb.control<string | null>(null),
  birthday: fb.control<Date | null>(null),
  nickname: fb.control<string | null>(null),
})
```

# 4.样板代码对模型一无所知

从上一个问题可以看出，`UserForm`对`User`接口一无所知。

如果我们的模型改变了，你应该修正两个地方(`UserForm`类型和`userForm` `const`)。

此外，您只会在使用 setValue 或 patch 之类的地方看到错误。这些都是间接错误，对开发者没有好处。

# 让我们修理它

所以我们需要一种魔法来解决我们的问题。但是它看起来像什么？

假设我们有一个复杂的模型:

```
interface Model {
    a: number;
    b: string[];
    c: {
        d: {
            e: number[];
        }
    }
}
```

我们需要递归地将我们的`Model`变成`FormGroup`、`FormArray`和`FormControl`的集合。

在 TypeScript 中，我们有`[Mapped Types](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html)`，类型可以是递归的。我们还需要`[Conditional Types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)`来管理我们想要推断的内容。

但是我们怎么知道我们想要推断什么呢？有些情况下`Model.b`应该是`FormControl`而不是`FormArray`或者`Model.c`应该是`FormGroup`而不是`FormControl`，我们该如何描述？

我们需要注释来定义嵌套对象的输出类型。

假设我们的类型应该是这样的:

```
type FormModel<TModel, TAnnotation> = ...
```

我们有一些对象、数组和原语，假设`Model.b`应该是`FormArray`,`Model.c.d`应该是`FormGroup`。如何编写可以传递给类型参数的注释？应该是一个类型吧！好，我们可以用什么来描述类型？我们可以使用对象、数组和常量，这就是我们所需要的！

所以我们可以这样写我们的注释:

```
type Annotation = { b: 'array', c: { d: 'group' } }
```

并且是正确的 TypeScript 类型。我们可以利用这一点。

所以我们使用`[Mapped Types](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html)`和`[Conditional Types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)`将`Model`类型映射到一组控件。我们可以通过特殊的`Annotation`来告诉我们的`FormModel`我们需要推断什么控件。然后我们基于我们的`Model`得到递归构造的`FormGroup`类型。

这一章只是对主要思想的总结。如果你愿意，你可以深入到现成的解决方案中，阅读我的库中的文档、测试和代码

[](https://github.com/iamguid/ngx-mf) [## GitHub - iamguid/ngx-mf:将模型类型绑定到 angular FormGroup 类型

### ngx-mf 是递归推断角度 FormGroup、FormArray 或 FormControl 类型的零依赖性类型脚本集…

github.com](https://github.com/iamguid/ngx-mf) 

还有，你可以在 https://stackblitz.com/edit/angular-ngx-mf 上试试

感谢您的阅读，祝您愉快！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)
*   🚀👉 [**软件工程师的顶级工作**](https://jobs.levelup.dev/jobs?utm_source=pub&utm_medium=post)