# 如何在 Angular 应用程序中使用砖石效果显示图像

> 原文：<https://levelup.gitconnected.com/how-to-display-images-like-pinterest-and-flickr-in-an-angular-app-6f6fa9c3cc35>

## 将 Pinterest 和 Flickr 等图片放在一个有角度的应用程序中

![](img/6b26006faf8f8ad332e7b3062718f44d.png)

如果你使用像 Pinterest 或 Flickr 这样的图片搜索网站，你会注意到它们的图片显示在一个看起来像砖墙的网格中。这些图像高度不均匀，但宽度相等。这被称为砖石效应，因为它看起来像一堵砖墙。

为了实现砖石效果，我们必须将图像的宽度设置为与屏幕宽度成比例，并将图像的高度设置为与图像的纵横比成比例。

如果没有任何库，这是一件痛苦的事情，所以人们制作了包来创造这种效果。

在本文中，我们将构建一个照片应用程序，允许用户搜索图像并在砖石网格中显示图像。图像网格将无限滚动，以获得更多的图像。我们将用 Angular 和砌体布局和 Ng 砌体网格库来构建它。我们将把 Ngx-Infinite-Scroll 组件包装在 Ng-Masonry-Grid 之外，以获得显示图像时的无限滚动效果。

我们的应用程序将显示来自 Pixabay API 的图像。你可以在[https://pixabay.com/api/docs/](https://pixabay.com/api/docs/)查看 API 文档并注册一个密钥

## 入门指南

首先，我们运行 Angular CLI 来启动项目。如果还没有安装，我们运行`npm i -g @angular/cli`来安装 Angular CLI。然后运行`ng new image-app`来创建项目。在向导中，选择包含路由并使用 SCSS 作为 CSS 预处理器。

接下来，我们安装我们的软件包。我们需要为无限卷轴安装 Ngx-Infinite-Scroll，为砌体网格布局安装 Ng-Masonry-Grid 及其依赖项。为了安装所有东西，我们运行:

```
npm i imagesloaded masonry-layout ng-masonry-grid ngx-infinite-scroll
```

接下来，我们继续编写代码。首先，我们通过运行以下命令来创建我们需要的文件:

```
ng g component homePage
ng g component searchPage
ng g service image
```

然后在`home-page.component.html`中，我们将现有代码替换为:

```
<h1 class="text-center">Home</h1>
<div
  class="search-results"
  infiniteScroll
  [infiniteScrollDistance]="10"
  [infiniteScrollThrottle]="50"
  (scrolled)="getImages()"
>
  <ng-masonry-grid
    [masonryOptions]="{
      transitionDuration: '0.8s',
      gutter: 5,
      columnWidth: 300,
      fitWidth: true
    }"
    [useAnimation]="true"
    [useImagesLoaded]="true"
    [scrollAnimationOptions]="{
      animationEffect: 'effect-4',
      minDuration: 0.4,
      maxDuration: 0.7
    }"
  >
    <ng-masonry-grid-item
      id="{{ 'masonry-item-' + i }}"
      *ngFor="let item of images"
      class="masonry-item"
    >
      <img [src]="item.previewURL" />
    </ng-masonry-grid-item>
  </ng-masonry-grid>
</div>
```

## 构建组件

添加内部带有砖石网格组件的无限滚动容器。我们将`infiniteScrollDistance`设置为 10，这样当我们浏览完 90%的内容时，它将开始加载新数据，然后我们将`infiniteScrollThrottle`设置为 50，这样当我们到达页面底部时，它在加载新数据之前会等待 50 毫秒。

`scrolled`事件是当我们滚动得足够低时加载新数据。在这种情况下，我们通过调用`getImages`获得更多的图像。

在`ng-masonry-grid`组件中，我们设置我们的砖石选项。我们用内置效果之一的`transitionDuration: ‘0.8s'`和`scrollAnimationOptions`、`effect-4`给网格添加一些动画。它显示一个闪光。同样，我们用`minDuration`和`maxDuration`属性设置动画的持续时间。在组件内部，我们展示了来自 Pixabay API 的图像。

在`home-page.component.ts`中，我们将现有代码替换为:

```
import { Component, OnInit } from '[@angular/core](http://twitter.com/angular/core)';
import { ImageService } from '../image.service';[@Component](http://twitter.com/Component)({
  selector: 'app-home-page',
  templateUrl: './home-page.component.html',
  styleUrls: ['./home-page.component.scss']
})
export class HomePageComponent implements OnInit {
  images: any[] = [];
  page = 1; constructor(private imageService: ImageService) { } ngOnInit() {
    this.getImages();
  } getImages() {
    this.page++;
    this.imageService.getImages(this.page)
      .subscribe((res: any) => {
        this.images = this.images.concat(res.hits);
      })
  }}
```

这将在页面首次加载时获取图像，并在我们滚动时追加更多图像。

接下来在`search-page.component.html`中，我们将现有代码替换为:

```
<h1 class="text-center">Search</h1>
<form (ngSubmit)="search(searchForm)" #searchForm="ngForm">
  <div class="form-group">
    <label>Keyword</label>
    <input
      type="text"
      class="form-control"
      placeholder="Keyword"
      #keyword="ngModel"
      name="keyword"
      [(ngModel)]="form.keyword"
      required
    />
    <div *ngIf="keyword?.invalid && (keyword.dirty || keyword.touched)">
      <div *ngIf="keyword.errors.required">
        Keyword is required.
      </div>
      <div *ngIf="keyword.invalid">
        Keyword is invalid.
      </div>
    </div>
  </div><button class="btn btn-primary">Search</button>
</form><br /><div
  class="search-results"
  infiniteScroll
  [infiniteScrollDistance]="10"
  [infiniteScrollThrottle]="50"
  (scrolled)="getImages()"
>
  <ng-masonry-grid
    [masonryOptions]="{
      transitionDuration: '0.8s',
      gutter: 5,
      columnWidth: 300,
      fitWidth: true
    }"
    [useAnimation]="true"
    [useImagesLoaded]="true"
    [scrollAnimationOptions]="{
      animationEffect: 'effect-4',
      minDuration: 0.4,
      maxDuration: 0.7
    }"
  >
    <ng-masonry-grid-item
      id="{{ 'masonry-item-' + i }}"
      *ngFor="let item of images"
      class="masonry-item"
    >
      <img [src]="item.previewURL" />
    </ng-masonry-grid-item>
  </ng-masonry-grid>
</div>
```

我们有和主页一样的无限滚动容器和砖石网格。我们唯一添加的是通过关键字搜索图片的搜索表单。我们使用 Angular 的内置表单验证来检查是否输入了关键字。然后我们在组件代码中调用`search`函数来搜索图像。

接下来在`search-page.component.ts`中，我们将代码替换为:

```
import { Component, OnInit } from '[@angular/core](http://twitter.com/angular/core)';
import { ImageService } from '../image.service';
import { NgForm } from '[@angular/forms](http://twitter.com/angular/forms)';[@Component](http://twitter.com/Component)({
  selector: 'app-search-page',
  templateUrl: './search-page.component.html',
  styleUrls: ['./search-page.component.scss']
})
export class SearchPageComponent implements OnInit {
  images: any[] = [];
  form: any = <any>{};
  page = 1; constructor(private imageService: ImageService) { } ngOnInit() { } getImages() {
    this.page++;
    this.imageService.searchImages(this.form.keyword, this.page)
      .subscribe((res: any) => {
        this.images = this.images.concat(res.hits);
      })
  } search(searchForm: NgForm) {
    if (searchForm.invalid) {
      return;
    }
    this.images = [];
    this.imageService.searchImages(this.form.keyword, this.page)
      .subscribe((res: any) => {
        this.images = this.images.concat(res.hits);
      })
  }
}
```

这个文件有我们在模板中调用的`search`函数，以及我们在无限滚动容器中调用的`getImages`函数，以获取更多图像并将其附加到我们的`images`数组中。`search`函数采用关键字和页码，通过关键字进行图像搜索。

在`app-routing.module.ts`中，我们输入:

```
import { NgModule } from '[@angular/core](http://twitter.com/angular/core)';
import { Routes, RouterModule } from '[@angular/router](http://twitter.com/angular/router)';
import { HomePageComponent } from './home-page/home-page.component';
import { SearchPageComponent } from './search-page/search-page.component';const routes: Routes = [
  { path: '', component: HomePageComponent },
  { path: 'search', component: SearchPageComponent },
];[@NgModule](http://twitter.com/NgModule)({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

当用户点击链接或输入 URL 时，他们可以看到我们刚刚添加的页面。

接下来在`app.component.html`中，我们输入:

```
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <a class="navbar-brand" routerLink="/">Image App</a>
  <button
    class="navbar-toggler"
    type="button"
    data-toggle="collapse"
    data-target="#navbarSupportedContent"
    aria-controls="navbarSupportedContent"
    aria-expanded="false"
    aria-label="Toggle navigation"
  >
    <span class="navbar-toggler-icon"></span>
  </button><div class="collapse navbar-collapse" id="navbarSupportedContent">
    <ul class="navbar-nav mr-auto">
      <li class="nav-item active">
        <a class="nav-link" routerLink="/">Home </a>
      </li>
      <li class="nav-item active">
        <a class="nav-link" routerLink="/search">Image </a>
      </li>
    </ul>
  </div>
</nav><div class="page">
  <router-outlet></router-outlet>
</div>
```

这将链接添加到我们的页面，并暴露`router-outlet`以便用户可以看到我们的页面。

然后在`app.component.scss`中，我们添加:

```
.page {
  padding: 20px;
}nav {
  background-color: lightpink !important;
}
```

在`app.module.ts`中，我们将现有代码替换为:

```
import { BrowserModule } from '[@angular/platform-browser](http://twitter.com/angular/platform-browser)';
import { NgModule } from '[@angular/core](http://twitter.com/angular/core)';import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { HomePageComponent } from './home-page/home-page.component';
import { SearchPageComponent } from './search-page/search-page.component';
import { NgMasonryGridModule } from 'ng-masonry-grid';
import { FormsModule } from '[@angular/forms](http://twitter.com/angular/forms)';
import { HttpClientModule } from '[@angular/common](http://twitter.com/angular/common)/http';
import { ImageService } from './image.service';
import { InfiniteScrollModule } from 'ngx-infinite-scroll';[@NgModule](http://twitter.com/NgModule)({
  declarations: [
    AppComponent,
    HomePageComponent,
    SearchPageComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    NgMasonryGridModule,
    FormsModule,
    HttpClientModule,
    InfiniteScrollModule
  ],
  providers: [ImageService],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

我们添加我们在应用中使用的组件、服务和库。

在`image.service.ts`中，我们将代码替换为:

```
import { Injectable } from '[@angular/core](http://twitter.com/angular/core)';
import { HttpClient } from '[@angular/common](http://twitter.com/angular/common)/http';
import { environment } from 'src/environments/environment.prod';[@Injectable](http://twitter.com/Injectable)({
  providedIn: 'root'
})
export class ImageService { constructor(private http: HttpClient) { } getImages(page = 1) {
    return this.http.get(`${environment.apiUrl}/?key=${environment.apiKey}&page=${page}`)
  } searchImages(keyword, page = 1) {
    return this.http.get(`${environment.apiUrl}/?key=${environment.apiKey}&q=${keyword}&page=${page}`)
  }}
```

这让我们可以在组件中发出请求，从 Pixabay API 获取必要的数据。我们有`getImages`功能来获取图片，还有`searchImages`功能来通过关键字搜索图片。

然后在`styles.scss`中，我们添加:

```
$width: 300px;
$auto: auto;
ng-masonry-grid {
  margin: 0 $auto;
}.masonry-item {
  height: $auto;
  width: $width;
  img {
    height: $auto;
    width: $width;
    display: inline-block;
  }
}
```

这会将图像宽度更改为 300 像素，并自动调整高度以保持图像的纵横比。

最后，在`index.html`中，我们将代码替换为:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Image App</title>
    <base href="/" /><meta name="viewport" content="width=device-width, initial-scale=1" />
    <link rel="icon" type="image/x-icon" href="favicon.ico" />
    <link
      href="[https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css](https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css)"
      rel="stylesheet"
    />
    <script
      src="[https://code.jquery.com/jquery-3.3.1.slim.min.js](https://code.jquery.com/jquery-3.3.1.slim.min.js)"
      integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo"
      crossorigin="anonymous"
    ></script>
    <script
      src="[https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js](https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js)"
      integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1"
      crossorigin="anonymous"
    ></script>
    <script
      src="[https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js](https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js)"
      integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM"
      crossorigin="anonymous"
    ></script>
  </head>
  <body>
    <app-root></app-root>
  </body>
</html>
```

这将引导 CSS 和 JavaScript 依赖项添加到我们的应用程序中，并更改了标题。

做完所有工作，我们就可以运行`ng serve`来运行 app 了。那么我们应该得到:

![](img/6fca430c698cc1b77a328bf34cbd50f0.png)