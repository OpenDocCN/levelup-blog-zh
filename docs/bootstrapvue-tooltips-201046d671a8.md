# BootstrapVue —工具提示

> 原文：<https://levelup.gitconnected.com/bootstrapvue-tooltips-201046d671a8>

![](img/470b9b229b83fc5e536120fbae2acc73.png)

照片由[谷仓图片](https://unsplash.com/@barnimages?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我们看看如何使用`v-b-tooltip`指令来添加工具提示。

# 工具提示

我们可以用`v-b-tooltip`指令添加工具提示。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-button v-b-tooltip.hover title="Tooltip">Hover Me</b-button>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

我们的按钮上有`v-b-tooltip`指令。

`title`有内容提示。

`hover`修改器使工具提示在悬停时打开。

禁用元素上的工具提示必须由其包装元素触发。

# 配置

我们可以用修改器改变工具提示的显示位置。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-button v-b-tooltip.hover.bottomright title="Tooltip">Hover Me</b-button>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

我们用`bottomright`修改器在右下角显示工具提示。

其他可能的选择包括`top`、`topleft`、`topright`、`right`、`righttop`、`rightbottom`、`bottom`、`bottomleft`、`bottomright`、`left`、`lefttop`和`leftbottom`。

# 扳机

我们可以改变一些修改器触发工具提示的方式。

我们可以没有修饰符，这意味着当一个元素被悬停或聚焦时它被打开。

`hover`表示悬停时工具提示打开。

`click`表示点击时工具提示打开。

`focus`表示工具提示在焦点上打开。

我们可以写:

```
<template>
  <div id="app">
    <b-button v-b-tooltip.click title="Tooltip">Hover Me</b-button>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

现在由于`click`修改器的作用，工具提示会在点击时打开。

# 禁用元素

如果我们想在一个被禁用的元素上打开工具提示，我们必须触发包装器上的工具提示。

例如，我们可以写:

```
<template>
  <div id="app">
    <span v-b-tooltip title="Disabled tooltip">
      <b-button variant="primary" disabled>Disabled</b-button>
    </span>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

我们将`v-b-tooltip`放在 span 上，而不是禁用按钮上。

结果仍然是悬停或聚焦时禁用工具提示。

# 焦点和按钮

因为如果我们添加了`focus`触发器，那么`b-button`将被呈现为一个标签。

我们需要添加`tabindex='0'`属性。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-button href="#" tabindex="0" v-b-tooltip.focus title="Tooltip">Link</b-button>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

# 下次点击时关闭

我们需要添加`click`和`blur`修饰符来添加一个工具提示，它只在元素被单击时打开，当文档中的任何内容被单击或获得焦点时关闭。

例如，我们写道:

```
<template>
  <div id="app">
    <b-button v-b-tooltip.click.blur title="Tooltip">Link</b-button>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

# 标题

我们可以将`v-b-tooltip`指令的值设置为一个对象来设置工具提示的选项。

对象可以有`title`属性来设置标题。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-button v-b-tooltip="options">Link</b-button>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      options: {
        title: "hello <strong>world</strong>",
        html: true
      }
    };
  }
};
</script>
```

我们有`title`属性，里面有一些 HTML。

`html`被设置为`true`以便呈现 HTML。

# 变体

要改变样式，我们可以向对象添加`variant`属性。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-button v-b-tooltip="options">Link</b-button>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      options: {
        title: "hello <strong>world</strong>",
        html: true,
        variant: "info"
      }
    };
  }
};
</script>
```

那么`variant`就是`'info'`，所以背景会是绿色的。

其他可能的值包括:`'primary'`、`'secondary'`、`'warning'`、`'success'`等。

# 使工具提示不可交互

我们可以制作一个不与`noninteractive`道具交互的工具提示。

# 通过$root 事件隐藏和显示工具提示

要显示工具提示，我们可以写:

```
this.$root.$emit('bv::show::tooltip', 'trigger-button-id')
```

显示添加到 ID 为`trigger-button-id`的元素的工具提示。

要隐藏所有工具提示，我们可以写:

```
this.$root.$emit('bv::hide::tooltip')
```

`this`是组件。

# 通过$root 事件禁用和启用工具提示

我们可以通过编写以下内容来禁用所有工具提示:

```
this.$root.$emit('bv::disable::tooltip')
```

要通过元素 ID 禁用工具提示，我们可以写:

```
this.$root.$emit('bv::disable::tooltip', 'trigger-button-id')
```

为了通过 ID 启用工具提示，我们可以写:

```
this.$root.$emit('bv::enable::tooltip', 'trigger-button-id')
```

![](img/33f215cb5653bf4f773d0307e18588f1.png)

由 [Sam Dan Truong](https://unsplash.com/@sam_truong?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 结论

我们可以用`v-b-tooltip`指令给元素添加工具提示。

内容可以是纯文本或 HTML。