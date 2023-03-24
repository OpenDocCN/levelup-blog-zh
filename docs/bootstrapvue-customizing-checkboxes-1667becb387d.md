# BootstrapVue —自定义复选框

> 原文：<https://levelup.gitconnected.com/bootstrapvue-customizing-checkboxes-1667becb387d>

![](img/d182851ca77ad2a9099fea9576b94cc3.png)

迈克·本纳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

为了制作好看的 Vue 应用，我们需要设计组件的样式。为了使我们的生活更容易，我们可以使用内置样式的组件。

在本文中，我们将看看如何用 BootstrapVue 定制复选框。

# HTML 复选框文本

我们可以在复选框文本中使用 HTML。我们不使用`text`属性，而是使用`html`来设置文本。

然而，我们应该小心跨站脚本攻击，因为 HTML 在显示之前不会被净化。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-group label="fruits">
      <b-form-checkbox-group v-model="selected" :options="options" name="fruits"></b-form-checkbox-group>
    </b-form-group>
    <div>{{ selected }}</div>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      options: [
        { text: "orange", value: "orange", disabled: false },
        { html: "<b>apple</b>", value: "apple", disabled: false },        
      ],
      selected: []
    };
  }
};
</script>
```

我们有`html: “<b>apple</b>”`让‘苹果’变得大胆。

# 内嵌和堆叠复选框

我们可以在`b-form-checkbox-groip`上加`stacked`道具来叠加选项。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-group label="fruits">
      <b-form-checkbox-group stacked v-model="selected" :options="options" name="fruits"></b-form-checkbox-group>
    </b-form-group>
    <div>{{ selected }}</div>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      options: [
        { text: "orange", value: "orange", disabled: false },
        { text: "apple", value: "apple", disabled: false },        
      ],
      selected: []
    };
  }
};
</script>
```

然后，复选框将堆叠在一起，而不是并排。

# 胶料

我们可以改变复选框的大小。为此，我们可以使用`size`道具。可用选项有`sm`或`lg`

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-checkbox size="sm">Small</b-form-checkbox>
  </div>
</template>
<script>
export default {
  name: "App"
};
</script>
```

获得一个额外的小复选框。

# 按钮样式复选框

我们可以将复选框更改为按钮的样子。我们可以用`button`道具做到这一点。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-checkbox v-model="checked" name="checked" button>
      <b>{{ checked }}</b>
    </b-form-checkbox>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      checked: false
    };
  }
};
</script>
```

我们将`button`属性添加到`b-form-checkbox`中，然后将内容放入标签中。另外，我们可以添加`button-variant`来改变按钮的样式。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-checkbox v-model="checked" button-variant="info" name="checked" button>
      <b>{{ checked }}</b>
    </b-form-checkbox>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      checked: false
    };
  }
};
</script>
```

与其他 BootstrapVue 组件一样，`button-variant`接受一个变量字符串。`info`会使按钮变绿。

# 分组按钮样式复选框

像按钮一样，我们可以将按钮样式的复选框分组。我们可以使用`b-form-checkbox-group`来创建一组按钮样式的复选框。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-checkbox-group v-model="selected" :options="options" buttons></b-form-checkbox-group>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      selected: [],
      options: ["apple", "orange", "banana"]
    };
  }
};
</script>
```

我们有`buttons`道具将复选框组转换成一组按钮样式的复选框。此外，我们可以像定制按钮一样定制它们。

我们可以在他们身上使用`size`和`button-variant`道具。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-checkbox-group
      button-variant="primary"
      size="lg"
      v-model="selected"
      :options="options"
      buttons
    ></b-form-checkbox-group>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      selected: [],
      options: ["apple", "orange", "banana"]
    };
  }
};
</script>
```

我们用`size="lg"`和`button-variant='primary'`使按钮样式的复选框变大，使它们变成蓝色。

# 切换样式复选框

我们可以让复选框看起来像一个开关。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-checkbox v-model="checked" name="check-button" switch>{{ checked }}</b-form-checkbox>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      checked: false
    };
  }
};
</script>
```

我们添加了`switch`道具，使复选框看起来像一个拨动开关。

![](img/1e1a8729ac1a09fd1e718b854eb366a8.png)

由 [Victor Malyushev](https://unsplash.com/@malyushev?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 结论

我们可以用复选框做很多事情。

可以自定义文本。

我们也可以让每个复选框看起来像按钮。

它们的大小也可以改变。

我们也可以让复选框看起来像一个拨动开关。