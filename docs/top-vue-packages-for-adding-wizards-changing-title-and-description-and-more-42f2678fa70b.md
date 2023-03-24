# 用于添加向导、更改标题和描述等的顶级 Vue 包

> 原文：<https://levelup.gitconnected.com/top-vue-packages-for-adding-wizards-changing-title-and-description-and-more-42f2678fa70b>

![](img/0e56bdb0b1558aaab475234b15f29f2e.png)

照片由 [Alvan Nee](https://unsplash.com/@alvannee?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将看看添加向导、更改标题和元属性、使用 Moment 和添加百分比显示的最佳包。

# 好巫师

vue-good-wizard 是一个用于 vue 应用程序的向导插件。

要使用它，我们通过编写以下内容来安装它:

```
npm i vue-good-wizard
```

然后我们可以写:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueGoodWizard from "vue-good-wizard";Vue.use(VueGoodWizard);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div>
    <vue-good-wizard :steps="steps" :onNext="nextClicked" :onBack="backClicked">
      <div slot="page1">
        <h4>Step 1</h4>
        <p>This is step 1</p>
      </div>
      <div slot="page2">
        <h4>Step 2</h4>
        <p>This is step 2</p>
      </div>
      <div slot="page3">
        <h4>Review</h4>
        <p>review</p>
      </div>
    </vue-good-wizard>
  </div>
</template>

<script>
export default {
  name: "demo",
  data() {
    return {
      steps: [
        {
          label: "Step 1",
          slot: "page1"
        },
        {
          label: "Step 2",
          slot: "page2"
        },
        {
          label: "Review",
          slot: "page3",
          options: {
            nextDisabled: true
          }
        }
      ]
    };
  },
  methods: {
    nextClicked(currentPage) {
      console.log("next clicked", currentPage);
      return true;
    },
    backClicked(currentPage) {
      console.log("back clicked", currentPage);
      return true;
    }
  }
};
</script>
```

我们使用`vue-good-wizard`组件来创建向导。

`steps`道具有一个包含插槽名称和标签的数组。

每个条目的`slot`属性都有槽。

然后，我们通过在其中放入我们想要的东西来填充我们定义的槽。

`options`有我们可以在每个步骤上设置的选项。

它还发出事件，我们可以为它们设置监听器。

# 充满活力

我们可以使用 vue-headful 包来设置我们的应用程序的标题和元描述。

我们可以通过运行以下命令来安装它:

```
npm i vue-headful
```

然后我们写道:

```
<template>
  <div>
    <vue-headful title="headful title" description="headful description"/>
  </div>
</template>
```

去使用它。

`vue-headful`是设置元数据的组件。

`title`设置标题。

`description`设置 meta 标签的`description`属性。

我们还可以设置 meta 标签和 link 标签的其他属性。

# 简单日历

vue-simple-calendar 是 vue 应用程序的日历组件。

为了使用它，我们运行:

```
npm i vue-simple-calendar
```

安装软件包。

然后我们写道:

```
<template>
  <div id="app">
    <calendar-view :show-date="showDate">
      <calendar-view-header
        slot="header"
        slot-scope="t"
        :header-props="t.headerProps"
        [@input](http://twitter.com/input)="setShowDate"
      />
    </calendar-view>
  </div>
</template>
<script>
import { CalendarView, CalendarViewHeader } from "vue-simple-calendar";
import "vue-simple-calendar/static/css/default.css";
import "vue-simple-calendar/static/css/holidays-us.css";export default {
  name: "app",
  data() {
    return { showDate: new Date() };
  },
  components: {
    CalendarView,
    CalendarViewHeader
  },
  methods: {
    setShowDate(d) {
      this.showDate = d;
    }
  }
};
</script>
```

去使用它。

我们必须导入组件和 CSS 文件。

然后我们使用`calendar-view`来添加日历。

我们使用`calendar-view-header`组件来添加头部。

`showDate`设置要显示的默认日期。

# 简易饼图

vue-easy-pie-chart 让我们向我们的 vue 应用程序显示一个圆形的百分比显示。

我们通过运行以下命令来安装它:

```
npm i vue-easy-pie-chart
```

然后我们写道:

```
<template>
  <div>
    <vue-easy-pie-chart :percent="70"></vue-easy-pie-chart>
  </div>
</template>
<script>
import VueEasyPieChart from "vue-easy-pie-chart";
import "vue-easy-pie-chart/dist/vue-easy-pie-chart.css";export default {
  components: { VueEasyPieChart }
};
</script>
```

去使用它。

我们设置`percent`来显示百分比。

此外，我们已经导入了 CSS，使它看起来正确。

# 真空时刻

vue-momentjs 是 moment.js 的 vue 插件包装器。

我们可以通过运行以下命令来安装它:

```
npm install moment vue-momentjs
```

然后我们可以通过写来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import moment from "moment";
import VueMomentJS from "vue-momentjs";Vue.use(VueMomentJS, moment);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div>{{date}}</div>
</template>
<script>
export default {
  data() {
    return {
      date: this.$moment(new Date()).format("YYYY-MM-DD")
    };
  }
};
</script>
```

我们使用`this.$moment`属性来调用`moment`函数。

然后我们可以用它调用矩对象的任何其他方法。

![](img/b24dd5f1430efb375e0d1ad9ee8e7853.png)

[凯特·古](https://unsplash.com/@kategu?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

vue-good-wizard 是 vue 的向导组件。

vue-headful 允许我们更改 vue 应用程序的标题和元描述。

vue-momentjs 是 moment 的一个小包装。

vue 简易饼图是一个百分比显示。