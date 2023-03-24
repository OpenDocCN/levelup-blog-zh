# BootstrapVue —分页导航

> 原文：<https://levelup.gitconnected.com/bootstrapvue-pagination-nav-85b72bfee132>

![](img/52787fb9c402874ab3db5429553e199d.png)

由[莎拉·基利安](https://unsplash.com/@rojekilian?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了制作好看的 Vue 应用，我们需要设计组件的样式。为了使我们的生活更容易，我们可以使用内置样式的组件。

我们看看如何添加分页导航链接到我们的网页。

# 页码生成

我们可以生成任何我们喜欢的内容的链接。

我们所要做的就是改变属性来返回我们想要的 URL 路径。

`page-gen` prop 可以生成我们想要的任何链接文本:

```
<template>
  <div class="overflow-auto">
    <b-pagination-nav :link-gen="linkGen" :page-gen="pageGen" :number-of-pages="links.length"></b-pagination-nav>
  </div>
</template><script>
export default {
  data() {
    return {
      links: ["#foo", "#bar", "#baz", "#qux"]
    };
  },
  methods: {
    linkGen(pageNum) {
      return this.links[pageNum - 1];
    },
    pageGen(pageNum) {
      return this.links[pageNum - 1].slice(1);
    }
  }
};
</script>
```

我们有`linkGen`方法返回我们想要的路径。并且`pageGen`方法返回与`linkGen`相同的条目，但是没有散列。

所以我们会看到显示了' foo '，' bar '，' baz '，' qux '。

当我们点击它们的时候，我们就会转到那些链接。

# 页面数组

我们可以使用`use-router`道具，这样链接就不是`a`标签了。导航将使用 JavaScript 来完成，而不是常规的链接。

例如，我们可以写:

```
<template>
  <div>
    <b-pagination-nav :pages="pages" use-router></b-pagination-nav>
  </div>
</template><script>
export default {
  data() {
    return {
      pages: ["?page=1", "?page=2", "?page=3"]
    };
  }
};
</script>
```

然后我们得到 3 个带有查询字符串的链接，作为要访问的 URL。

我们也可以让数组中的对象具有`link`和`text`属性。

例如，我们可以写:

```
<template>
  <div>
    <b-pagination-nav :pages="pages" use-router></b-pagination-nav>
  </div>
</template><script>
export default {
  data() {
    return {
      pages: [
        { link: "?page=1", text: "one" },
        { link: "?page=2", text: "two" },
        { link: "?page=3", text: "three" }
      ]
    };
  }
};
</script>
```

我们有带有 URL 的属性`link`。

`text`有每个链接的文本。

或者，我们可以用`query`属性来编写查询字符串。

例如，我们可以写:

```
<template>
  <div>
    <b-pagination-nav :pages="pages" use-router></b-pagination-nav>
  </div>
</template><script>
export default {
  data() {
    return {
      pages: [
        { link: { query: { page: 1 } }, text: "one" },
        { link: { query: { page: 2 } }, text: "two" },
        { link: { query: { page: 3 } }, text: "three" }
      ]
    };
  }
};
</script>
```

我们有和以前一样的查询字符串。

但是创建查询字符串要容易得多，因为我们可以只添加键值对。

# 按钮内容

我们可以按照我们想要的方式改变按钮的内容。

为此，我们可以设置`first-text`、`prev-text`、`next-text`和`last-next`道具。

例如，我们可以写:

```
<template>
  <div>
    <b-pagination-nav
      number-of-pages="15"
      base-url="#"
      first-text="First"
      prev-text="Prev"
      next-text="Next"
      last-text="Last"
    ></b-pagination-nav>
  </div>
</template><script>
export default {};
</script>
```

我们设置`first-text`属性来设置进入第一页的文本。

`prev-text`属性被设置为改变链接的文本以转到上一页。

`next-text`属性被设置为改变下一步按钮的文本。

`last-text`用于设置转到最后一页的链接的文本。

此外，我们可以填充插槽进行更多的定制。

例如，我们可以写:

```
<template>
  <div>
    <b-pagination-nav number-of-pages="15" base-url="#">
      <template v-slot:first-text>
        <span class="text-success">First</span>
      </template>
      <template v-slot:prev-text>
        <span class="text-success">Prev</span>
      </template>
      <template v-slot:next-text>
        <span class="text-success">Next</span>
      </template>
      <template v-slot:last-text>
        <span class="text-success">Last</span>
      </template>
      <template v-slot:ellipsis-text>
        <b-spinner small type="grow"></b-spinner>
        <b-spinner small type="grow"></b-spinner>
      </template>
      <template v-slot:page="{ page, active }">
        <b v-if="active">{{ page }}</b>
        <span v-else>{{ page }}</span>
      </template>
    </b-pagination-nav>
  </div>
</template><script>
export default {};
</script>
```

我们填充插槽，使内容看起来像我们的方式。

我们设置了`first-text`槽来设置进入第一页的文本。

`prev-text`槽被设置为改变链接的文本以转到上一页。

`next-text`槽被设置为改变下一步按钮的文本。

`last-text`用于设置转到最后一页的链接的文本。

`ellipsis-text`槽用来改变省略号的内容。

我们把它改成了闪光点。

`page`显示当前页面,`active`显示当前页面是否有效。

![](img/7a8006a7838ec1af1458d646fdcee1b5.png)

马修·布罗德尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以用自己的内容定制分页导航按钮。

此外，我们可以自定义链接的路径或查询字符串。