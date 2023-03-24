# BootstrapVue —延迟加载图像和输入组

> 原文：<https://levelup.gitconnected.com/bootstrapvue-lazy-load-images-and-input-groups-2e1a79f47ffa>

![](img/6c1e9839520e14e00b0463f0a9972e8a.png)

由 [Anthony DELANOIX](https://unsplash.com/@anthonydelanoix?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了制作好看的 Vue 应用，我们需要设计组件的样式。为了使我们的生活更容易，我们可以使用内置样式的组件。

在这篇文章中，我们将看看如何添加图像，只有当我们看到它们时才加载。我们还将了解如何添加输入组。

# 惰性加载的图像

延迟加载图像更有效，因为我们不需要预先加载所有的图像。

为此，我们可以使用`b-image-lazy`组件。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-img-lazy src="http://placekitten.com/200/200" alt="kitten"></b-img-lazy>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

现在，我们的应用程序将只加载屏幕上显示的图像。

# 输入组

我们可以创建输入组，这让我们可以将输入与其他组件组合在一起。

为了添加一个输入组，我们使用了`b-input-group`组件。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-input-group size="lg" prepend="$" append=".00">
      <b-form-input></b-form-input>
    </b-input-group>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

我们用`size="lg"`改变组的大小。

`prepend`让我们在输入的左边添加一些东西。

`append`让我们在输入的右边添加一些东西。

所以我们应该在左边看到美元符号，在右边看到. 00。为了更好地定制输入组，我们可以将代码放入插槽中。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-input-group>
      <template v-slot:append>
        <b-input-group-text>
          <strong class="text-success">!</strong>
        </b-input-group-text>
      </template>
      <b-form-input></b-form-input>
    </b-input-group>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

然后我们可以加上一个感叹号，右边是绿色的。

# 子组件

我们可以在`b-input-group-prepend`和`b-input-group-append`组件中添加子组件，在右边添加我们想要的任何东西。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-input-group>
      <b-input-group-prepend>
        <b-button variant="primary">left button</b-button>
      </b-input-group-prepend> <b-form-input type="text"></b-form-input> <b-input-group-append>
        <b-button variant="outline-secondary">right button</b-button>
      </b-input-group-append>
    </b-input-group>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

在`b-input-group`组件中有`b-input-group-prepend`和`b-input-group-append`组件。

这让我们可以添加用`prepend`和`append`道具无法添加的东西。

在上面的例子中，我们在每个组件中添加了按钮。

# 支持输入组中的表单控件

我们可以在`b-input-group`组件中添加以下组件:

*   `<b-form-input>`
*   `<b-form-textarea>`
*   `<b-form-select>`
*   `<b-form-file>`
*   `<b-form-rating>`
*   `<b-form-tags>`
*   `<b-form-spinbutton>`
*   `<b-form-datepicker>`
*   `<b-form-timepicker>`

# 复选和单选按钮

我们可以在`b-input-group-prepend`和`b-input-group-append`组件中添加复选框和单选按钮。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-input-group>
      <b-input-group-prepend is-text>
        <input type="checkbox">
      </b-input-group-prepend>
      <b-form-input></b-form-input>
    </b-input-group>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

我们在`b-input-geoup-prepend`组件中嵌套了一个复选框。

`is-text`为文本内容应用适当的内容。

我们也可以用同样的方法编写下面的代码来添加一个单选按钮:

```
<template>
  <div id="app">
    <b-input-group>
      <b-input-group-prepend is-text>
        <input type="radio">
      </b-input-group-prepend>
      <b-form-input></b-form-input>
    </b-input-group>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

# 自定义单选按钮、复选框或开关

上面的例子添加了本地单选按钮和复选框。

我们还可以添加自举风格的单选按钮，复选框或开关组件。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-input-group>
      <b-input-group-prepend is-text>
        <b-form-checkbox></b-form-checkbox>
      </b-input-group-prepend>
      <b-form-input></b-form-input>
    </b-input-group>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

在`b-input-group-prepend`组件中添加一个`b-form-checkbox`。

可以用同样的方法添加其他组件。

![](img/dc9eb1c24f2dbcdbee9092dbb55a60a1.png)

照片由[迈克·本纳](https://unsplash.com/@mbenna?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以用`b-img-lazy`组件添加延迟加载图像。

此外，我们可以添加输入组，在输入框的左边和右边添加组件。