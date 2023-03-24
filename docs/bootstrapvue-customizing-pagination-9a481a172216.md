# BootstrapVue —自定义分页

> 原文：<https://levelup.gitconnected.com/bootstrapvue-customizing-pagination-9a481a172216>

![](img/7202c74fa86017303dd3f3e3446dd9ce.png)

Alex Munsell 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了制作好看的 Vue 应用，我们需要设计组件的样式。为了使我们的生活更容易，我们可以使用内置样式的组件。

在本文中，我们将研究如何在页面中添加分页按钮。

# 第一个和最后一个按钮类型

我们可以改变在分页组件中显示哪些按钮。

属性表明我们是否希望总是在第一页显示按钮。

同样，`last-number`属性表明我们是否希望总是显示最后一页的按钮。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-pagination first-number v-model="page" :total-rows="rows" :per-page="perPage"></b-pagination> <p>Current Page: {{ page }}</p>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      rows: 100,
      perPage: 10,
      page: 1
    };
  }
};
</script>
```

现在我们总是能看到转到第一页的按钮。

同样，如果我们有:

```
<template>
  <div id="app">
    <b-pagination last-number v-model="page" :total-rows="rows" :per-page="perPage"></b-pagination><p>Current Page: {{ page }}</p>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      rows: 100,
      perPage: 10,
      page: 1
    };
  }
};
</script>
```

然后我们总是看到转到最后一页的按钮。

# 按钮大小

按钮大小可以用`size`道具改变。

值`sm`使它们小于默认值。

`lg`使它们大于默认值。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-pagination size="sm" v-model="page" :total-rows="rows" :per-page="perPage"></b-pagination><p>Current Page: {{ page }}</p>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      rows: 100,
      perPage: 10,
      page: 1
    };
  }
};
</script>
```

现在它们看起来更小了。

# 药丸样式

我们可以添加`pills`道具，使按钮看起来像药丸:

```
<template>
  <div id="app">
    <b-pagination pills v-model="page" :total-rows="rows" :per-page="perPage"></b-pagination> <p>Current Page: {{ page }}</p>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      rows: 100,
      perPage: 10,
      page: 1
    };
  }
};
</script>
```

# 对齐

分页按钮可以在我们想要的位置对齐。

我们可以使用`align`道具来设置校准。

默认对齐方式是左对齐。

其他可能的值有`right`向右对齐、`center`居中对齐、`fill`填充屏幕宽度。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-pagination align="center" v-model="page" :total-rows="rows" :per-page="perPage"></b-pagination><p>Current Page: {{ page }}</p>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      rows: 100,
      perPage: 10,
      page: 1
    };
  }
};
</script>
```

因为我们将`align`设置为`'center'`，所以分页栏位于屏幕中央。

# 分页导航

BootstrapVue 还提供了让我们设置导航位置的`b-pagination-nav`组件。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-pagination-nav :link-gen="linkGen" :number-of-pages="10" use-router></b-pagination-nav>
  </div>
</template>
<script>
export default {
  name: "App",
  methods: {
    linkGen(pageNum) {
      return pageNum === 1 ? "?" : `?page=${pageNum}`;
    }
  }
};
</script>
```

`b-pagination`和`b-pagination-nav`的区别在于分页链接可以转到我们想要的 URL。

我们有`link-gen`属性，它返回一些我们可以添加到当前 URL 的内容。

`number-of-page`让我们显示所需的页面按钮数量。

`use-router`让我们导航到 Vue 或 Nuxt 路由器生成的 URL。

`linkGen`方法也可以返回一个对象。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-pagination-nav :link-gen="linkGen" :number-of-pages="10" use-router></b-pagination-nav>
  </div>
</template>
<script>
export default {
  name: "App",
  methods: {
    linkGen(pageNum) {
      return { path: `/foo/page/${pageNum}` };
    }
  }
};
</script>
```

该对象具有追加到当前 URL 的属性。

它还可以具有分别用于路线名称和查询字符串的`name`和`query`属性:

```
<template>
  <div id="app">
    <b-pagination-nav :link-gen="linkGen" :number-of-pages="10" use-router></b-pagination-nav>
  </div>
</template>
<script>
export default {
  name: "App",
  methods: {
    linkGen(pageNum) {
      return {
        name: "page",
        params: { page: pageNum }
      };
    }
  }
};
</script>
```

或者:

```
linkGen(pageNum) {
  return {
    path: '/foo/',
    query: { page: pageNum }
  }
}
```

![](img/d99e840635713b1bd8d816d18ddedc3c.png)

照片由 [Brenda Godinez](https://unsplash.com/@cravethebenefits?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以定制`b-pagination`组件上的分页按钮。

此外，我们可以添加一个`b-pagination-nav`来生成链接，我们可以点击链接到一个网址。