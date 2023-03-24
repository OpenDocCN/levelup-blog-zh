# angular 中的自定义表单验证

> 原文：<https://levelup.gitconnected.com/custom-form-validation-in-angular-91c2af5d6e6>

![](img/020f7f4420da6d89e2cf23f7da26c679.png)

马克斯维尔·纳尔逊在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄的照片

今天我们将学习 angular 中的自定义表单验证。

Angular 为我们提供了一些内置的验证器，但是它不足以满足我们所有的用例。有时我们需要创建自己的自定义验证器。

# 自定义验证器的基础

下面是自定义验证函数的基本结构。

```
export function yourCustomFunctionName(): **ValidatorFn** {
  return (control: **AbstractControl** | form: **FormGroup**): **ValidationErrors** | **null** => {
    return condition ? { key: value } : null
  }
}
```

这里可以看到，`yourCustomFunctionName`是一个返回验证器函数的函数。

如果验证用于字段级，那么验证器函数接受一个角度`AbstractControl`对象，如果用于表单级，那么接受一个`FormGroup`对象。

我们将在本文后面看到这两种类型的验证。

如果控制值有效，将返回`null`，如果有错误，将返回`validation error object`。

# 实现自定义验证器

现在你有了基本的概念。所以，事不宜迟，让我们实现我们的自定义表单验证。

在这个例子中，我们将使用反应式。

```
<form [formGroup]="validationForm">
  <div>
   <label>Name</label>
   <input formControlName="name">
  </div> <div>
   <label>From</label>
   <input type="number" formControlName="from">
  </div> <div>
   <label>To</label>
   <input type="number" formControlName="to">
  </div> <button type="submit" class="btn btn-primary">Submit</button></form>
```

这里我们可以看到一个简单的表单，有三个字段，一个用于名称输入，另外两个用于范围输入。

这是这个模板的组件类。

```
[@Component](http://twitter.com/Component)({
  selector: 'my-app',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent   {

  validationForm = this.fb.group({
    name: ['', [Validators.required, **nameValidation()**]],
    from: ['', [Validators.required]],
    to: ['', [Validators.required]]
  }, {
    validators: [**rangeValidation()**]
  }); constructor(private fb: FormBuilder) {} get name() { return this.validationForm.get('name'); }}
```

您可以在这里看到，我们应用了两个自定义验证。一个在字段级别(**名称验证**)，另一个在表单级别(**范围验证**)。

您还可以注意到我们的`name`控件有一个 get 方法。我们稍后在组件模板中使用它来检查我们的验证。

如果您不清楚何时使用字段级验证，何时使用表单级验证。那么就很简单了，如果你想根据输入本身的值来验证输入，那么你可以使用字段级。但是如果您的验证是基于表单的另一个字段，那么您可以使用表单级验证。

让我们分别实现这两个验证。

## 字段级自定义验证器

为了这篇文章，我们将实现非常简单的验证。

名称字段值中始终包含单词“ **angular** ”。

```
export function nameValidation(): **ValidatorFn** { return (**control: AbstractControl**): ValidationErrors | null => { const isValid = /angular/gm.test(control.value); return isValid ? **null** : **{ name: true }**; };}
```

让我们了解一下这是怎么回事。这里我们可以看到我们的`nameValidator()`函数返回了`ValidatorFn`。它本身也不是一个验证函数。

验证器函数接受一个控制对象，如果控制值有效，则返回`null`，否则返回一个`ValidationErrors`对象。

## 表单级自定义验证器

现在让我们实现表单级别的自定义验证功能。为此，我们也将实现简单的验证。

检查`to`值是否总是大于`from`值。

```
export function rangeValidation(): **ValidatorFn** { return (**form: FormGroup**): ValidationErrors | null => { const from = Number(form.get('from')?.value);
   const to = Number(form.get('to')?.value);

    return  from >= to ? {range: true} : null; };}
```

可以看到函数结构和上面的函数差不多。但是这里主要的区别是验证函数把`FormGroup`对象作为一个参数。

这里我们检查`from`值是否大于或等于`to`值。这是一个返回错误的对象，否则返回 null。

# 如何在模板中显示错误？

现在我们学习了如何创建我们自己的自定义验证。让我们在模板中使用这种验证。

为了简洁起见，我将只检查我们的自定义验证，不检查输入触摸状态。

**检查字段级自定义验证**

```
<div>
 <label>Name</label>
 <input class="form-control" formControlName="name">
 **<div *ngIf="name.errors?.name">Please enter valid name</div>**
</div>
```

**检查表单级自定义验证**

```
<div class="form-group">
<label>To</label>
<input type="number" class="form-control" formControlName="to">
**<div *ngIf="validationForm.errors?.range">Please enter valid range</div>**
</div>
```

# 在 stackblitz 玩跑步 app

[](https://stackblitz.com/edit/angular-ivy-2ywlef?embed=1&file=src/app/app.component.ts&hideExplorer=1) [## 角度堆栈中的自定义表单验证

### 关于如何在 angular 中实现自定义窗体验证的快速演示。

stackblitz.com](https://stackblitz.com/edit/angular-ivy-2ywlef?embed=1&file=src/app/app.component.ts&hideExplorer=1) 

# 源代码

[](https://github.com/arpanpatel/Custom-Form-Validation-In-Angular) [## github-arpanpatel/自定义表单-角度验证:使用 StackBlitz ⚡️创建

### 由斯塔克布里兹·⚡️.创作有助于 arpanpatel/自定义形式验证的角度发展，创造一个…

github.com](https://github.com/arpanpatel/Custom-Form-Validation-In-Angular) 

# 我们做到了。