# 角度-布线基础

> 原文：<https://levelup.gitconnected.com/angular-routing-basics-30f9c9e19d29>

![](img/f9ad503f6f11ce50e6b877835466070c.png)

照片由[迭戈·希门尼斯](https://unsplash.com/@diegojimenez?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在这篇文章中，我们将看看如何添加路由到我们的角度应用程序。

# 带角度的布线

当我们转到某个 URL 时，我们需要路由来加载组件。

要创建启用路由的应用程序，我们运行:

```
ng new routing-app --routing
```

路线将相对于基本路径进行定义，基本路径设置为`base`标签的`href`值。

首先，我们创建一些想要映射 URL 的组件。

为此，我们运行:

```
ng generate component first
ng generate component second
```

在`app.module.ts`，我们应该有`AppRoutingModule`:

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { FirstComponent } from './first/first.component';
import { SecondComponent } from './second/second.component';@NgModule({
  declarations: [
    AppComponent,
    FirstComponent,
    SecondComponent
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

那么在`app-routing.module.ts`中，我们应该有:

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';const routes: Routes = [];@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

现在我们可以定义路线了。

为此，我们写道:

`app-routing.module.ts`

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { FirstComponent } from './first/first.component';
import { SecondComponent } from './second/second.component';const routes: Routes = [
  { path: 'first-component', component: FirstComponent },
  { path: 'second-component', component: SecondComponent },
];@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

然后在`app.coomponent.html`中，我们写道:

```
<nav>
  <ul>
    <li><a routerLink="/first-component" routerLinkActive="active">First
        Component</a></li>
    <li><a routerLink="/second-component" routerLinkActive="active">Second
        Component</a></li>
  </ul>
</nav>
<router-outlet></router-outlet>
```

当我们点击链接时,`routerLink`属性有我们想要访问的路线的路径。

`routerLink`激活设置变量，用于设置链接是否激活。

# 获取路线信息

为了获得路由数据，我们可以调用`this.route.queryParams.subscribe`方法来获得查询字符串键值对。

例如在`app.component.ts`中，我们可以这样写:

```
import { Component } from '@angular/core';
import { ActivatedRoute } from '@angular/router';@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'angular-example';
  constructor(
    private route: ActivatedRoute,
  ) { } name: string; ngOnInit() {
    this.route.queryParams.subscribe(params => {
      this.name = params['name'];
      console.log(this.name)
    });
  }
}
```

我们注入`route`依赖项，然后调用`ngOnInit`中的`subscribe`方法，以便在加载组件时观察查询参数的变化。

然后我们从`params`参数中获得查询参数。

当我们转到`http://localhost:4200/first-component?name=foo`时，我们从`name`查询参数中获得`'foo'`。

# 通配符路由

我们可以使用`'**'`字符串定义通配符路由:

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { FirstComponent } from './first/first.component';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';
import { SecondComponent } from './second/second.component';const routes: Routes = [
  { path: 'first-component', component: FirstComponent },
  { path: 'second-component', component: SecondComponent },
  { path: '**', component: PageNotFoundComponent },
];@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

所以现在如果我们去除了`first-component`或`second-component`之外的任何 URL，角度加载`PageNotFoundComponent`。

# 结论

我们可以使用路由模块添加带角度的路由。