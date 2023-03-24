# 下拉菜单、日期选择器和弹出菜单的顶级 Vue 包

> 原文：<https://levelup.gitconnected.com/top-vue-packages-for-dropdown-date-pickers-and-popups-9503a83509d9>

![](img/db72060239e3b1b5fdaeae45cc39f3c2.png)

卢卡·安布罗西在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在这篇文章中，我们将看看如何最好地添加一个带有层次选择、弹出窗口和自动建议输入的下拉菜单。

# 树选择

vue-treeselect 为我们提供了一个可以在我们的 vue 应用中使用的分层下拉列表。

要使用它，首先我们通过运行以下命令来安装它:

```
npm i @riophae/vue-treeselect
```

然后我们可以通过写来使用它:

```
<template>
  <div id="app">
    <treeselect v-model="value" multiple :options="options"/>
  </div>
</template>

<script>
import Treeselect from "@riophae/vue-treeselect";
import "@riophae/vue-treeselect/dist/vue-treeselect.css";export default {
  components: { Treeselect },
  data() {
    return {
      value: null,
      options: [
        {
          id: 1,
          label: "a",
          children: [
            {
              id: 2,
              label: "foo"
            },
            {
              id: 3,
              label: "bar"
            }
          ]
        },
        {
          id: 4,
          label: "baz"
        },
        {
          id: 5,
          label: "qux"
        }
      ]
    };
  }
};
</script>
```

我们有一个包含所有选项的`options`数组。

它们可以嵌套在`children`属性中。

这样，我们将看到它们列在下拉选项中。

`multiple`支持多重选择。

我们必须记住根据我们的选择设置`id`。

# vue-popperjs

vue-popperjs 是一个弹出组件，用于向元素添加工具提示。

要使用它，我们首先通过运行以下命令来安装它:

```
npm i vue-popperjs
```

然后我们可以通过写来使用它:

```
<template>
  <popper
    trigger="clickToOpen"
    :options="{
      placement: 'top',
      modifiers: { offset: { offset: '0,10px' } }
    }"
  >
    <div class="popper">Popper</div><button slot="reference">click me</button>
  </popper>
</template>

<script>
import Popper from "vue-popperjs";
import "vue-popperjs/dist/vue-popper.css";export default {
  components: {
    popper: Popper
  }
};
</script>
```

我们使用`popper`组件。

我们将`trigger`设置为`clickToOpen`以在点击时打开弹出窗口。

# Vue FlatPickr 组件

Vue FlatPickr 组件是一个日期时间选择器组件。

要安装它，我们运行:

```
npm i vue-flatpickr-component
```

然后我们可以通过写来使用它:

```
<template>
  <div>
    <flat-pickr v-model="date"></flat-pickr>
    <p>{{date}}</p>
    <p></p>
  </div>
</template>

<script>
import flatPickr from "vue-flatpickr-component";
import "flatpickr/dist/flatpickr.css";export default {
  data() {
    return {
      date: null
    };
  },
  components: {
    flatPickr
  }
};
</script>
```

我们使用`flat-pickr`组件并将选择的值绑定到`date`状态。

`flat-pickr`将显示日期时间选择器。

我们也可以用我们自己的配置来定制它。

例如，我们可以写:

```
<template>
  <div>
    <flat-pickr v-model="date" :config="config"></flat-pickr>
    <p>{{date}}</p>
    <p></p>
  </div>
</template>

<script>
import flatPickr from "vue-flatpickr-component";
import "flatpickr/dist/flatpickr.css";
import { French } from "flatpickr/dist/l10n/fr.js";export default {
  data() {
    return {
      date: null,
      config: {
        wrap: true,
        altFormat: "M j, Y",
        altInput: true,
        dateFormat: "Y-m-d",
        locale: French
      }
    };
  },
  components: {
    flatPickr
  }
};
</script>
```

我们更改`altFormat`来设置日期格式。

`dateFormat`是选择的日期格式。

`locale`设置区域设置。

`wrap`换行输入。

# vue-bootstrap-日期时间选择器

Vue Bootstrap 4 DatetimePicker 是一个我们可以使用的日期时间选择器。

要安装它，我们运行:

```
npm i vue-bootstrap-datetimepicker
```

然后，我们通过编写以下内容来使用该组件:

```
<template>
  <div class="container">
    <div class="row">
      <div class="col-md-12">
        <date-picker v-model="date" :config="options"></date-picker>
        <p>{{date}}</p>
      </div>
    </div>
  </div>
</template>

<script>
import "bootstrap/dist/css/bootstrap.css";
import datePicker from "vue-bootstrap-datetimepicker";
import "pc-bootstrap4-datetimepicker/build/css/bootstrap-datetimepicker.css";export default {
  data() {
    return {
      date: new Date(),
      options: {
        format: "DD/MM/YYYY",
        useCurrent: false
      }
    };
  },
  components: {
    datePicker
  }
};
</script>
```

我们使用`date-picker`组件来添加我们的日期选择器输入。

`v-model`将选择的日期绑定到`date`变量。

`options`让我们添加选项。

我们必须导入 CSS 来使它看起来正确。

它使用 Bootstrap 的样式来设计日期选择器。

# vue-自动建议

vue-autosuggest 是一个输入组件，为我们提供输入内容的建议。

要使用它，首先我们通过运行以下命令来安装它:

```
npm i vue-autosuggest
```

然后我们通过书写来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueAutosuggest from "vue-autosuggest";
Vue.use(VueAutosuggest);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div class="container">
    <vue-autosuggest
      :suggestions="[{data:['apple', 'orange' ,'grape']}]"
      :input-props="{id:'autosuggest', placeholder:`What's your favorite fruit?`}"
    >
      <template slot-scope="{suggestion}">
        <span class="my-suggestion-item">{{suggestion.item}}</span>
      </template>
    </vue-autosuggest>
  </div>
</template>

<script>
export default {
  data() {
    return {};
  }
};
</script>
```

我们用`vue-autosuggest`组件添加输入。

然后我们填充缺省槽来显示我们的选择。

![](img/f5b456a3809e75343080934692f0023f.png)

照片由[乔舒亚·柯尔曼](https://unsplash.com/@joshstyle?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 结论

vue-treeselect 允许我们添加一个带有分层选择的下拉列表。

vue-popperjs 允许我们添加弹出窗口。

vue-bootstrap-datetimepicker 是一个简单的日期选择器，使用引导样式。

vue-autosuggest 是一个自动建议输入，我们可以使用 simp，y。