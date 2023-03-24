# 角度指令—开关、输入和输出

> 原文：<https://levelup.gitconnected.com/angular-directives-ngswitch-input-and-output-ee236d6cd29e>

![](img/d7d6f708eabc89ced97d80aa1236be26.png)

照片由 [Sneha](https://unsplash.com/@sne5ha?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Angular 是 Google 制作的一个流行的前端框架。像其他流行的前端框架一样，它使用基于组件的架构来构建应用程序。

在本文中，我们将看看`NgSwitch`指令、模板变量以及`Input`和`Ouput`语法。

# `NgSwitch`指令

我们可以使用`NgSwitch`指令来显示可能选项列表中的一项。

例如，我们可以如下使用它:

`app.component.ts`:

```
import { Component } from "@angular/core";@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  name = "foo";
}
```

`app.component.html`:

```
<div [ngSwitch]="name">
  <p *ngSwitchCase="'foo'">foo</p>
  <p *ngSwitchCase="'bar'">bar</p>
  <p *ngSwitchCase="'baz'">baz</p>
</div>
```

在上面的代码中，我们将`AppComponent`中的`name`字段设置为`'foo'`。

然后在`app.component.html`中，我们将`ngSwitch`设置为`name`，以便它检查`AppComponent`中`name`的值，以获得要显示的内容。

我们有多个不同值的`*ngSwitchCase`，如果`name`是`'foo'`，我们显示‘foo’，如果`name`是`'bar'`，我们显示‘bar’，如果`name`是`'baz'`，我们显示‘baz’。

由于`name`是`‘foo’`，我们显示‘foo’。

`*ngSwitchCase`支持本地元素和组件。

# 模板引用变量(#var)

模板引用变量通常是对模板中 DOM 元素的引用。它也可以指指令、元素。`TemplateRef`或一个 web 组件。

例如，我们可以如下使用它:

`app.component.ts`:

```
import { Component } from "@angular/core";@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  call(phone) {
    alert(`Calling ${phone.value}`);
  }
}
```

`app.component.html`:

```
<input #phone placeholder="phone" />
<button (click)="call(phone)">Call</button>
```

在上面的代码中，我们添加了一个`call`方法，它将`phone`元素作为参数，并显示一个警告，其中的值被键入到`phone`输入中。

然后我们在`app.component.html`中添加了`#phone`来引用输入元素。此外，我们添加了一个按钮来调用带有`phone`输入引用的`call`方法，这样我们就可以在警报中显示值。

# `NgForm`

`NgForm`指令改变行为并将模板变量的值设置为其他值。

有了它，我们可以在表单外部引用表单内部的变量。

例如，我们可以如下使用`NgForm`:

`app.component.ts`:

```
import { Component } from "@angular/core";@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  onSubmit(itemForm) {
    if (itemForm.invalid) {
      return;
    }
    alert("form submitted");
  }
}
```

`app.component.html`:

```
<form #itemForm="ngForm" (ngSubmit)="onSubmit(itemForm)">
  <label>Name <input name="name" ngModel required /> </label>
  <button type="submit">Submit</button>
</form><div [hidden]="itemForm.form.valid">
  <p>Missing name</p>
</div>
```

在上面的代码中，我们在`AppComponent`中使用了`onSubmit`方法，它采用了`itemForm`引用。然后我们可以在提交之前用它来检查组件的有效性。

然后在`app.component.html`中，我们通过使用`ngForm`指令创建了`#itemForm`变量，这样我们就可以在底部`div`和传入`itemForm`的`onSubmit`中引用表单并检查其有效性。

## 替代语法

我们可以用`ref-`前缀代替`#`，所以我们可以写:

```
<input ref-phone placeholder="phone" />
<button (click)="call(phone)">Call</button>
```

# `@Input()`和`@Output()`属性

`@Input`和`@Output`允许 Angular 在父上下文和子指令和组件之间共享数据。

## 如何使用@Input()

例如，我们可以编写以下代码来使用`@Input`将数据从父组件传递给子组件:

`app.module.ts`:

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { PersonComponent } from './person/person.component';@NgModule({
  declarations: [
    AppComponent,
    PersonComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

`app.component.ts`:

```
import { Component } from '@angular/core';@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  persons = [{ name: 'Jane' }, { name: 'John' }, { name: 'Mary' }];
}
```

`app.component.html`:

```
<app-person *ngFor="let p of persons" [person]='p'></app-person>
```

`person.component.ts`:

```
import { Component, OnInit, Input } from '@angular/core';@Component({
  selector: 'app-person',
  templateUrl: './person.component.html',
  styleUrls: ['./person.component.css']
})
export class PersonComponent {
  @Input() person;
}
```

`person.component.html`:

```
<div>{{person.name}}</div>
```

在上面的代码中，我们创建了一个`PersonComponent`组件，然后将它包含在`AppModule`的`declarations`数组中。

然后在`AppComponent`中，我们有:

```
persons = [{ name: 'Jane' }, { name: 'John' }, { name: 'Mary' }];
```

和`app.component.html`中的`*ngFor`循环遍历项目并渲染`PersonComponent`。

我们将`p`作为`person`属性的值传入，然后它将是`PersonComponent`中`person`的值。

最后，我们在`person.component.html`中显示`person`的`name`值。

最后，我们应该看到:

```
JaneJohnMary
```

显示在屏幕上。

![](img/e9a5672797e8dc413684db4f7795f0a9.png)

简·梅乌斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 如何使用@Output()

我们可以在子组件或指令中使用`@Output`装饰器将数据从子组件传递到父组件。

例如，我们可以如下使用它:

`app.module.ts`:

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { PersonComponent } from './person/person.component';@NgModule({
  declarations: [
    AppComponent,
    PersonComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

`app.component.ts`:

```
import { Component } from '@angular/core';@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  emittedName: string;
}
```

`app.component.html`:

```
<app-person (name)='emittedName = $event'></app-person>
<p>{{emittedName}}</p>
```

`person.component.ts`:

```
import { Component, Output, EventEmitter } from '@angular/core';@Component({
  selector: 'app-person',
  templateUrl: './person.component.html',
  styleUrls: ['./person.component.css']
})
export class PersonComponent {
  @Output() name = new EventEmitter<string>();
  emitName() {
    this.name.emit('Jane');
  }
}
```

`person.component.html`:

```
<button (click)='emitName()'>Get Name</button>
```

在上面的代码中，我们在`person.component.html`中有一个按钮，它调用`PersonComponent`的`emitName`方法将字符串`'Jane'`发送给带有`EventEmitter`的父组件。

然后在父组件`AppComponent`中，我们有:

```
<app-person (name)='emittedName = $event'></app-person>
```

在`app.component.html`中。

`$event`具有从`name`发射器发射的值。然后我们将其设置为`emittedName`并显示。

# @Input()和@Output()一起

我们可以一起使用它们。

例如，我们可以写:

```
<app-input-output [item]="currentItem" (deleteRequest)="deleteItem($event)"></app-input-output>
```

在上面的代码中，我们将`currentItem`传递给了`item`组件的`app-input-output`属性。

从`app-input-output`发出`deleteRequest`，然后在发出`deleteRequest`时用从子进程传递的内容调用`deleteItem`方法。

我们可以向`Input`和`Output`传递一个名字来分别改变它们的名字，然后我们可以在模板中引用它们。

# 结论

我们可以使用`NgSwitch`根据组件中某个东西的值来显示一个项目。

模板变量让我们在模板中引用 DOM 元素并访问它的属性。

使用`Input`和`Output`，我们可以将数据传递给子组件或指令，并将项目分别从子组件传递给父组件。