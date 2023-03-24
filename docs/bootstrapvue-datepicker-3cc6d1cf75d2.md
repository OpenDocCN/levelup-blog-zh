# BootstrapVue —日期选择器

> 原文：<https://levelup.gitconnected.com/bootstrapvue-datepicker-3cc6d1cf75d2>

![](img/f3b05b5445c54196c7576f069adfe229.png)

马修·埃利斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了制作好看的 Vue 应用，我们需要设计组件的样式。为了使我们的生活更容易，我们可以使用内置样式的组件。

在这篇文章中，我们将看看如何添加一个日期选择器到我们的 Vue 应用程序。

# 日期选择器

BootstrapVue 自带日期选择器组件。我们可以使用它，而不是使用另一个库来添加一个。我们可以添加`b-form-datepicker`来添加日期选择器。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-datepicker v-model="value"></b-form-datepicker>
    <p>Value: '{{ value }}'</p>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      value: "2020-05-13"
    };
  }
};
</script>
```

我们将带有`v-model`指令的`b-form-datepicker`绑定到`value`字段。当我们选择一个日期时,`value`被更新为日期字符串。

定制它需要一些道具。我们可以禁用它，这意味着导航和日期选择都被禁用。`disabled`道具禁用日期选择器。

此外，我们可以将日期选择器设置为只读，这意味着我们可以浏览日期，但不能选择任何日期。`readonly`属性使日期选择器只读。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-datepicker v-model="value" :disabled="true"></b-form-datepicker>
    <p>Value: '{{ value }}'</p>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      value: "2020-05-13"
    };
  }
};
</script>
```

禁用日期选择器。

我们写道:

```
<template>
  <div id="app">
    <b-form-datepicker v-model="value" :readonly='true'></b-form-datepicker>
    <p>Value: '{{ value }}'</p>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      value: "2020-05-13"
    };
  }
};
</script>
```

将日期选取器设置为只读。

# 日期限制

我们可以限制使用`min`和`max`属性选择的日期范围。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-datepicker v-model="value" :min="min" :max="max"></b-form-datepicker>
    <p>Value: '{{ value }}'</p>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      value: "2020-05-13",
      min: new Date(2020, 4, 1),
      max: new Date(2020, 4, 31)
    };
  }
};
</script>
```

那么我们只能选择 2020 年 5 月份以内的日期。

# 禁用日期

我们不必限制日期的范围。我们可以根据其他一些选择标准禁用日期。`date-disabled-fn`属性需要禁用一个日期函数。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-datepicker v-model="value" :date-disabled-fn="dateDisabled"></b-form-datepicker>
  </div>
</template><script>
export default {
  name: "App",
  methods: {
    dateDisabled(ymd, date) {
      const weekday = date.getDay();
      return weekday >= 1 && weekday <= 5;
    }
  }
};
</script>
```

那么我们只能选择周末的日期。`date`是一个`Date`实例，所以我们可以像`getDay`一样调用它可用的方法。

# 验证状态

`b-form-datepicker`可以显示验证状态。我们只需向`state`属性传递一个值来显示它。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-datepicker v-model="value" :state="isValid"></b-form-datepicker>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      value: undefined
    };
  },
  computed: {
    isValid() {
      return new Date(this.value).getDay() === 1;
    }
  }
};
</script>
```

我们有一个计算属性`isValid`,它返回星期几是否为 1。1 表示今天是星期二。所以当我们选择星期二时，日期选择器是绿色的。否则就是红色。

验证状态仅在选择日期时显示。

# 式样

我们可以用`size`道具改变日期选择器的大小。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-datepicker v-model="value" size="sm"></b-form-datepicker>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      value: undefined
    };
  }
};
</script>
```

我们把`size`设为`'sm'`，比较小。或者，我们可以将其设置为`'lg'`，这是大的。占位符可以用`placeholder`道具设定。

我们可以写:

```
<template>
  <div id="app">
    <b-form-datepicker v-model="value" placeholder="Choose a date"></b-form-datepicker>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      value: undefined
    };
  }
};
</script>
```

显示“选择日期”占位符。

![](img/d32808e539097df083822f3dff169504.png)

由[杰西卡·福特尼](https://unsplash.com/@jessicamaephotographyga?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

`b-form-datepicker`让我们选择日期并展示出来。

它有许多定制选项。

我们可以设置用户可以选择的日期范围。

此外，我们可以改变大小和占位符。