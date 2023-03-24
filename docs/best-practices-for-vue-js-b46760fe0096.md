# Vue.js 的最佳实践

> 原文：<https://levelup.gitconnected.com/best-practices-for-vue-js-b46760fe0096>

![](img/6d59eb56c9934c30b6dc0254687d74bb.png)

由 [Marvin Meyer](https://unsplash.com/@marvelous?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将通过遵循一些最佳实践，来看看如何以一种易于维护的方式编写 Vue 应用。

# 在组件上使用数据属性

组件中的`data`属性应该是一个函数而不是一个对象，这样组件的状态就不会与外界共享。

例如，我们应该写:

```
<template>
  <div id="app">{{name}}</div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      name: "Jane"
    };
  }
};
</script>
```

这样`name`就不会与其他组件共享。

# 使用烤肉串盒

元件和组件应该是烤肉串外壳。此外，自定义事件名称会自动更改为 kebab case。

例如，我们可以编写以下代码来嵌套组件并发出新事件:

`components/CountButton.vue`:

```
<template>
  <div>
    <button @click="increment">Increment</button>
  </div>
</template><script>
export default {
  name: "count-button",
  methods: {
    increment() {
      this.$emit("increase-count");
    }
  }
};
</script>
```

`App.vue`:

```
<template>
  <div id="app">
    {{count}}
    <count-button @increase-count="count++"></count-button>
  </div>
</template><script>
import CountButton from "./components/CountButton.vue";
export default {
  name: "App",
  components: {
    CountButton
  },
  data() {
    return {
      count: 0
    };
  }
};
</script>
```

在上面的代码中，我们有:

```
this.$emit("increase-count");
```

发射一个`increase-count`事件，这是在烤肉串的情况下。我们还有`count-button`烤肉串。

# 向 v-for 指令添加密钥属性

用`v-for`渲染的项目需要一个`key` prop，用一个唯一的键作为值，这样 Vue 就能正确地识别它。如果我们需要在项目呈现后对其进行操作，这一点尤其重要。

例如，我们应该写:

```
<template>
  <div id="app">
    <div v-for="item in items" :key="item.id">{{ item.name }}</div>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      items: [{ id: 1, name: "Jane" }, { id: 2, name: "Mary" }]
    };
  }
};
</script>
```

在上面的代码中，我们在`id`字段中传递了一个带有唯一 ID 的`key`道具。

`key`属性值应该是唯一的，并且尽可能不是数组的索引。

![](img/09c2a1a21a5616b04af06b5da7eb6f0f.png)

照片由[米卡·鲍梅斯特](https://unsplash.com/@mbaumi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 不要混淆 v-for 和 v-if

`v-for`和`v-if`不能一起使用。检查数组或对象的每个条目是否都应该用`v-if`显示需要更多的计算能力。

相反，我们应该使用计算属性过滤掉不应该显示的项目，如下所示:

```
<template>
  <div id="app">
    <div v-for="todo in todoTasks" :key="todo.id">{{todo.description}}</div>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      tasks: [
        { id: 1, description: "eat", done: false },
        { id: 2, description: "drink", done: true },
        { id: 3, description: "sleep", done: true },
        { id: 4, description: "walk", done: false },
        { id: 5, description: "read", done: false }
      ]
    };
  },
  computed: {
    todoTasks() {
      return this.tasks.filter(t => !t.done);
    }
  }
};
</script>
```

在上面的代码中，我们用`todoTasks`属性返回一个过滤后的任务数组，这些任务的`done`设置为`false`。这样，我们就不必像用`v-for`渲染它们一样，用`v-if`一个一个地检查它们。

这比:

```
<template>
  <div id="app">
    <div v-if="!todo.done" v-for="todo in tasks" :key="todo.id">{{todo.description}}</div>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      tasks: [
        { id: 1, description: "eat", done: false },
        { id: 2, description: "drink", done: true },
        { id: 3, description: "sleep", done: true },
        { id: 4, description: "walk", done: false },
        { id: 5, description: "read", done: false }
      ]
    };
  }
};
</script>
```

如果编写了类似上面的代码，Vue CLI 会给我们一个警告。

# 结论

为了加速我们的应用程序并使它们更干净，我们应该对从其他数据中导出的数据使用计算属性。

此外，我们应该使用 kebab-case 来保证事件名称和组件的一致性。

渲染数组时，需要注意一些事情。我们不应该用`v-for`和`v-if`。相反，我们应该使用计算属性。此外，为了帮助 Vue 唯一地标识一个呈现的数组项，我们应该为每个条目传递一个带有唯一值的`key` prop。这通常应该是唯一的 ID。

`data`属性应该是组件中的函数，而不是对象。