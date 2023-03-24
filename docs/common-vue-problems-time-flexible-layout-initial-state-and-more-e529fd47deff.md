# 常见的 Vue 问题—时间、灵活布局、初始状态等等

> 原文：<https://levelup.gitconnected.com/common-vue-problems-time-flexible-layout-initial-state-and-more-e529fd47deff>

![](img/12c1e6ddde47154ffc3f4b8bc0acbd8e.png)

Nik Shuliahin 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 让开发前端应用变得简单。然而，我们仍有可能遇到问题。

在这篇文章中，我们将看看一些常见的问题，并看看如何解决它们。

# 通过 Moment.js 使用 Vue

我们可以把`moment`和 Vue 一起用。

例如，我们可以写:

```
methods: {
  moment() {
    return moment();
  }
},
```

将`moment`的全局版本放入组件的范围。

此外，使用它们来格式化过滤器中的日期也是一个好主意。

我们可以这样写:

```
filters: {
  moment(date) {
    return moment(date).format('MMMM Do YYYY, h:mm:ss a');
  }
}
```

然后我们可以写:

```
<span>{{ date | moment }}</span>
```

我们有`moment`函数来创建一个 Moment 对象，让我们格式化日期字符串。

此外，我们可以通过编写以下代码来添加`moment` 作为全局 Vue 变量:

```
import moment from 'moment'Vue.prototype.$moment = moment
```

然后我们可以在所有的 Vue 实例和组件中使用`this.$monent`来访问它。

# 将道具作为初始数据传递的正确方法

要将属性作为初始数据传入，我们可以访问数据中的属性，并将其设置为返回对象中的属性值。

例如，我们可以写:

```
props: ['initialCounts'],
data() {
  return {
    count: this.initialCounts
  }
}
```

然后当我们传入`initialCount`属性时，`this.count`将被设置为属性的值。

此外，我们可以使用`Vue.util.extend`来制作道具的副本，而不是直接将它们赋值为属性值。

例如，我们可以写:

```
props: ['initialCounts'],
data() {
  return {
    count: Vue.util.extend({}, this.initialCounts)
  }
}
```

同样，我们可以使用`Object.assign`或 spread 操作符来克隆道具。

所以我们可以写:

```
props: ['initialCount'],
data() {
  return {
    count: Object.assign({}, this.initialCounts)
  }
}
```

或者:

```
props: ['initialCounts'],
data() {
  return {
    counts: { ...this.initialCounts }
  }
}
```

这样，当我们操作这些值时，就不会意外地改变道具的值。

# 获取 Vue 组件中的元素

我们可以通过几种方式在 Vue 组件中获取元素。

我们可以使用`this.$children`来访问一个组件的子组件。

同样，我们可以使用`this.$el.querySelector`来获取组件中的元素。

此外，我们可以用一个名称字符串为一个组件或元素指定一个引用。

例如，我们可以写:

```
ref="elementName"
```

然后我们可以通过在组件代码中使用`this.$refs.elementName`来访问它。

# 创建允许不同布局的组件

为了创建一个可以显示不同布局的组件，我们可以向组件添加插槽，这样我们就可以用我们想要的东西填充它。

例如，我们可以写:

`layout.vue`

```
<template>
  <div>
    <header>header</header> <slot/> <footer>
      footer
    </footer>
  </div>
</template>
```

我们有一个带有页眉和页脚的组件。

在它们之间，我们有`slot`元素，它可以被填充到组件标签之间的任何地方。

然后我们可以通过写来使用它:

```
<template>
  <layout>
    <p>Hello</p>
  </layout>
</template>
```

这样`slot`就会被替换成`<p>Hello</p>`。

# 为每个组件实例设置唯一的 ID

一个组件在`this._uid`属性中已经有了唯一的 ID。

但是，这是内部使用的，所以我们应该保密。

如果我们想要一个 ID，我们可以创建自己的 ID:

```
let uuid = 0;export default {
  beforeCreate() {
    this.uuid = uuid.toString();
    uuid += 1;
  },
};
```

我们有所有实例共享的`uuid`变量。

所以我们可以在创建组件之前，通过在`beforeCreate`钩子中添加 ID 生成代码，将它设置为`this.uuid`。

当我们创建更多的实例时，我们只是增加`uuid`。

# 参考文献的多种含义

我们可以将`ref`属性分配给一个组件，为它添加一个 ID，我们可以用它来调用组件上的方法。

如果一个 ref 被赋给了一个 DOM 元素，那么我们可以对它调用 DOM 方法。

如果它被赋给了一个组件，那么我们就可以对它调用组件的方法。

引用可以在我们外部的 Vue 实例中访问。

它们不像数据属性那样是被动的。

![](img/bcb62da2302ac3344cd411f07739fb5d.png)

照片由 [Nathan Shively](https://unsplash.com/@shivelycreative?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以通过把`moment`放在一个方法中，在 Vue 组件中使用它的全局版本。

我们可以用一个共享变量设置 Vue 组件的 ID。

此外，我们可以使用插槽来用自定义布局填充组件。