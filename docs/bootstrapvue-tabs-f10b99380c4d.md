# BootstrapVue —选项卡

> 原文：<https://levelup.gitconnected.com/bootstrapvue-tabs-f10b99380c4d>

![](img/090074f489c8aa24a320b15f31eefdfc.png)

托马斯·索贝克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了制作好看的 Vue 应用，我们需要设计组件的样式。

为了使我们的生活更容易，我们可以使用内置样式的组件。

我们来看看如何给我们的 Vue 应用添加标签。

# 制表符

我们可以用`b-tabs`组件在我们的应用程序中添加可制表的窗格。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-tabs content-class="mt-3">
      <b-tab title="First" active>
        <p>first tab</p>
      </b-tab>
      <b-tab title="Second">
        <p>second tab</p>
      </b-tab>
      <b-tab title="Disabled" disabled>
        <p>Bisabled tab</p>
      </b-tab>
    </b-tabs>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

我们添加了包含`b-tab`组件的`b-tabs`组件。

`b-tab`具有由`title`设置的标签标题。

我们可以单击选项卡标题转到选项卡。

`disabled`表示选项卡被禁用。

我们不能去禁用的选项卡。

# 卡片集成

我们可以把标签放进卡片里。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-card no-body>
      <b-tabs card>
        <b-tab title="One" active>
          <b-card-text>Tab 1</b-card-text>
        </b-tab>
        <b-tab title="Two">
          <b-card-text>Tab 2</b-card-text>
        </b-tab>
      </b-tabs>
    </b-card>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

我们用`no-body`道具禁用卡体。

然后我们可以通过将`b-tabs`放入`b-card`来显示选项卡内容。

我们可以使用`b-tab`中的`b-card-text`来显示卡片中的内容。

我们用`active`属性设置默认的活动选项卡。

`b-tabs`上的`card`支柱使其在卡片模式下工作，以便在卡片中看起来正确。

如果处于`card` 模式，那么`b-tab`中的子组件将应用`card-body`类。

例如，如果我们有:

```
<template>
  <div id="app">
    <b-card no-body>
      <b-tabs card>
        <b-tab title="One" active>
          <b-card-img bottom src="https://placekitten.com/200/200" alt="kitten"></b-card-img>
          <b-card-footer>kitten</b-card-footer>
        </b-tab>
        <b-tab title="Two">
          <b-card-text>Tab 2</b-card-text>
        </b-tab>
      </b-tabs>
    </b-card>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

然后里面所有东西都加了`card-body`类。

# 药丸

我们可以用`pills`道具以药丸形式显示标签:

```
<template>
  <div id="app">
    <b-card no-body>
      <b-tabs pills card>
        <b-tab title="Tab 1" active>
          <b-card-text>Tab 1</b-card-text>
        </b-tab>
        <b-tab title="Tab 2">
          <b-card-text>Tab 2</b-card-text>
        </b-tab>
      </b-tabs>
    </b-card>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

# 充满

`fill`支柱可用于按比例用拉环填充可用的水平空间。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-tabs fill>
      <b-tab title="Tab 1" active>
        <b-card-text>Tab 1</b-card-text>
      </b-tab>
      <b-tab title="Tab 2">
        <b-card-text>Tab 2</b-card-text>
      </b-tab>
    </b-tabs>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

# 调整

`justified`道具让我们使标签等宽:

```
<template>
  <div id="app">
    <b-tabs justified>
      <b-tab title="Tab 1" active>
        <b-card-text>Tab 1</b-card-text>
      </b-tab>
      <b-tab title="Tab 2">
        <b-card-text>Tab 2</b-card-text>
      </b-tab>
    </b-tabs>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

# 对齐

`align`道具可以让我们对齐标签。

可能的值有`left`、`center`和`right`。

因此，我们可以这样写:

```
<template>
  <div id="app">
    <b-tabs align="center">
      <b-tab title="Tab 1" active>
        <b-card-text>Tab 1</b-card-text>
      </b-tab>
      <b-tab title="Tab 2">
        <b-card-text>Tab 2</b-card-text>
      </b-tab>
    </b-tabs>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

# 选项卡控件的底部位置

我们可以使用`end`道具将选项卡控件放置在选项卡内容的下方:

```
<template>
  <div id="app">
    <b-card no-body>
      <b-tabs pills card end>
        <b-tab title="Tab 1" active>
          <b-card-text>Tab 1</b-card-text>
        </b-tab>
        <b-tab title="Tab 2">
          <b-card-text>Tab 2</b-card-text>
        </b-tab>
      </b-tabs>
    </b-card>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

将`pills`道具做成按钮的样子会更好看。

![](img/1d0c7780576a12fa83a5abe6de0fdf36.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Nitish Kadam](https://unsplash.com/@nitish007?utm_source=medium&utm_medium=referral) 拍摄的照片

# 结论

我们可以添加选项卡来显示选项卡式内容。

它还与卡集成。