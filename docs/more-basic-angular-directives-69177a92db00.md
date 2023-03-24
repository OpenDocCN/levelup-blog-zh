# 更多基本角度指令

> 原文：<https://levelup.gitconnected.com/more-basic-angular-directives-69177a92db00>

![](img/4892521c884f37664f34b770d612b8e2.png)

鲍里斯·斯莫克罗维奇在 Unsplash 的照片

Angular 是 Google 制作的一个流行的前端框架。像其他流行的前端框架一样，它使用基于组件的架构来构建应用程序。

在本文中，我们将看看`NgModel`、`NgIf`和`NgFor`角度指令。

# NgModel

我们可以使用`ngModel`进行双向数据绑定。在我们编写值访问器之前，它不能用于非形式的本机元素或第三方自定义元素。

`[(ngModel)]`是`[ngModel]`和`(ngModelChange)`的简写。

例如，以下内容:

`app.module.ts`:

```
import { BrowserModule } from "@angular/platform-browser";
import { NgModule } from "@angular/core";
import { FormsModule } from "@angular/forms";import { AppComponent } from "./app.component";@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, FormsModule],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

`app.component.ts`:

```
import { Component } from "@angular/core";@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  name: string;
}
```

`app.component.html`:

```
<input [(ngModel)]="name" />
<p>{{name}}</p>
```

与以下内容相同:

```
<input [ngModel]="name" (ngModelChange)="name = $event" />
<p>{{name}}</p>
```

其余的都是一样的。

# 内置结构指令

结构指令负责呈现 HTML 布局。它们通过添加、删除和操作它们所附加的宿主元素来重塑 DOM 结构。

Angular 有以下结构指令:

*   `NgIf` —有条件地从模板创建或销毁子视图
*   `NgFor` —为列表中的每个项目重复一个节点
*   `NgSwitch` —一组在选项之间切换的指令

# NgIf

我们可以通过对一个主机元素应用`NgIf`指令来添加或删除 DOM 中的一个元素。

它用于有条件地显示项目。例如，我们可以写:

`app.component.ts`:

```
import { Component } from "@angular/core";@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  showFoo: boolean = false;
}
```

`app.component.html`:

```
<button (click)="showFoo = !showFoo">toggle</button>
<p *ngIf="showFoo">foo</p>
```

在上面的代码中，我们在`AppComponent`中有`showFoo`字段。

然后在`app.component.html`中，我们有一个在`true`和`false`之间切换`showFoo`的按钮和一个 p 元素，由于`*ngIf`指令，该元素仅在`showFoo`为`true`时显示。

当我们点击按钮时，我们会看到单词“foo”被显示和隐藏。

## 显示/隐藏与`NgIf`

我们还可以显示和隐藏具有`hidden`类或`display`样式的元素。

这些和`NgIf`的区别在于，如果我们设置的表达式返回一个错误的值，那么`NgIf`会从 DOM 中删除这个条目。

`hidden`和`display`让它们在 DOM 中，不管它们是否被隐藏。

例如，我们可以通过编写以下代码来使用`hidden`类:

```
<div [class.hidden]="isGreen">green</div>
```

我们也可以如下使用`display`样式:

```
<div [style.display]="isGreen? 'none' : 'block'">green</div>
```

## 警惕空值

我们可以使用`NgIf`来防范空值。

例如，在下面的例子中，`customer`可能是`null`或`undefined`，所以我们可以这样写:

```
<div *ngIf="customer">Hello, {{customer.name}}</div>
```

然后，如果`customer`是`null`或`undefined`，我们从 DOM 中移除 div。

# NgFor

`NgFor`用于在屏幕上显示项目列表。我们可以定义一个单独的 HTML 块，然后使用`NgFor`对列表上的项目重复它。

例如，我们可以如下使用它:

`app.component.ts`:

```
import { Component } from "@angular/core";@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  persons = [{ name: "John" }, { name: "Jane" }, { name: "Mary" }];
}
```

`app.component.html`:

```
<div *ngFor="let p of persons">{{p.name}}</div>
```

在上面的代码中，我们在`AppComponent`中有一个`persons`字段，其中的对象在每个条目中都有`name`属性。

然后我们使用`NgFor`遍历`persons`的每个条目，然后显示每个条目的`name`属性值。

`NgFor`也可用于重复成分元素。

例如，我们可以写:

`app.module.ts`:

```
import { BrowserModule } from "@angular/platform-browser";
import { NgModule } from "@angular/core";
import { FormsModule } from "@angular/forms";import { AppComponent } from "./app.component";
import { PersonComponent } from "./person.component";@NgModule({
  declarations: [AppComponent, PersonComponent],
  imports: [BrowserModule, FormsModule],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

`person.component.ts`:

```
import { Component, Input } from "@angular/core";@Component({
  selector: "app-person",
  templateUrl: "./person.component.html"
})
export class PersonComponent {
  @Input() person;
}
```

`person.component.html`:

```
<div>{{person.name}}</div>
```

`app.component.ts`:

```
import { Component } from "@angular/core";@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  persons = [{ name: "John" }, { name: "Jane" }, { name: "Mary" }];
}
```

`app.component.html`:

```
<app-person *ngFor="let p of persons" [person]="p"></app-person>
```

在上面的代码中，我们创建了一个新的`PersonComponent`组件，它接受一个`person`属性，并在`person.component.html`中显示`person`的`name`属性值。

然后在`app.component.ts`中，我们有了之前的`persons`数组。

我们显示`app-person`元素，它是带有`*ngFor`的`PersonComponent`组件，并传入`p`作为`persons`的值。

`*ngFor`是一个 microsyntax，是 Angular 解读的语言。

![](img/cd4e5604d4ba33ecc8e573d6141dad18.png)

文森特·范·扎林格在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## `*ngFor`同`index`

通过将`index`设置为模板中的一个变量，我们可以得到被循环的元素的索引。

例如，我们可以这样写:

`app.component.ts`:

```
import { Component } from "@angular/core";@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  persons = [{ name: "John" }, { name: "Jane" }, { name: "Mary" }];
}
```

`app.component.html`:

```
<div *ngFor="let p of persons; let i = index">
  {{i}} - {{p.name}}
</div>
```

在上面的代码中，`i`被设置为`app.componet.html`中的`index`，`index`是被循环的元素的索引。

因此，我们应该看到:

```
0 - John1 - Jane2 - Mary
```

显示在屏幕上。

## *ngFor with trackBy

我们可以使用`trackBy`来提高大型列表的操作效率。它防止列表在每次更改时都被从头开始重新呈现。

要使用它，我们必须向组件添加一个方法来返回应该跟踪的内容。

例如，我们可以如下使用它:

`app.component.ts`:

```
import { Component } from "@angular/core";@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  persons = [
    { id: 1, name: "John" },
    { id: 2, name: "Jane" },
    { id: 3, name: "Mary" }
  ]; trackById(person) {
    return person.id;
  }
}
```

`app.component.html`:

```
<div *ngFor="let p of persons; trackBy: trackById">
  {{p.name}}
</div>
```

在上面的代码中，我们给`AppComponent`添加了一个`trackById`方法，它接受一个`person`对象，我们用`*ngFor`作为参数来循环这个对象。然后我们可以在方法中从`person`返回`id`属性。

然后在`app.component.html`中，将`trackBy`属性集添加到我们的`trackById`方法中。

# 结论

`NgModel`让我们在模板和组件之间绑定值。

`NgIf`根据条件，通过在 DOM 中添加或删除项目来有条件地显示项目。

从 iterable 对象中呈现一个项目列表。