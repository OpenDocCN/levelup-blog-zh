# BootstrapVue —文本输入

> 原文：<https://levelup.gitconnected.com/bootstrapvue-text-inputs-661b26454e9b>

![](img/c97344d69580585e97791f1652239070.png)

[Jason Leung](https://unsplash.com/@ninjason?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

为了制作好看的 Vue 应用，我们需要设计组件的样式。为了使我们的生活更容易，我们可以使用内置样式的组件。

在这篇文章中，我们将看看如何添加文本输入框。

# 文本输入

我们可以用文本或其他值来创建输入，例如`text`、`password`、`number`、`url`、`email`、`search`、`range`、`date`等等。

例如，我们可以通过编写以下代码添加一个带有`b-form-input`组件的:

```
<template>
  <div id="app">
    <b-form-input v-model="text" placeholder="Enter your name"></b-form-input>
    <div>{{ text }}</div>
  </div>
</template><script>
export default {
  data() {
    return {
      text: ""
    };
  }
};
</script>
```

`v-model`将`text`状态绑定到输入值。

然后我们在它下面显示`text`值。

当我们输入一些东西时，我们会看到它显示在它的下面。

# 输入类型

我们可以把它设置成不同的类型。

我们可以将`type`属性设置为以下值:

*   `text`
*   `password`
*   `number`
*   `url`
*   `email`
*   `search`
*   `range`
*   `date`
*   `color`

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-input v-model="color" placeholder="Enter color" type="color"></b-form-input>
    <div>{{ color }}</div>
  </div>
</template><script>
export default {
  data() {
    return {
      color: ""
    };
  }
};
</script>
```

然后我们单击输入框，我们看到一个本地颜色选择器，我们可以在其中选择颜色。我们选好颜色后，就会显示出来。

`b-form-input`不支持`v-model.lazy`。而是应该使用`lazy`道具进行懒人评估。

`date`和`time`是原生浏览器类型。它们不是可定制的日期选择器。

# 范围类型输入

我们可以将`type`道具设置为`range`来得到一个滑块。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-input v-model="value" type="range" min="0" max="5"></b-form-input>
    <div>Value: {{ value }}</div>
  </div>
</template><script>
export default {
  data() {
    return {
      value: 0
    };
  }
};
</script>
```

得到一个滑块。

当我们滑动它时，因为有了`v-model`指令，所以`value`被更新。我们可以设置`steps`道具来改变滑动时的增量或减量。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-input  v-model="value" type="range" step="0.5" min="0" max="5"></b-form-input>
    <div>Value: {{ value }}</div>
  </div>
</template><script>
export default {
  data() {
    return {
      value: 0
    };
  }
};
</script>
```

# 控制尺寸

我们可以用`size`道具改变控件的大小。该值可以是`sm`或`lg`。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-input size="sm" placeholder="enter your name"></b-form-input>
  </div>
</template><script>
export default {};
</script>
```

然后我们得到一个额外的小输入框。

# 验证状态

如果输入值有效，我们可以显示绿色。否则，我们可以显示红色。

我们可以写:

```
<template>
  <div id="app">
    <b-form-input :state="valid" v-model="name" placeholder="name"></b-form-input>
  </div>
</template><script>
export default {
  computed: {
    valid() {
      return this.name.length > 0;
    }
  },
  data() {
    return {
      name: ""
    };
  }
};
</script>
```

当输入值为空时显示红色边框。

否则，我们显示绿色边框。`state`道具是验证状态。

# 格式程序

我们可以添加一个格式化函数，按照我们想要的方式格式化输入值。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-input :formatter="formatter" v-model="value" placeholder="enter some text"></b-form-input>
    <p>{{value}}</p>
  </div>
</template><script>
export default {
  data() {
    return {
      value: ""
    };
  },
  methods: {
    formatter(value) {
      return value.toLowerCase();
    }
  }
};
</script>
```

我们在`formatter`函数中添加了`formatter`道具组。在它里面，我们返回转换成小写的`value`状态。

然后当我们输入大写文本时，它会被转换成小写。

# 只读输入

我们可以用`readonly`属性将输入设为只读。

# 在类似数字的输入上禁用鼠标滚轮事件

可以将`no-wheel`属性设置为`true`来禁用这些输入上的鼠标滚轮事件。

# 数据列表

我们可以用`datalist`元素添加一个自动完成列表。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-input list="fruits" name="fruit"></b-form-input>
    <datalist id="fruits">
      <option>apple</option>
      <option>orange</option>
      <option>grape</option>
    </datalist>
  </div>
</template><script>
export default {};
</script>
```

`b-form-input`组件和`id`的`list`属性必须相同。

![](img/585cec457ab6af0193f9a5ae9af569c4.png)

照片由 [Kouji 鹤](https://unsplash.com/@pafuxu?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 去抖

我们可以添加一个`debounce`道具来延迟输入值的注册。

所以我们可以加上:

```
<b-form-input v-model="value" type="text" debounce="500"></b-form-input>
```

# 结论

我们可以用`b-form-input`组件添加多种文本输入。

该值可以被格式化和延迟。