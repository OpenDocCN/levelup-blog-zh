# 使用 vue-virtual-scroll-list 向我们的 Vue 应用程序添加虚拟滚动列表

> 原文：<https://levelup.gitconnected.com/add-a-virtual-scroll-list-to-our-vue-app-with-vue-virtual-scroll-list-c199dfc43029>

![](img/c8e3fcd5386945338b18104e524bbe0e.png)

[Lucrezia Carnelos](https://unsplash.com/@ciabattespugnose?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

vue 虚拟滚动列表库允许我们添加一个虚拟滚动列表到我们的应用程序中。

# 装置

我们可以通过运行以下命令来安装该软件包:

```
npm install vue-virtual-scroll-list --save
```

# 添加虚拟滚动列表

安装包后，我们可以通过添加带有组件的`virtual-list`组件来添加虚拟滚动列表，以显示一个项目。

为此，我们可以写:

`App.vue`

```
<template>
  <div>
    <virtual-list
      style="height: 360px; overflow-y: auto;"
      :data-key="'uid'"
      :data-sources="items"
      :data-component="itemComponent"
    />
  </div>
</template>

<script>
import Item from "./Item";
import VirtualList from "vue-virtual-scroll-list";export default {
  name: "root",
  data() {
    return {
      itemComponent: Item,
      items: [{ uid: 1, text: "abc" }, { uid: 2, text: "xyz" }]
    };
  },
  components: { "virtual-list": VirtualList }
};
</script>
```

`Item.vue`

```
<template>
  <div>{{ index }} - {{ source.text }}</div>
</template>

<script>
export default {
  name: "item-component",
  props: {
    index: {
      type: Number
    },
    source: {
      type: Object,
      default() {
        return {};
      }
    }
  }
};
</script>
```

在`App.vue`中，我们有`virtual-list`组件。

我们设置列表高度，并将 overflow-y 设置为 auto，这样我们就可以使列表可滚动。

`data-key`是 ID 的属性名。

`data-source`有数据的来源。

`data-component`是显示行的组件。

# 小道具

它有许多其他选项，我们可以通过道具来改变。

`keeps`道具是我们期望列表在真实 DOM 中持续呈现的项目数量。

`extra-props`让我们传入不在`data-sources`中的数据。

`estimate-size`这是调整滚动条的项目的估计大小。

`start`设置起始索引的滚动位置。

`offset`设置滚动位置停留偏移量。

`page-mode`设置是否使用文档对象滚动列表。

`item-tag`是呈现行的标记名。

`item-class`是要添加到行中的类名。

`item-style`有行的样式。

`header-tag`有表头的标记。

`header-class`有表头的标记。

`header-style`有页眉的样式。

我们还用`footer`替换了`header`来设置页脚的样式。

# 事件

`virtual-list`组件也发出一些事件。

滚动时发出`scroll`事件。

`totop`滚动到顶部或左侧时发出事件。发出`event`和`range`参数。

`tobottom`向下或向右滚动时发出事件。

调整项目大小时发出`resized`。发射`id`和`size`有效载荷。

# 结论

vue 虚拟滚动列表对于增加显示虚拟滚动列表是有用的。