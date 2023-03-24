# 角度 10 NgRX 存储示例

> 原文：<https://levelup.gitconnected.com/angular-10-ngrx-store-by-example-afec6929bbf9>

![](img/9f02552db3e7c2defe10e039c44295b7.png)

由 [Mike Petrucci](https://unsplash.com/@mikepetrucci?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本教程中，我们将学习如何在 Angular 10 示例应用程序中使用 NgRX store。我们将看到如何创建动作、减少器和分派动作。

Angular NgRx store 是一个客户端数据管理模式，它使用 RxJS observables 实现了脸书发明的 Redux 模式。

作为先决条件，您需要在本地开发机器上安装 Node.js 和 Angular CLI，

# 角度 10 NgRx 存储示例

打开一个新的命令行界面，并运行以下命令:

```
$ ng new angular10ngrx
$ cd angular10ngrx
```

CLI 将询问您几个问题—如果**您想要添加角度路由？**键入 **y** 表示是，键入**表示您希望使用哪种样式表格式？**选择 **CSS** 。

接下来，使用以下命令创建一个新的角度组件:

```
$ ng g c product
```

接下来，`src/app/product`文件夹中的一个文件名为`product.model.ts`的文件如下:

```
export interface Product {
  name: string;
  price: number;
}
```

# 安装 NgRx 库

接下来，运行下面的命令从 npm 安装`ngrx`到您的项目中，如下所示:

```
$ npm install @ngrx/core
$ npm install @ngrx/store
$ npm install @ngrx/effects
```

# 创建 NgRx 减速器

用下面的代码创建一个`src/app/reducers`文件夹和一个名为`product.reducer.ts`的文件:

```
// product.reducer.tsimport { Product } from './../product/product.model';
import { Action } from '@ngrx/store';export const ADD_PRODUCT = 'ADD_PRODUCT';export function addProductReducer(state: Product[] = [], action) {
  switch (action.type) {
    case ADD_PRODUCT:
        return [...state, action.payload];
    default:
        return state;
    }
}
```

# 配置 NgRx 存储

接下来，打开`src/app/app.module.ts`文件并更新如下:

```
// app.module.tsimport { StoreModule } from '@ngrx/store';
import { addProductReducer } from './reducers/product.reducer'; imports: [
    BrowserModule,
    StoreModule.forRoot({product: addProductReducer})
],
```

我们只需将减速器送到商店。我们可以通过将动作分派给商店来更新应用程序状态。

接下来，打开`src/app/product/product.component.ts`文件，并更新如下:

```
// src/app/product/product.component.tsimport { Product } from './product.model';
import { AppState } from './../app.state';
import { Component, OnInit } from '@angular/core';
import { Store } from '@ngrx/store';@Component({
  selector: 'app-product',
  templateUrl: './product.component.html',
  styleUrls: ['./product.component.css']
})
export class ProductComponent implements OnInit { products: Observable<Product[]>; constructor(private store: Store<AppState>) {
    this.products = this.store.select(state => state.product);
   } addProduct(name, price) {
    this.store.dispatch({
      type: 'ADD_PRODUCT',
      payload: <Product> {
        name: name,
        price: price
      }
    });
  } ngOnInit() {
  }}
```

我们首先订阅商店并获取产品，然后添加一个向商店添加产品的操作。

接下来，创建一个`src/app/app.state.ts`文件并添加以下代码:

```
// src/app/app.state.tsimport { Product } from './product/product.model';export interface AppState {
  readonly product: Product[];
}
```

# 显示 NgRx 操作

接下来，打开`src/app/product/product.component.html`文件，并按如下方式更新它:

```
<div class="panel panel-primary">
    <div class="panel-body">
      <form>
        <div class="form-group">
          <label class="col-md-4">Product Name</label>
          <input type="text" class="form-control" #productName/>
        </div>
        <div class="form-group">
          <label class="col-md-4">Product Price</label>
          <input type="text" class="form-control" #productPrice />
          </div>
          <div class="form-group">
            <button (click) = "addProduct(productName.value, productPrice.value)" class="btn btn-primary">Add Product</button>
          </div>
      </form>
    </div>
  </div><table class="table table-hover" *ngIf="products != 0">
  <thead>
  <tr>
      <td>Product Name</td>
      <td>Product Price</td>
  </tr>
  </thead>
  <tbody>
      <tr *ngFor="let product of products | async">
          <td></td>
          <td></td>
      </tr>
  </tbody>
  </table>
```

我们首先创建一个表单，通过将相应的动作分派给商店来创建新产品，然后创建一个表来显示我们在商店中创建的产品。`products`变量是一个可观察变量，所以我们使用`[async](https://www.techiediaries.com/angular-10-async-pipe-observable-promise-example/)` [管道来订阅](https://www.techiediaries.com/angular-10-async-pipe-observable-promise-example/)它。

我们使用 Bootstrap 4 向我们的应用程序添加样式。阅读如何[将引导程序添加到 Angular 10 应用程序](https://www.techiediaries.com/angular-bootstrap/)。

您可以使用以下命令为您的应用程序提供服务:

```
$ ng serve --open
```

# 结论

在这个简单的例子中，我们学习了如何在 Angular 10 示例应用程序中使用 ngrx store。我们已经看到了如何分派动作，以及如何创建 reducers。

这篇文章最初发表在[技术资料](https://www.techiediaries.com/angular-10-ngrx-store-example/)上。