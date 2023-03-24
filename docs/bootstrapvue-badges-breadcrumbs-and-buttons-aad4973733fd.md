# BootstrapVue —徽章、面包屑和按钮

> 原文：<https://levelup.gitconnected.com/bootstrapvue-badges-breadcrumbs-and-buttons-aad4973733fd>

![](img/90c88f3676d03cc26bb4f94a1c60607d.png)

照片由[迈克·本纳](https://unsplash.com/@mbenna?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

为了制作好看的 Vue 应用，我们需要设计组件的样式。

为了使我们的生活更容易，我们可以使用内置样式的组件。

在本文中，我们将了解如何使用 BootstrapVue 添加徽章、面包屑和按钮。

# 徽章

徽章是为任何内容添加上下文的小标签。

我们可以通过使用`b-badge`组件来使用它:

```
<template>
  <div id="app">
    <h2>Feature
      <b-badge>New</b-badge>
    </h2>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

我们只是把它直接添加到模板中，没有任何道具。

还有类似`primary`、`secondary`等变种。

我们可以添加`variant`道具来设置变体。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-badge variant="primary">New</b-badge>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

药丸徽章是比平常更圆的徽章。

我们只是添加了`pill`道具:

```
<template>
  <div id="app">
    <b-badge pill variant="primary">Primary</b-badge>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

# 面包屑

面包屑是一个导航小部件，显示通向当前页面的层次结构。

BootstrapVue 有`b-breadcrumb`组件让我们添加面包屑。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-breadcrumb :items="items"></b-breadcrumb>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      items: [
        {
          text: "Home",
          href: "#"
        },
        {
          text: "Settings",
          href: "#"
        },
        {
          text: "Library",
          active: true
        }
      ]
    };
  }
};
</script>
```

我们有一个包含文本和 URL 的`items`。

我们将其设置为`items`属性的值，它们将以相同的顺序显示。

我们可以使用`b-breadcrumb-item`组件手动放置物品。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-breadcrumb>
      <b-breadcrumb-item href="#foo">Foo</b-breadcrumb-item>
    </b-breadcrumb>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

然后，我们手动将面包屑项目放入面包屑中。

`href`是链接的 URL。

# 小跟班

BootstrapVue 带有多种按钮。

要添加一个引导按钮，我们可以使用`b-button`组件。

我们可以写:

```
<template>
  <div id="app">
    <b-button>Button</b-button>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

然后我们看到一个按钮显示为灰色。

我们可以使用`variant`道具来改变样式。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-button variant="success">Button</b-button>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

然后我们会看到一个绿色按钮。

## 大小

我们可以用`size`道具改变按钮的大小。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-button size="sm">Button</b-button>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

`sm`小的意思。

## 概述

我们也可以制作带有白色填充和彩色轮廓的按钮。

为此，我们给变量加上前缀`outline-`。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-button variant="outline-primary">Primary</b-button>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

然后我们得到一个蓝色轮廓和蓝色文字的按钮。

## 圆形按钮

我们可以添加`pill`道具，让按钮更加圆润。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-button pill>Button</b-button>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

我们将得到一个比默认样式更圆的按钮。

## 禁用按钮

此外，我们可以用`disabled`道具设置一个被禁用的按钮。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-button disabled>Button</b-button>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

然后我们得到一个不能点击的按钮。

## 受压状态

我们可以设置按钮的按下状态，使它们看起来被按下或没有被按下。

为了让他们看起来很紧张，我们可以写:

```
<template>
  <div id="app">
    <b-button :pressed="true">Button</b-button>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

我们使用了`pressed`道具，并将其设置为`true`以使其看起来被按压。

同样，我们可以将它设置为`false`来使它们不被按下。

![](img/4535662a3967815b4ce5a96a78995954.png)

照片由 [Luiz Eduardo Alves da Silva](https://unsplash.com/@luizduardin?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

使用 BootstrapVue，我们可以添加徽章和标签文本。

此外，我们可以添加带有面包屑和面包屑项目组件的面包屑。

BootstrapVue 还带有多种按钮。