# BootstrapVue —日期选择器自定义

> 原文：<https://levelup.gitconnected.com/bootstrapvue-datepicker-customization-2dfd92eed26d>

![](img/fa1254b36b8cd7b9ce6590380edcd30b.png)

照片由[布鲁克·卡吉尔](https://unsplash.com/@brookecagle?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

为了制作好看的 Vue 应用，我们需要设计组件的样式。为了使我们的生活更容易，我们可以使用内置样式的组件。

在本文中，我们将了解如何定制 BootstrapVue 日期选择器。

# 下拉布局

我们可以用`right`、`dropup`、`dropright`、`dropleft`、`no-flip`和`offset`道具来改变下拉位置。他们改变日期选择器的位置。

`no-flip`即使日期选择器被切断，也不移动它。

`offet`让我们移动日期选择器的位置。

剩下的让我们改变方向。

# 深色模式

我们可以将`dark`道具设置为`true`来启用黑暗模式。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-datepicker v-model="value" dark></b-form-datepicker>
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

将日期选取器的背景设为黑色。

# 仅按钮模式

默认情况下，当我们单击输入框时，会显示日期选择器。并且默认显示输入框。

要在单击日历图标时只显示日期选择器，我们可以使用`button-only`道具。这也将删除输入框。

所以，我们可以写:

```
<template>
  <div id="app">
    <b-form-datepicker v-model="value" button-only></b-form-datepicker>
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

则只显示日历图标。

我们可以点击它来查看日期选择器。

# 日期字符串格式

我们可以改变日期选择器的日期字符串格式。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-datepicker
      v-model="value"
      :date-format-options="{ year: 'numeric', month: 'short', day: '2-digit', weekday: 'short' }"
    ></b-form-datepicker>
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

我们添加了`date-format-options`道具。

我们传入的对象有`year`、`month`、`day`和`weekday`属性来设置所选的日期格式。

`'numeric'`表示是一个数字。

`'short'`表示我们展示的缩写。

`'2-digit'`表示我们显示 2 位数。

因此，我们应该看到所选择的日期有完整的年份、缩写的月份和工作日以及日期。

# 全幅日历下拉菜单

我们可以用`calendar-width`和`menu-class`支柱调整日历下拉宽度。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-datepicker v-model="value" menu-class="w-100" calendar-width="100%"></b-form-datepicker>
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

`menu-class`为`'w-100'`，使日期选择器填满屏幕宽度。

`calendar-width`是`'100%'`以便内容填满盒子。

# 国际化

我们可以更改日期选择器的区域设置。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-datepicker v-model="value" :locale="locale"></b-form-datepicker>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      value: undefined,
      locale: 'de'
    };
  }
};
</script>
```

我们将`locale`字段设置为`'de'`，以将日期选择器设置为德国地区。

现在我们将看到工作日和月份以德语显示。

我们还可以添加标签来改变文本的其余部分。

例如，我们可以写:

```
<template>
  <div id="app">
    <b-form-datepicker v-model="value" v-bind="labels.de" :locale="locale"></b-form-datepicker>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      value: undefined,
      locale: "de",
      labels: {
        de: {
          labelPrevDecade: "Vorheriges Jahrzehnt",
          labelPrevYear: "Vorheriges Jahr",
          labelPrevMonth: "Vorheriger Monat",
          labelCurrentMonth: "Aktueller Monat",
          labelNextMonth: "Nächster Monat",
          labelNextYear: "Nächstes Jahr",
          labelNextDecade: "Nächstes Jahrzehnt",
          labelToday: "Heute",
          labelSelected: "Ausgewähltes Datum",
          labelNoDateSelected: "Kein Datum gewählt",
          labelCalendar: "Kalender",
          labelNav: "Kalendernavigation",
          labelHelp: "Mit den Pfeiltasten durch den Kalender navigieren"
        }
      }
    };
  }
};
</script>
```

现在剩下的标签都是德语的。

这包括所有导航标签和说明。

同样，我们可以添加`weekday`字段来更改起始周日期。

`weekdays`为工作日添加标签。

`showDecadeNav`打开或关闭十年导航。

`hideHeader`隐藏页眉如果是`true`则隐藏页眉。

我们可以将它们设置如下:

```
<template>
  <div id="app">
    <b-form-datepicker
      :start-weekday="weekday"
      :show-decade-nav="showDecadeNav"
      :hide-header="hideHeader"
      v-model="value"
      v-bind="labels.de"
      :locale="locale"
    ></b-form-datepicker>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      value: undefined,
      locale: "de",
      labels: {
        de: {
          labelPrevDecade: "Vorheriges Jahrzehnt",
          labelPrevYear: "Vorheriges Jahr",
          labelPrevMonth: "Vorheriger Monat",
          labelCurrentMonth: "Aktueller Monat",
          labelNextMonth: "Nächster Monat",
          labelNextYear: "Nächstes Jahr",
          labelNextDecade: "Nächstes Jahrzehnt",
          labelToday: "Heute",
          labelSelected: "Ausgewähltes Datum",
          labelNoDateSelected: "Kein Datum gewählt",
          labelCalendar: "Kalender",
          labelNav: "Kalendernavigation",
          labelHelp: "Mit den Pfeiltasten durch den Kalender navigieren"
        }
      },
      showDecadeNav: false,
      hideHeader: false,
      weekday: 0,
      weekdays: [
        { value: 0, text: "Sunday" },
        { value: 1, text: "Monday" },
        { value: 6, text: "Saturday" }
      ]
    };
  }
};
</script>
```

我们加了一些道具来设置它们。

![](img/82c343e74efe55045ee37f3785cc6a06.png)

照片由 [AZGAN MjESHTRI](https://unsplash.com/@azganmjeshtri?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

日期选择器有许多自定义选项。

我们可以随意更改区域设置和标签。