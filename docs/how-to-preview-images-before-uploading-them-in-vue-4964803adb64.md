# 如何在上传到 Vue 之前预览图片

> 原文：<https://levelup.gitconnected.com/how-to-preview-images-before-uploading-them-in-vue-4964803adb64>

![](img/de106cd3b5fc4db00901576b76182466.png)

如今，大多数用户都希望在点击上传按钮之前看到他们选择上传的图片的预览。不提供这种行为有时意味着你的应用不够现代或者不够好。

您可以搜索具有这种行为的包。但是如果你想为你的应用程序定制一个解决方案，你可以在 Vue 中这样做。

在 Vue 中提供可重用解决方案的最佳方式之一是创建一个组件。因此，我们将创建一个名为`BaseImageInput`的组件，我们将使用它来保存图像的输入。

在你开始之前，你可以看看这个 [CodePen](https://codepen.io/tahazsh/pen/KrYwQb) 来看看我们将要构建什么:

# 准备项目

让我们用[即时原型](https://medium.com/@Taha_Shashtari/test-out-your-vue-component-ideas-in-no-time-with-instant-prototyping-5fdf22688c08)快速做好准备。我们需要两个 vue 文件: *App.vue* 和 *BaseImageInput.vue* 。

创建它们之后，使用以下命令从终端运行它:

```
vue serve App.vue
```

如果你不熟悉“即时原型制作”，我在本文中已经解释过了:[用即时原型制作立即测试你的 Vue 组件想法](https://medium.com/@Taha_Shashtari/test-out-your-vue-component-ideas-in-no-time-with-instant-prototyping-5fdf22688c08)。

# 实现 App.vue

在我们开始实现`BaseImageInput`之前，让我们看看它将如何在`App.vue`中使用:

```
<template>
  <div class="app">
    <base-image-input v-model="imageFile"/>
  </div>
</template><script>
**import** BaseImageInput **from** './BaseImageInput'**export** **default** {
  components: { BaseImageInput },
  data () {
    **return** {
      imageFile: null
    }
  }
}
</script>
```

在这个组件中，我们有`imageFile`数据属性来保存所选文件的值。此外，我们在这里使用的是 [v-model，而不是通过](https://medium.com/@Taha_Shashtari/how-to-make-your-custom-vue-components-support-v-model-ad92ef6dc23d) `[:value](https://medium.com/@Taha_Shashtari/how-to-make-your-custom-vue-components-support-v-model-ad92ef6dc23d)` [prop 传递 imageFile 并用](https://medium.com/@Taha_Shashtari/how-to-make-your-custom-vue-components-support-v-model-ad92ef6dc23d) `[@input](https://medium.com/@Taha_Shashtari/how-to-make-your-custom-vue-components-support-v-model-ad92ef6dc23d)`更新它。

# BaseImageInput 如何工作？

在我们开始向`BaseImageInput.vue`添加任何代码之前，让我们先看看它是如何工作的。

众所周知，在 HTML 中选择文件的方法是使用`<input type="file">`元素。因为这个元素不显示所选图像的预览，所以我们需要另一个元素来显示所选图像。

下面是我们的 HTML 将如何构造的快速概述:

```
<template>
  <div class="base-image-input">
    <span class="placeholder">Choose an Image</span>
    <input type="file">
  </div>
</template>
```

用户不会直接与`<input type="file">`交互。我们不会显示这个元素，但是我们将使用它来显示文件选择器并读取所选文件。

我们可以通过调用`click()`在任何想要的元素上执行点击操作。

考虑到这一点，我们将用`display: none`隐藏`<input type="file">`，当用户点击`.base-image-input`时，在上面调用`click()`。

这就引出了我们的下一个元素`.base-image-input`。这个元素不仅是我们组件的根元素，也是用户将看到并点击以选择图像的元素。当图像被选中时，这个元素会将图像显示为预览。

工作流程是这样的:

1.  用户点击`.base-image-input`。
2.  我们调用`<input type="file">`上的`click()`来打开文件选择器。
3.  一旦用户选择了图像，我们就在`<input type="file">`上监听`@input`，读取该文件，然后将它显示为`.base-image-input`的`background-image`。

最后，我们有`<div class="placeholder">`，当用户还没有选择任何图像时，我们将使用它来显示一个占位符。

所以最初，用户会看到占位符。当他/她选择图像时，我们将隐藏占位符，并将所选图像显示为`.base-image-input`上的背景图像。

# 实现 BaseImageInput.vue

现在我们了解了这个组件是如何工作的，让我们来实现它。

让我们从 HTML 部分开始:

```
<template>
  <div
    class="base-image-input"
    :style="{ 'background-image': `url(${imageData})` }"
    @click="chooseImage"
  >
    <span
      v-if="!imageData"
      class="placeholder"
    >
      Choose an Image
    </span>
    <input
      class="file-input"
      ref="fileInput"
      type="file"
      @input="onSelectFile"
    >
  </div>
</template>
```

关于此代码的注释:

*   我们使用`imageData`数据属性在`.base-image-input`上将所选图像显示为`background-image`。我们还用它来显示和隐藏占位符元素。
*   当用户点击`.base-image-input`时，我们应该调用`chooseImage`方法，在该方法中，我们将通过编程点击`<input type="file">`。
*   当用户选择一幅图像时，我们将调用`onSelectFile`方法。

根据这些注释，我们必须定义一个`imageData`数据属性和`chooseImage` & `onSelectFile`方法。

所以，让我们把这个放进`<script>`一节:

```
**export** **default** {
  data () {
    **return** {
      imageData: null
    }
  }, methods: {
    chooseImage () {},
    onSelectFile () {}
  }
}
```

先说比较简单的:`chooseImage`。

```
chooseImage () {
  **this**.$refs.fileInput.click()
}
```

我们只是在`<input type="file">`上调用`click()`来显示文件选择器。

现在，让我们实现`onSelectFile`方法:

```
onSelectFile () {
  **const** input = **this**.$refs.fileInput
  **const** files = input.files
  **if** (files && files[0]) {
    **const** reader = **new** FileReader
    reader.onload = e => {
      **this**.imageData = e.target.result
    }
    reader.readAsDataURL(files[0])
    **this**.$emit('input', files[0])
  }
}
```

在这个方法中，我们使用`FileReader`浏览器功能来读取所选文件作为`DataURL`。我们在文件输入上使用 [readAsDataURL](https://developer.mozilla.org/en-US/docs/Web/API/FileReader/readAsDataURL) 方法，该方法从`blob`本地创建一个临时 URL，作为 base64 编码的字符串，可以在`img` href 中读取，或者作为 CSS 中的`background-image`。一旦读取完成，我们将结果存储到`imageData`中。同样，我们使用这个值来显示图像并隐藏占位符。

我们实际上并不使用这些数据来上传图像(如果我们愿意，我们可以这样做)。相反，我们通过`input`事件传递选定的文件。这就是我们在`App.vue`中存储/使用的内容。

我们要做的最后一件事是为这个组件添加样式:

```
<**style** **scoped**>
.base-image-input {
  display: block;
  width: 200px;
  height: 200px;
  cursor: pointer;
  background-size: cover;
  background-position: center center;
}.placeholder {
  background: #F0F0F0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  color: #333;
  font-size: 18px;
  font-family: Helvetica;
}.placeholder:hover {
  background: #E0E0E0;
}.file-input {
  display: none;
}
</**style**>
```

现在，我们的自定义图像输入组件已经可以使用了！

[![](img/b62413b7c7ec47e9b1212d1f624904d7.png)](https://levelup.gitconnected.com/)[](https://gitconnected.com/learn/vue-js) [## 学习 Vue.js -最佳 Vue.js 教程(2019) | gitconnected

### 27 大 Vue.js 教程。课程由开发者提交并投票，让你找到最好的 Vue.js…

gitconnected.com](https://gitconnected.com/learn/vue-js)