# 处理鼠标事件的最佳 Vue 事件处理库

> 原文：<https://levelup.gitconnected.com/best-vue-event-handling-libraries-for-handling-mouse-events-c69160142eb8>

![](img/34cf9d29e6cd0f80f7d8150716130c1d.png)

照片由 [Ricky Kharawala](https://unsplash.com/@sweetmangostudios?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将研究处理各种鼠标事件的最佳 Vue 指令，这些事件不包含在 Vue 中。

# vue 可选

使用`vue-selectable`包，当我们点击一个给定的项目或者当我们将要点击它时，我们可以很容易地添加效果。

要安装它，我们可以运行以下命令:

```
npm install vue-selectable  --save
```

那么我们可以如下使用它:

```
<template>
  <div id="app">
    <div
      v-selectable="{ 
         selectedGetter, 
         selectedSetter, 
         selectingSetter
         }"
    >
      <div class="selection"></div>
      <div
        v-for="(n, i) in 100"
        :class="{ selected: !!selected[i], selecting: !!selecting[i] }"
        class="selectable"
        :key="n"
      >{{ n }}</div>
    </div>
  </div>
</template><script>
import selectable from "vue-selectable";export default {
  name: "App",
  data() {
    return {
      selected: [],
      selecting: []
    };
  },
  directives: { selectable },
  methods: {
    selectedGetter() {
      return this.selected;
    },
    selectedSetter(v) {
      this.selected = v;
    },
    selectingSetter(v) {
      this.selecting = v;
    }
  }
};
</script><style>
.selected {
  color: green;
}.selecting {
  color: red;
}
</style>
```

在上面的代码中，我们定义了`selectedGetter`来返回选择的值。

`selectedSetter`用于获取选中的值并将其设置为`this.selected`，这样我们就可以在选中的项目上获得高亮效果。

`selectingSetter`设置正在被选择的项目，这样当我们要通过点击它来选择某个项目时，我们可以添加一个效果。

在模板中，我们有一个带有`v-selectable`指令的包装器 div，它有一个带有我们定义为属性和值的方法的对象。

然后我们有一个带有类`selection`的 div 来显示选择。

在那下面，我们有一系列 div，包含我们想要显示的项目，它们只是从 0 到 100 的数字。

我们还为`selected`和`selecting`类定义了样式，它们分别有绿色和红色。

由于我们使用带有所选条件的`:class`绑定将类名绑定到条目，我们看到当我们单击列表中的条目时类名会相应地改变。

现在，当我们点击一个项目时，我们会看到当我们第一次点击它时，颜色会变成红色，然后变成绿色。

它还提供了其他选项，我们可以将这些选项放入我们设置为`v-selectable`指令值的对象中。

这些选项包括:

*   `disableTextSelection` —选择框激活时禁用浏览器文本选择的布尔值
*   `scrollingFrame` —元素的 DOM 节点，用于放置包含可选项目列表的滚动条
*   `scrollSpeed` —以像素表示滚动速度的数字
*   `scrollDistance` —以像素为单位表示距边框距离的数字
*   `scrollDocumentEnabled` —布尔值，表示选择项目时是否启用文档滚动
*   `renderSelected` —布尔值，表示将 CSS `selectedClass` 添加到当前选定的元素
*   `renderSelecting` —布尔值，表示将 CSS `selectedClass` 添加到所选元素中
*   `selectingClass` —带有 CSS 类的字符串，用于标记选择框下的项目。
*   `selectedClass` —使用 CSS 类的字符串来标记选定的项目
*   `overrideAddMode` —布尔值，表示选择框是否总是向选择添加项目。

![](img/c791ead7b95a4079dd48a5f32d2900ad.png)

兹登尼克·马赫切克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 点击助手

`vue-click-helper`包让我们用一个指令处理单击和双击事件。

我们可以通过运行以下命令来安装它:

```
npm i vue-click-helper
```

那么我们可以如下使用它:

```
<template>
  <div id="app">
    <div v-click-helper="clickHelper">click me</div>
  </div>
</template><script>
import vueClickHelper from "vue-click-helper";export default {
  name: "App",
  directives: {
    "click-helper": vueClickHelper
  },
  methods: {
    clickHelper(e, isDoubleClick) {
      if (isDoubleClick) {
        console.log("double click");
      } else {
        console.log("click");
      }
    }
  }
};
</script>
```

在上面的代码中，我们有接受事件参数`e`的`clickHelper`方法，该参数有鼠标事件对象，以及指示单击是单击还是双击的`isDoubleClick`布尔参数。

在模板中，我们有一个`v-click-helper`指令，它的值被设置为`clickHelper`方法。

然后，当我们双击“点击我”时，我们会看到`'double click'`已登录，当我们单击时，我们会看到`'click'`已登录。

# 结论

`vue-selectable`让我们为点击选择的项目添加效果。

`vue-click-helper`包让我们用一个指令处理单击和双击。