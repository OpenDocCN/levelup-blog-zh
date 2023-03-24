# 角度简介:显示数据

> 原文：<https://levelup.gitconnected.com/introduction-to-angular-display-data-aea0eadefcd6>

![](img/362f3acf58fb45f104d45af14e4038d9.png)

安德烈斯·努内斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Angular 是 Google 创建的一个流行的前端框架。像其他流行的前端框架一样，它使用基于组件的架构来构建应用程序。

在本文中，我们将了解如何在 Angular 应用程序中显示数据。

# 显示数据

我们可以通过将模板数据绑定到模板来显示数据。

为此，我们可以将 JavaScript 表达式放在花括号中。

例如，我们可以编写以下代码:

`app.component.ts`

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
{{name}}
```

然后我们屏幕上出现了`foo`这个词。

正在发生的事情是，`app.component.ts`的值中的`name`字段被绑定到模板中的`name`字段。

我们还可以加入其他表达方式，比如:

```
{{ 1 + 1 }}
```

然后我们得到 2 显示。

在上面的例子中，我们在一个单独的文件中有一个模板。我们也可以将模板放在组件文件中。

我们可以将第一个示例改写为:

```
import { Component } from "@angular/core";@Component({
  selector: "app-root",
  template: `
    {{ name }}
  `,
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  name = "foo";
}
```

这将像第一个例子一样显示`foo`。

我们也可以初始化构造函数中的字段，如下所示:

```
import { Component } from "@angular/core";@Component({
  selector: "app-root",
  template: `
    {{ name }}
  `,
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  name: string;
  constructor() {
    this.name = "foo";
  }
}
```

它会做和其他例子一样的事情。

# 使用*ngFor 显示数组属性

我们可以使用`*ngFor`指令来遍历数组中的项目。

要使用它，我们可以如下使用它:

`app.component.ts`:

```
import { Component } from "@angular/core";@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  persons = [{ name: "Jane" }, { name: "John" }, { name: "Mary" }];
}
```

`app.component.html`:

```
<div *ngFor="let p of persons">{{p.name}}</div>
```

在上面的代码中，我们在`AppComponent`中定义了`persons`数组。

然后我们可以用`*ngFor`指令来呈现`app.component.html`中的数组。

我们通过编写`let p of persons`来遍历数组，然后将`p.name`放在花括号中，以显示花括号中每个条目的`name`属性。

`*ngFor`可以在任何可重复对象中重复项目。

例如，我们可以这样写:

`app.component.ts`:

```
import { Component } from "@angular/core";@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  getArgs() {
    return arguments;
  }
}
```

`app.component.html`:

```
<div *ngFor="let a of getArgs('John', 'Jane')">{{a}}</div>
```

在上面的代码中，`getArgs`返回了`arguments`对象，这是一个类似数组的可迭代对象。

然后，当我们将`'John'`和`'Jane'`传递给`getArgs`并用`*ngFor`循环遍历它时，我们会看到显示的项目就像在一个数组中一样。

# 为数据创建一个类

我们可以创建一个类来设置数据的结构。要创建一个类，我们可以写:

```
ng g class Person
```

在`person.ts`中，我们可以写道:

```
export class Person {
    constructor(name: string) { }
}
```

`Person`有一个`constructor`，它传递了`name`字符串字段。将它传递给`constructor`会将其声明为`Person`的一个字段。

然后在`app.component.ts`中，我们写道:

```
import { Component } from '@angular/core';
import { Person } from './person';@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  persons: Person[] = [new Person('Jane'), new Person('John')];
}
```

在上面的代码中，我们引用了`Person`类并从中创建了一个模板。

然后在`app.template.html`中，我们写道:

```
<div *ngFor="let p of persons">{{p.name}}</div>
```

显示`persons`条目。

创建类有助于我们组织数据。

# 使用 NgIf 的条件显示

我们可以使用`*ngIf`指令有条件地显示数据。

它将任何计算结果为 true 或 falsy 值的表达式作为一个值。

例如，我们可以写:

`app.component.ts`:

```
import { Component } from "@angular/core";@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  showText: boolean = false;
}
```

`app.component.html`:

```
<button (click)="showText = !showText">Toggle</button>
<div *ngIf="showText">foo</div>
```

在上面的代码中，我们在`AppComponent`中添加了`showText`布尔字段，并将其设置为`false`。

然后我们给`app.component.html`添加了一个按钮，它有一个点击处理程序，当我们点击按钮时，它在`true`和`false`之间切换`showText`。

如果`showText`是`true`，那么 div 使用`*ngIf`指令来显示‘foo’。

# 结论

我们可以通过在模板中放入一个 JavaScript 表达式来用 Angular 显示数据。

为了引用变量，我们可以在组件中将它们声明为字段，并在模板中引用它们。

我们可以用`*ngIf`指令有条件地显示项目，用`*ngFor`指令呈现数组和其他可迭代对象。