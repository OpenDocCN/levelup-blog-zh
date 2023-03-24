# BootstrapVue —覆盖

> 原文：<https://levelup.gitconnected.com/bootstrapvue-overlays-8fe877abc3f4>

![](img/444e982229050ceb4fb0455b33faa4a9.png)

克里斯汀·门多萨在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了制作好看的 Vue 应用，我们需要设计组件的样式。

为了使我们的生活更容易，我们可以使用内置样式的组件。

在这篇文章中，我们将看看如何添加叠加到我们的应用程序。

# 覆盖物

我们可以使用`b-overlay`组件在视觉上隐藏特定的元素或组件及其内容。

从 BootstrapVue 2.7.0 开始就有了。

例如，我们可以通过编写以下内容来创建一个:

```
<template>
  <div id="app">
    <b-overlay :show="show">
      <b-card title="Card">
        <b-card-text>foo</b-card-text>
      </b-card>
    </b-overlay>
    <b-button @click="show = !show">Toggle overlay</b-button>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      show: false
    };
  }
};
</script>
```

我们在`b-overlay`组件中有一个`b-card`。

`b-overlay`需要一个`show`道具来让我们设置何时显示覆盖图。

当我们单击“切换覆盖”时，我们将在`true`和`false`之间切换`show`状态时，切换覆盖的开和关。

当显示覆盖图时，我们会看到卡片内容以灰色覆盖，中间有一个旋转器。

# 覆盖背景颜色

我们可以改变覆盖图的背景颜色。

例如，我们用`opacity`道具改变不透明度:

```
<template>
  <div id="app">
    <b-overlay :show="show" opacity="0.5">
      <b-card title="Card">
        <b-card-text>foo</b-card-text>
      </b-card>
    </b-overlay>
    <b-button @click="show = !show">Toggle overlay</b-button>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      show: false
    };
  }
};
</script>
```

我们设置`opacity`为 0.5，所以我们会看到一个半透明的覆盖。

此外，我们可以用`blur`道具改变背景的模糊方式:

```
<template>
  <div id="app">
    <b-overlay :show="show" blur="2px">
      <b-card title="Card">
        <b-card-text>foo</b-card-text>
      </b-card>
    </b-overlay>
    <b-button @click="show = !show">Toggle overlay</b-button>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      show: false
    };
  }
};
</script>
```

该值以像素为单位指定。

# 旋转造型

我们可以用各种道具改变旋转器的样式。

`spinner-type`道具让我们改变旋转器的类型。

`spinner-variant`让我们改变微调器的颜色。

`spinner-small`如果设置为`true`会使微调器变小。

我们可以写:

```
<template>
  <div id="app">
    <b-overlay show spinner-variant="primary" spinner-type="grow" spinner-small>
      <b-card title="Card">
        <b-card-text>foo</b-card-text>
      </b-card>
    </b-overlay>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

现在得到一个不同于默认的微调器。

我们有一个闪光点。

`primary`使微调器变成蓝色。

`spinner-small`使之变小。

# 圆角

我们可以把覆盖层的角弄圆。

为此，我们可以设置`rounded`道具。

可能的值有:

*   `true` —默认样式
*   `false` —无舍入
*   `'sm'` —小圆角
*   `'lg'` —大圆角
*   `'pill'` —药丸式圆角
*   `'circle'` —圆形或椭圆形
*   `'top'` —仅倒圆角的两个顶角
*   `'bottom'` —仅巡视底部两个角落
*   `'left'` —仅倒圆角左边的两个角
*   `'right'` —仅倒圆角 2 个右角

例如，我们可以写:

```
<template>
  <div id="app">
    <b-overlay rounded="circle" show>
      <b-card title="Card">
        <b-card-text>foo</b-card-text>
      </b-card>
    </b-overlay>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

因为我们设置`rounded`为`'circle'`，我们将看到一个椭圆形的覆盖。

# 自定义覆盖内容

我们可以定制覆盖的内容。

为此，我们填充了`overlay`插槽。

例如，我们写道:

```
<template>
  <div id="app">
    <b-overlay show>
      <b-card title="Card">
        <b-card-text>foo</b-card-text>
      </b-card>
      <template v-slot:overlay>
        <div>loading</div>
      </template>
    </b-overlay>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

然后我们在我们的覆盖图上看到“加载”而不是一个微调器。

# 居中覆盖内容

默认情况下，覆盖图的内容居中。

要禁用它，我们可以将`no-center`属性设置为`true`:

```
<template>
  <div id="app">
    <b-overlay no-center show>
      <b-card title="Card">
        <b-card-text>foo</b-card-text>
      </b-card>
    </b-overlay>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

现在微调器将显示在左上角。

![](img/b78a56274e612d437ca505ca51d477e4.png)

Annika Treial 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以添加覆盖图来掩盖其背后的内容。

覆盖可以定制做我们想要的。