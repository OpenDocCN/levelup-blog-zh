# 角度中的交叉字段验证

> 原文：<https://levelup.gitconnected.com/cross-field-validation-in-angular-588fbfdea96d>

> **前提:**[角无功形式](https://angular.io/guide/reactive-forms)中的基础知识

![](img/2541f2ae6d9fb4768233fb0d1f69c2c3.png)

Angular 提供`FormControl`验证，以确定表单字段是否有效。这些表单控件验证有助于使用一些预先确定的规则来确定表单域的有效性。这些规则纯粹是专门为该表单控件定义的。定义的验证器函数将特定表单控件的值与预定规则列表进行比较，以确定特定字段的有效性。

考虑这样一种情况，其中一个表单域的有效性不仅取决于特定的表单控件，还取决于另一个域的值。在这个场景中，我们不能简单地将验证器定义到那个特定的`FormControl`级别，因为那个表单控件的有效性也依赖于另一个表单控件的值。

> 基于另一个`FormContol`确定一个`FormControl`的有效性被称为交叉字段验证。

让我们考虑一个场景，我们有一个用户注册表单，用户需要输入密码以及确认密码。密码和确认密码可能都有一些预定义的规则，如`min length`、`max length`、`pattern`等。除了这些规则，还有一个额外的规则需要满足，即密码和确认密码字段应该相同。让我们看看如何在 Angular 中进行这种多字段验证。

在这个场景中，我们可以将验证器函数定义到`FormGroup`级别，而不是`FormControl`级别。在`FormGroup`级别中定义验证器将验证器功能控制权交给表单组中的所有`FormControl`。

为了给`[FormGroup](https://angular.io/guide/form-validation#adding-cross-validation-to-reactive-forms)`定义一个验证器函数，我们可以在创建验证器的同时将它作为第二个参数传递给`FormGroup`。

那么我们来看看我们的`FormGroup`将如何定义。

```
ngOnInit(): void {
    this.registerForm = this.formBuilder.group({
        passsword: ['', 
            [Validators.required,
             Validators.minLength(3),
             Validators.maxLength(10)]],
        confirm_passsword: ['',
            [Validators.required,
             Validators.minLength(3),
             Validators.maxLength(10)]],
    }, {
        validator:
            CustomValidator.passwordValidator(
                'passsword', 'confirm_passsword'
            )
    });
}
```

这里我们添加了两组验证器。对于`FormControls`，我们增加了`required`、`minLength`和`maxLength`验证器，对于`FormGroup`，我们增加了一个自定义验证器，即`passwordValidator`。此外，我们将表单控件名称作为参数发送给验证器函数，只是为了具有一些动态性。

让我们看看我们的自定义验证器函数。

```
export class CustomValidator {
    static passwordValidator(formCtrlOne, formCtrlTwo) {
        return (fg: FormGroup) => {
            // Select the two form conrols from the form group
            // on which the comparison is to be performed.
            const fieldOne = fg.controls[formCtrlOne];
            const fieldTwo = fg.controls[formCtrlTwo];
            if (fieldOne && fieldTwo) {
                if(fieldOne.value || fieldTwo.value) {
                    if (fieldOne.value !== fieldTwo.value) {
                        // Use set error methods like this to append
                        // 'password_mismatch' error
                        // with the existing errors.
                        fieldOne.setErrors({
                            ...fieldOne.errors,
                            ...{ 'password_mismatch': true }
                        });
                        fieldTwo.setErrors({
                            ...fieldTwo.errors,
                            ...{ 'password_mismatch': true }
                        });
                    } else {
                        let fieldOneError = {...fieldOne.errors};
                        delete(fieldOneError['password_mismatch']);
                        // If there is no keys in the error object,
                        // it means that the control has no error
                        // In that case set the object as null
                        // Setting null as error to a form field
                        // makes the form control valid
                        fieldOneError =
                            Object.keys(fieldOneError).length > 0 ?
                                fieldOneError : null; 
                        fieldOne.setErrors(fieldOneError);

                        let fieldTwoError = {...fieldTwo.errors};
                        delete(fieldTwoError['password_mismatch']);
                        fieldTwoError =
                            Object.keys(fieldTwoError).length > 0 ?
                                fieldTwoError : null; 
                        fieldTwo.setErrors(fieldTwoError);
                    }
                }
            }
        }
    }
}
```

在`CustomValidator`函数中，我们将接收表单控件名称作为参数，通过使用这些名称，我们可以简单地使用`formgroup.controls[controlname]`从`FormGroup`中选择`FormControl`。这里我们期望两个表单控件具有相同的值，作为自定义验证。

如果表单控件值不匹配，我们可以通过添加自定义错误`password_mismatch`来更新表单控件。我们可以通过`formfield.setErrors({…formfield.errors, …{ ‘password_mismatch’: true }})`将这个自定义错误添加到现有的错误对象中，这将保留现有的错误，同时添加新的错误。

为了在自定义验证满足要求时删除自定义错误，我们将选择当前的错误对象并从中删除自定义错误对象，并将结果对象设置为表单字段本身的错误对象。这将有助于保留除自定义错误之外的所有现有错误。

**请注意**，如果删除自定义错误后的错误对象没有任何属性，则表单域有效。在这种情况下，我们需要手动将错误设置为`null`，否则将空对象设置为`FormControl`的错误将使`FormControl`字段本身保持无效状态。

既然你已经了解了交叉验证，你可能会想尝试一下。

> 请在这里找到工作的小提琴
> 
> [https://stackblitz.com/edit/angularcrossfieldvalidation](https://stackblitz.com/edit/angularcrossfieldvalidation)
> 
> 或者你可以检查我的 github 库
> [https://github.com/devKR2911/cross-field-validation](https://github.com/devKR2911/cross-field-validation)

快乐编码。:)

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)