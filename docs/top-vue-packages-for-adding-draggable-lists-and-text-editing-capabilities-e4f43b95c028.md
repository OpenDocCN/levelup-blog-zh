# 用于添加可拖动列表和文本编辑功能的顶级 Vue 包

> 原文：<https://levelup.gitconnected.com/top-vue-packages-for-adding-draggable-lists-and-text-editing-capabilities-e4f43b95c028>

![](img/c37b47450a88b8fd80001ffd2c59981a.png)

gfg 图片由 [Sharon McCutcheon](https://unsplash.com/@sharonmccutcheon?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将看看如何将可拖动列表添加到 Vue 应用程序中。

# vuedraggable

vuedraggable 包是一个我们可以用来添加可拖动列表到我们的应用程序的包。

要安装它，我们运行:

```
npm i vuedraggable
```

然后我们可以通过写来使用它:

```
<template>
  <div class="demo">
    <draggable v-model="arr" group="people" [@start](http://twitter.com/start)="drag=true" [@end](http://twitter.com/end)="drag=false">
      <div v-for="element in arr" :key="element.id">{{element.name}}</div>
    </draggable>
  </div>
</template>

<script>
import draggable from "vuedraggable";export default {
  components: {
    draggable
  },
  data() {
    return {
      arr: [
        {
          id: 1,
          name: "foo"
        },
        {
          id: 2,
          name: "bar"
        },
        {
          id: 3,
          name: "baz"
        }
      ]
    };
  }
};
</script>
```

我们注册了`draggable`组件。

然后我们把列表嵌套在里面。

我们还设置了我们的`start`和`end`处理程序，它们分别在我们开始和停止拖动时运行。

`v-model`绑定到`arr`的最新值。

现在我们可以拖放名字。

我们也可以配合`transition-group`使用:

```
<template>
  <div class="demo">
    <draggable v-model="arr" group="people" [@start](http://twitter.com/start)="drag = true" [@end](http://twitter.com/end)="drag = false">
      <transition-group name="list">
        <div v-for="element in arr" :key="element.id">{{element.name}}</div>
      </transition-group>
    </draggable>
  </div>
</template>

<script>
import draggable from "vuedraggable";export default {
  components: {
    draggable
  },
  data() {
    return {
      arr: [
        {
          id: 1,
          name: "foo"
        },
        {
          id: 2,
          name: "bar"
        },
        {
          id: 3,
          name: "baz"
        }
      ]
    };
  }
};
</script><style>
.list-enter-active,
.list-leave-active {
  transition: all 1s;
}
.list-enter, .list-leave-to {
  opacity: 0;
  transform: translateY(30px);
}
</style>
```

我们只是把它放在`draggable`组件内部，在拖放时添加过渡效果。

我们可以为页脚或页眉添加一个槽。

例如，我们可以写:

```
<template>
  <div class="demo">
    <draggable v-model="arr" group="people" [@start](http://twitter.com/start)="drag = true" [@end](http://twitter.com/end)="drag = false">
      <p slot="header">header</p>
      <div v-for="element in arr" :key="element.id">{{element.name}}</div>
      <p slot="footer">footer</p>
    </draggable>
  </div>
</template>

<script>
import draggable from "vuedraggable";export default {
  components: {
    draggable
  },
  data() {
    return {
      arr: [
        {
          id: 1,
          name: "foo"
        },
        {
          id: 2,
          name: "bar"
        },
        {
          id: 3,
          name: "baz"
        }
      ]
    };
  }
};
</script>
```

我们可以使用许多其他选项来配置它，包括更改呈现元素的标签、设置列表、查看移动事件等等。

# TinyMCE Vue

TinyMCE 有一个 Vue 版本，所以我们可以毫无困难地使用 TinyMCE 富文本编辑器。

要安装它，我们运行:

```
npm install --save @tinymce/tinymce-vue
```

然后我们可以通过写来使用它:

```
<template>
  <div class="demo">
     <editor
       api-key="no-api-key"
       :init="{
         height: 500,
         menubar: false,
         plugins: [
           'advlist autolink lists link image charmap print preview anchor',
           'searchreplace visualblocks code fullscreen',
           'insertdatetime media table paste code help wordcount'
         ],
         toolbar:
           'undo redo | formatselect | bold italic backcolor | \
           alignleft aligncenter alignright alignjustify | \
           bullist numlist outdent indent | removeformat | help'
       }"
     />
  </div>
</template>

<script>
import Editor from '@tinymce/tinymce-vue'export default {
   name: 'app',
   components: {
     'editor': Editor
   }
 }
</script>
```

我们只需导入组件并注册它。

然后我们指定我们的插件和其他选项。

我们可以添加`v-model`来将输入的值绑定到一个状态变量:

```
<template>
  <div class="demo">
    <editor
      api-key="no-api-key"
      :init="{
         height: 200,
         menubar: false,
         plugins: [
           'advlist autolink lists link image charmap print preview anchor',
           'searchreplace visualblocks code fullscreen',
           'insertdatetime media table paste code help wordcount'
         ],
         toolbar:
           'undo redo | formatselect | bold italic backcolor | \
           alignleft aligncenter alignright alignjustify | \
           bullist numlist outdent indent | removeformat | help'
       }"
      v-model="content"
    />
    <p v-html="content"></p>
  </div>
</template>

<script>
import Editor from "@tinymce/tinymce-vue";export default {
  name: "app",
  components: {
    editor: Editor
  },
  data() {
    return {
      content: ""
    };
  }
};
</script>
```

我们将`v-model`道具绑定到`content`。我们添加了 p 元素，并将`v-html`指令设置为`content`，以查看我们输入了什么。

现在，我们的应用程序中有了一个文本编辑器，没有太多麻烦。

![](img/6bb654fc36ec8acb7e13cabe735c0b43.png)

照片由 [Gena Okami](https://unsplash.com/@genaokami?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以使用 vuedraggable 库来使列表项可拖放。

TinyMCE 的 Vue 版本功能齐全，添加也很简单。