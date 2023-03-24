# 定义和使用弯管

> 原文：<https://levelup.gitconnected.com/defining-and-using-angular-pipes-82e5e77df97b>

![](img/cb03336428d43c3366de0832baa9485a.png)

文森特·范·扎林格在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Angular 是 Google 制作的一个流行的前端框架。像其他前端框架一样，它使用基于组件的架构来构建应用程序。

在本文中，我们将看看如何定义和使用角管道来转换屏幕上的数据。

# 使用管道

我们可以使用管道来转换显示在屏幕上的数据。

例如，我们想将一个`Date`对象转换成以某种格式显示的日期。

为此，我们可以使用 Angular 的内置`date`管道。

例如，如果我们在`AppComponent`中有`birthday`字段:

```
import { Component } from "@angular/core";@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  birthday = new Date(2008, 3, 15);
}
```

我们可以在`app.component.html`中以人类可读的方式显示`birthday`，如下所示:

```
{{birthday | date}}
```

在一个模板插值表达式中，我们可以使用`|`操作符将管道应用到一个变量来转换它。

上面的例子应该给我们提供:

```
Apr 15, 2008
```

在屏幕上。

# 内置管道

Angular 自带`DatePipe`、`UpperCasePipe`、`LowerCasePipe`、`PercentPipe`。

# 参数化管道

管道可以接受可选参数来微调其输出。

我们可以向管道添加参数，方法是添加管道名称，后跟一个冒号，然后是参数。

我们可以通过用冒号分隔来添加多个参数。

例如，我们可以用 MM/DD/YY 格式显示上面的`birthday`,方法是:

```
{{ birthday | date:"MM/dd/yy" }}
```

然后我们得到:

```
04/15/08
```

显示在屏幕上。

我们还可以通过用变量替换格式字符串来进行格式化，如下所示:

`app.component.ts`:

```
import { Component } from "@angular/core";@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  birthday = new Date(2008, 3, 15);
  format = "fullDate"; toggleFormat() {
    this.format = this.format === "fullDate" ? "shortDate" : "fullDate";
  }
}
```

`app.component.html`:

```
<button (click)="toggleFormat()">toggle format</button>
<p>{{ birthday | date: format }}</p>
```

在上面的代码中，我们在`AppComponent`中有一个`toggleFormat`方法来在`'fullDate'`和`'shortDate'`之间切换`format`字段。

然后我们在`app.component.html`中有一个按钮，当点击“切换格式”按钮时调用该方法。

当我们单击该按钮时，我们应该看到日期格式在完整和简短之间切换。

# 链接管道

我们可以链接多个管道来应用多个转换。

例如，我们可以将`date`和`uppercase`管道组合成我们的`birthday`大写。

我们可以这样写:

```
{{ birthday | date | uppercase}}
```

# 自定义管道

我们可以创建自己的管道来进行 Angular 内置管道中没有的转换。

例如，我们可以创建一个`exponent`管道，它转换一个基数，并根据我们作为参数传入的指数对其进行提升。

为了创建管道的骨架，我们运行:

```
ng g pipe exponent
```

然后我们可以编写下面的代码:

`app.module.ts`:

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { ExponentPipe } from './exponent.pipe';@NgModule({
  declarations: [
    AppComponent,
    ExponentPipe
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

`exponent.pipe.ts`:

```
import { Pipe, PipeTransform } from '@angular/core';@Pipe({
  name: 'exponent'
})
export class ExponentPipe implements PipeTransform {
  transform(value: number, exponent: number): number {
    return Math.pow(value, exponent);
  }
}
```

`app.component.html`:

```
{{ 2 | exponent:3 }}
```

我们通过传入一个数字 2 来使用我们的`exponent`管道，然后将它传入我们的`exponent`管道，并传入指数 3。

然后我们应该会看到屏幕上显示 8。

`transform`方法接受一个为 2 的`value`和一个为 3 的`exponent`，并将提升的`value`返回给`exponent`。

根据`PipeTransform`接口的要求，管道必须具有`transform`方法。

此外，我们必须在模块中包含`ExponentPipe`才能像在`app.module.ts`中一样使用它。

![](img/def308f97bbe274392d71f6715916db9.png)

维多利亚·希斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 纯净和不纯净的管道

纯管道仅在检测到输入值的纯变化时执行。纯粹的更改要么是对原始输入值(字符串、数字、布尔、符号)的更改，要么是已更改的对象引用。

角度忽略复合对象内的更改。如果我们改变一个输入的属性，它不会调用管道。

这是因为递归检查对象结构很慢。Angular 不喜欢这样做，除非它被明确告知这样做。

不纯管道在每个组件更改检测周期中检查更改。

我们可以添加一个`pure`选项，并将其设置为`false`来使管道不纯。

例如，我们可以这样做:

```
import { Pipe, PipeTransform } from '@angular/core';@Pipe({
  name: 'exponent',
  pure: false
})
export class ExponentPipe implements PipeTransform {
  transform(value: number, exponent: number): number {
    return Math.pow(value, exponent);
  }
}
```

# 纯管道和纯函数

纯管道应该用纯函数来实现。否则，我们会得到错误消息，说表达式在被检查后已经改变。

# 结论

我们可以创建自己的管道将屏幕上的数据转换成我们想要的。

Angular 也有几个我们可以使用的内置管道。

如果管道仅在原始值或引用发生变化时运行，那么它们就是纯管道。

不纯管道是那些在每个变化周期运行的管道。