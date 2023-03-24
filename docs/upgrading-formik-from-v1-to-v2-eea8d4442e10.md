# 将 Formik 从 v1 升级到 v2

> 原文：<https://levelup.gitconnected.com/upgrading-formik-from-v1-to-v2-eea8d4442e10>

![](img/f836b7bbbb45b7e6904ef31c8a09b344.png)

[Formik](https://formik.org/) 是 React 和 React Native 最流行的开源表单库，可以帮助您解决 3 个最烦人的部分:

1.  获取表单状态内外的值
2.  验证和错误消息
3.  处理表单提交

它为输入验证、格式化、屏蔽、数组和错误处理提供了久经考验的解决方案。这意味着你花更少的时间编写表单代码，花更多的时间构建下一个大东西。

Formik 版本 2 构建在 React 挂钩之上。从 v1 升级或迁移到 v2 的基本要求是您必须使用 React 16.8.x 或更高版本。此外，如果您使用的是 TypeScript，则必须是 TypeScript 3 或更高版本。

## **突变变化**

1.  `setError`

v2 中删除了此方法。你可以使用`[setStatus](https://formik.org/docs/api/formik#setstatus-status-any--void)(status).` 来代替它，它的工作原理是一样的。注意:这是/不是仍然存在的`setErrors`(复数)。

2.`validate`

正如你可能知道的，你可以从`validate`返回一个验证错误的承诺。在 v1 中，这个承诺是被解决还是被拒绝并不重要，因为在这两种情况下，承诺的有效负载都被解释为验证错误。在 v2 中，拒绝将被解释为实际的异常，它不会更新表单错误状态。任何返回拒绝的错误承诺的验证函数都需要进行调整，以返回解决的错误承诺。

3.`ref`

您不能再使用`ref`道具将 ref 附加到 Formik。然而，你仍然可以使用道具`innerRef`来解决这个问题。

*v1*

*v2*

如果你使用 React 钩子，那么你可以简单地使用`useRef`

```
const formikRef = useRef();...<Formik innerRef={formikRef} />
```

4.`isValid`

该属性不再考虑`dirty`的值。这意味着如果你想在表单不是`dirty`的时候禁用一个提交按钮(比如，在第一次呈现的时候，值没有改变)，你必须显式地检查它。

5.`resetForm`

Formik v2 推出了更多初始状态的新道具:`initialErrors`、`initialTouched`、`initialStatus`。所以，`resetForm`的签名变了。而不是可选地只接受表单的下一个初始值。它现在可以选择接受 Formik 的部分下一个初始状态。

*v1*

*v2*

上面的大部分突破性变化并不常用，这意味着这不会影响许多项目。

## 反对警告

所有的`render`道具都被弃用，并有控制台警告。

对于`<Field>`、`<FastField>`、`<Formik>`、`<FieldArray>`，`render`道具已被弃用，并带有警告，因为它将在未来版本中被移除。相反，使用子回调函数。

*v1*

*v2*

## 类型脚本更改

1.  `FormikActions`

`FormikActions`已被重命名为`FormikHelpers`，导入或别名该类型应该是一个简单的更改。

```
import { FormikHelpers as FormikActions } from 'formik';
```

2.`FieldProps`

`FieldProps`现在接受两个泛型类型参数。两个参数都是可选的，但是`FormValues`已经从第一个参数移动到第二个参数。

*v1*

```
type Props = FieldProps<FormValues>;
```

*v2*

```
type Props = FieldProps<FieldValue, FormValues>;
```

新版本 2 中增加的新特性，可以在这里查看[。](https://formik.org/docs/migrating-v2#whats-new)