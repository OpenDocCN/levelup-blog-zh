# Laravel:我对基于惯性的文件结构和可调用控制器的建议

> 原文：<https://levelup.gitconnected.com/laravel-my-proposal-for-the-inertia-based-file-structure-and-invokable-controllers-8278ca15fbb3>

作为一名 Laravel 开发人员，我目前的喜好是将 Vue 作为前端，将 Inertia 作为连接器。在我的工作中，我相信我已经找到了建模任何 Laravel/Inertia 代码库的完美架构。

# 前端文件夹结构

我的 *resources/js* 的结构如下:

*   Pages:保存 GET 请求返回的任何 Vue 文件。
*   组件:包含可重用的组件，这些组件可以扩展到其他存储库。
*   Layouts:保存所有隐式包装在前端的布局。
*   分音:

与组件不同，这些全局部分是由它们所工作的存储库专门标识的。它们可以应用于存储库中的任何 Vue 文件，但是不能在存储库之外重用。

对于本地部分，如果它们适用于页面/组件/布局目录中的一个或多个 Vue 文件，则它们嵌套在这些目录中。

*   …所有其他文件

这种设置非常方便，几乎与 [Nuxt](https://nuxtjs.org/docs/get-started/directory-structure/) 使用的设置相同。有了这种设置，我发现以镜像我的*资源/js* 目录的方式构建我的控制器非常容易。

# 可调用控制器还是普通控制器？此外，文件夹结构

如果你对可调用(或单个动作)控制器一无所知，请阅读这里的。

下面是我的 *App/Http/Controllers* 设置:

## 结构:App/Http/控制器

```
- API
- Web
  - Actions
  - Pages
```

**API** 意在存储所有用于网站 API 的控制器，并保存在 *routes/api.php* 文件中。

**Web** 显然是为了保存所有的基于 Web 的控制器或者那些位于 *routes/web.php* 中的路由。

在 web 下面，有两个子文件夹:**动作**和**页面**。这里，Pages 文件夹中的每一个文件都要与 *resources/js/Pages* 目录中的文件对齐。

因此，作为并列比较，它看起来完全像这样:

## 结构:App/Http/Controllers/Web/

```
- Pages
  - Item
    - Index.php
```

## 结构:资源/js

```
- Pages
  - Item
    - Index.vue
```

Index.vue 文件包含索引可调用控制器类，Index.vue 包含前端。

通过这样做，你可以改善组织。此外，通过将每个方法分成自己的可调用控制器类文件，可以减少每个控制器的行数。

但是，如果您不想对每个控制器都使用 invokable，您可以使用这种替代设置。

## 结构:应用程序/Http/控制器/网页/页面

```
- Pages
  - ItemController.php
```

## 结构:资源/js

```
- Pages
  - Item
    - Index.vue
```

在 ItemController.php，你会有指数法。虽然乍看之下很难将它们与它们的 Vue 页面联系起来，但是它们确实让您能够使用 Laravel 的内置 route::resource 功能，从而允许您缩短 routes/web.php 文件。

然而，对于任何不完全符合[索引、创建、存储、编辑、更新、销毁]范式的控制器方法(例如，“发布”)，我建议将其设为可调用的，并放入*App/Http/Controllers/Web/Actions 目录*。

# 服务层

最后，为了处理重复的行为，我依赖服务类。

我添加了一个目录 *App/Services* ，我放了两个子目录。您可以将第二个命名为任何您喜欢的名称(例如，“Other”)，但是我将第一个命名为“ **Client** ”，并且在那个子目录中，我将 ***放置在方法与外部 API*** 交互的任何服务类中。如果我想连接到一个特定组织的 API，我将构建一个描述该组织的目录，然后创建一个服务类文件，描述该 API 试图完成的一组内容。

让我们以这个例子为例，我正在创建一个客户端服务类，它连接到 Widget Inc .的支付 API (widget/v2/getPayments，widget/v2/postPayment)。

## 结构:应用程序/服务/客户端

```
- Services
  - Client
    - Widget
      - V2
        - PaymentService.php
```

*PaymentService.php*包含所有调用相关支付 API 的方法。

## 文件:App/Services/Client/Widget/V2/paymentservice . PHP

```
<?php

namespace App/Services/Client/PaymentService;

...

class PaymentService {

	public function getPayments(...)
	{
	   ...
	}

	publc function postPayment(...)
	{
	   ...
	}
}
```

通过这样做，我能够编写可重用的功能，其唯一目的是以清晰可读的方式与外部实体的 API 进行交互。我可以很容易地抓住这个逻辑，复制并粘贴到任何其他基于 PHP 的项目，并让它工作。

# 利弊

## 赞成的意见

*   不使用服务等级的较短控制器代码。
*   控制器类与前端页面匹配。
*   不必考虑抽象的控制器名称来对逻辑进行分组(通常是模型的名称)，因为命名方案是基于您喜欢如何对页面和操作进行分组。
*   由于缩短的控制器通常只需要一个服务类别，所以服务类别可以被相当普遍地注入。

例如，

```
e...

$this->service = $service;

...
```

## 骗局

*   较长的路由文件。

因为你的控制器保存在不同的文件中，你不能利用`Route::resource`。

如果典型的控制器结构符合[索引、创建、存储、编辑、更新、销毁]范式，您可以使用它，只需将任何冗长的逻辑抽象为一个服务类，以减少所用代码行的长度。

*   如果您想要更改几个 Vue 文件的位置，并随后更改它们的等效控制器文件，那么重构可能是一场噩梦。
*   有些人喜欢每个模型有一个控制器来存储他们的模型 CRUD 逻辑，但是我不喜欢这种方法。任何一种抽象的典型模型-动作逻辑都应该在它自己的类中(例如，存储库模式)。而不是在控制器中，控制器主要用于抽象路由逻辑并保持路由文件整洁。