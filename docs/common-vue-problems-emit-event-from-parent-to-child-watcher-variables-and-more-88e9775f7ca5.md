# 常见的 Vue 问题—从父节点向子节点发出事件、观察器变量等等

> 原文：<https://levelup.gitconnected.com/common-vue-problems-emit-event-from-parent-to-child-watcher-variables-and-more-88e9775f7ca5>

![](img/707d26232256e90e948b8d4c403b2f7a.png)

劳尔·popadineți 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Vue.js 让开发前端应用变得简单。然而，我们仍有可能遇到问题。

在这篇文章中，我们将看看一些常见的问题，并看看如何解决它们。

# 访问观察器中的变量

我们可以像使用其他方法一样访问观察器中的变量。

例如，我们可以写:

```
watch: {
  dates: { 
    handler(date) {    
      if (date.start) {
        this.params.from = new Date(date.start).toString()
      }
      if (date.end) {
        this.params.to = nbew Date(date.end).toString()
      }
    },
    deep: true
  }
}
```

我们在我们的观察器中得到`dates`对象的值，我们可以用它做一些事情。

`deep`设置为`true`意味着我们观察一个物体的所有属性。

# 以编程方式创建 Vue.js 插槽

没有记录在案的以编程方式创建插槽的方法。

但是，我们可以使用 render 函数来创建具有 slot 属性的元素。

例如，我们可以写:

```
Vue.component('parent', {
  render (createElement) {
    return createElement('child', [
      createElement('h1', { slot: 'parent' }, 'parent slot'),
      createElement('p', { slot: 'default' }, 'default slot')
    ])
  }
})
```

然后我们可以通过写来使用它:

```
Vue.component('child', {
  template: '<div><slot name="parent-slot" /><slot /></div>'
})
```

我们可以用`createElement`创建元素，它可以包含`slot`属性来创建命名的和默认的槽。

# 获取密钥属性的值

我们无法获取子元素中 key prop 的值。

相反，我们不得不叫它别的东西。

`key`是一个特殊的道具，Vue 使用它来跟踪用`v-for`渲染的组件和元素。

# 在 Vue 组件中下载 pdf

要在 Vue 组件中下载 PDF，我们可以在`Blob`构造函数中获得响应。

然后我们可以创建一个不可见的下载链接，然后通过编程点击它进行下载。

例如，我们可以写:

```
downloadFile() {
  this.$http.get('pdf/url', {responseType: 'arraybuffer'})
    .then(response => {
      let blob = new Blob([response.data], { type: 'application/pdf' })
      let link = document.createElement('a')
      link.href = window.URL.createObjectURL(blob)
      link.download = 'test.pdf'
      link.click()
    })
}
```

`this.$http`是 Axios HTTP 客户端。

`responseType`被设置为`'arraybuffer'`，这样我们就可以将其作为二进制文件下载。

然后我们将`response.data`放入数组中的`Blob`构造函数，并将 mime 类型设置为`'application/pdf'`。

然后我们用`createObjectURL`创建链接来下载创建的 blob。

然后我们将`link`的`download`属性设置为文件名。

最后，我们调用`click`点击链接来下载文件。

# Vue 中可选的父元素

我们可以使用`is`属性来动态设置元素的标签。

例如，我们可以写:

```
<a :href="link" :is="link ? 'a' : 'p'">text</a>
```

我们有`link`字段。如果是`true`，那么返回`'a'`将其设置为一个`a`元素。

否则，我们将它设置为一个`p`元素。

如果`link`是真的，我们就这么做。

# 从父节点向子节点发出事件

我们可以用`$emit`方法从父组件向子组件发出一个事件。

然而，没有明显的方法将事件从父节点发送到子节点。

然而，我们仍然可以这样做。

我们可以创建一个新的`Vue`实例，创建一个事件总线，然后将`Vue`实例传递给孩子来监听它。

例如，我们可以写:

```
export default {
  components: {
    Child
  },
  data() {
    return {
      bus: new Vue(),
      data: []
    }
  },
  methods: {
    loadMore() {
      this.bus.$emit('loadMore', {
        data: this.data
      });
    }
  }
}
```

我们在`bus`字段中有了`Vue`实例。

然后我们可以发出`loadMore`事件来发送监听来自`bus`事件的事件。

在我们的父模板中，我们写道:

```
<child :bus="bus"></child>
```

将`bus`字段传递给`child`组件。

然后在`child`的`created`钩子中，我们可以通过编写来监听`loadMore`事件:

```
created() {
  this.bus.$on('loadMore', (args) => {
    // do something with args.data
  });
}
```

`args`我们传入第二个参数`$emit`的对象。

当组件加载`$on`时，它会监听来自`this.bus`的`loadMore`事件。

![](img/86defa3b3aae783909857d55e1f50ee8.png)

[马丁·莫雷诺](https://unsplash.com/@memoreno?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

我们可以用渲染函数以编程方式创建插槽。

要将事件从父传递到子，我们可以将 Vue 实例传递给子，从父发出事件，并使用传递给子的 Vue 实例来监听它。