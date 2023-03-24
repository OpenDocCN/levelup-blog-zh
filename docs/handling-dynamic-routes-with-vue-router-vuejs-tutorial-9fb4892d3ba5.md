# 用 Vue 路由器处理动态路由

> 原文：<https://levelup.gitconnected.com/handling-dynamic-routes-with-vue-router-vuejs-tutorial-9fb4892d3ba5>

![](img/8326ca3d2b1df134b65f04a51b38dda9.png)

卢克·切瑟在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

没有人会记得这个 url，`http://yourwebsite.com/product/item-advxv12323-asdfa123`——所以让我们把它转换成更好的东西。

虽然 Vue Router 非常简单，但我经常发现自己在构建不同应用程序时面临的一个问题是试图为页面创建更漂亮的路径。

本文假设您已经了解 Vue 路由器的基础知识。只要你能使用默认的 Vue 路由器选项设置你的 Vue 项目(就像我的 ToDo 应用教程 **wink** )你就有了一个很好的起点。

本文将向您展示如何获取 URL 路径中的值并将其传递给 Vue 组件，从而允许您处理动态路由。

# 概观

我要用的例子是产品页面。本质上，我们所要做的就是利用 Vue 路由器捕捉动态路由的能力，并将其作为道具传递给 Vue 组件。然后，我们可以分析 URL 来确定它是否与现有产品匹配。如果是的话，让我们展示产品；否则，重定向到 404 页面。

# 设置组件

我们需要添加两个组件:

*   产品页面
*   404 页。

我不打算深入探讨页面的设计，我们只需要它们存在的导航目的。以下是我使用的示例页面。

## 产品. vue

```
<template lang="html">
  <div>
    Product Displayed: {{ product }}
  </div>
</template><script>
export default {
  props: ["product-path"],
  data () {
    return {
      product: {}
    }
  },
  created () {
   // look up the product // if it exists, proceed
  }
}
</script><style lang="scss">
</style>
```

## `PageNotFound.vue`

```
<template lang="html">
  <div>
    Error - Page Not Found
  </div>
</template><script>
export default {
}
</script><style lang="scss">
</style>
```

# 配置 Vue 路由器

设置 Vue 路由器非常容易，我们只需添加两条路由，以便映射到我们的两个组件。

首先，更容易添加的是 404 页面。只需将这段代码添加到您的 routes 数组中。

```
{
      path: '/404',
      name: '404',
      component: () =>
        import('./views/PageNotFound.vue')
}
```

现在是稍微复杂一点的(仍然很简单，不要担心)，它允许动态 URL 和参数。这与从 Node.js 中的 URL 获取参数遵循相同的模式，因此如果您对此有所了解，这应该是一件轻而易举的事情。如果你不这样做，你会很快看到它是如何工作的。

正确地将参数从 Vue 路由器传递到组件的唯一区别是，我们需要在这个路由中指定`props: true`。

这条路线的完整代码如下所示。

```
{
    path: "/:product",
    name: "Product",
    props: true,
    component: () => import("../views/Product.vue")
}
```

路径中的冒号表示该路由将选取所有匹配该模式的路径。换句话说，可以有任何不匹配另一个路由的字符串，而不是`:product`。由于我们将 props 设置为 true，该字符串将作为名为`product`的属性传递给`Product.vue`组件。

# 示例实现

现在我们有了映射到产品组件的所有动态路由，最后一步是确定何时我们有了实际的产品，何时我们需要重定向到 404 页面。

在完整的应用程序中，这通常是到某个数据库的连接，该数据库包含 URL 路径和产品之间的查找。然而，我认为没有必要在这里展示，因为我们只是主要关注这个问题的 Vue 路由器部分。

为了模拟这一点，我简单地将一个查找表硬编码到`Product.vue`中

在`created`钩子中，我们首先查找当前的 URL 路径是否映射到有效的产品。如果有，就展示出来。否则，我们使用命令`this.$router.push(“/404”)`将用户重定向到 404 页面。

```
// Product.vue<script>
const products = [
  {
    path: "example-product-1",
    product: {name: "Product 1", price: 500}
  },
  {
    path: "example-product-2",
    product: {name: "Product 2", price: 1000}
  },
  {
    path: "example-product-3",
    product: {name: "Product 3", price: 1500}
  }
]
export default {
  props: ["productPath"],
  data () {
    return {
      product: {}
    }
  },
  created () {
   // look up the product
   const foundProduct = products.filter(product => {
     return product.path === this.productPath
   });// if it exists, proceed
   if (foundProduct.length > 0) {
     this.product = foundProduct;
   } else {
     // otherwise, go to the 404 page
     this.$router.push("/404");
   }
  }
}
</script>
```

现在你知道了！这是将属性从路由传递到 Vue 组件并使用该信息来确定路由是否有效的基础。

如果你想看我的示例代码，你可以在 [Github](https://github.com/matthewmaribojoc/vue-router-tutorial) 上查看。

# 扩展它的方法

当然，这只是一个简单的教程，向您展示如何在 Vue 路由器中使用动态路由。这里有几个方法可以让它变得更高级。

*   实现数据库连接。
*   您可以实现条件组件呈现，以便能够处理不仅仅是产品(或任何其他类型的需要动态 URL 的对象)。这可以通过使用`<component :is=''>`标签来完成。

# 额外阅读

如果您对更高级的 vue 路由器功能感兴趣，那么[文档](https://router.vuejs.org/guide/essentials/passing-props.html)非常棒！在 Vue 路由器中，您可以做很多事情来消除不必要的重定向。

[如果你有兴趣了解更多关于 Vue 3 的知识，请下载我的免费 Vue 3 备忘单，里面有一些基本知识，比如合成 API、Vue 3 模板语法和事件处理。](https://learnvue.co/vue-3-essentials-cheatsheet/)