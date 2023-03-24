# JavaScript 和 Web——表单域和客户端存储

> 原文：<https://levelup.gitconnected.com/javascript-and-the-web-form-fields-and-client-side-storage-dc0557ec767c>

![](img/3824736a8809d974a90a0dd8dd421ae0.png)

尼古拉斯·雷蒙在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是世界上最流行的编程语言之一。为了有效地使用它，我们必须了解它的基本知识。

在本文中，我们将了解如何使用表单字段和本地存储。

我们还将了解如何使用会话存储来读取文件和存储数据。

# 挑选

select 元素让我们像单选按钮一样选择一样东西。

他们让用户从一组选项中选择。

单选按钮将选项的布局置于我们的控制之下，但是选择元素的外观由浏览器决定。

选择字段有一个类似复选框的变体。

我们可以添加`multiple`属性，让用户选择多个选项。

显示方式不同于普通的选择字段，后者显示为下拉列表。

`option`标签有一个值。每个值都有一个`value`属性。

如果没有添加`value`属性，选项内的文本将被计为值。

对于一个`multiple`领域来说，`value`属性帮不了我们太多。

如果我们有一个可以保存多个选择的 select 元素，我们可以访问选项标签的`selected`属性。

例如，给定以下 select 元素:

```
<select multiple>
  <option>apple</option>
  <option>orange</option>
  <option>banana</option>
  <option>grape</option>
</select><p></p>
```

我们可以写:

```
const select = document.querySelector("select");
const p = document.querySelector("p");
select.addEventListener("change", () => {
  const selected = [...select.options]
    .filter(o => o.selected)
    .map(o => o.value)
    .join(', ');
  p.textContent = selected;
});
```

我们获取 select 元素，然后将`select.options`转换成一个数组。

然后我们使用`filter`方法来获取选中的选项元素。

然后用`map`将它们的值放入一个数组。

然后我们将它们连接在一起，并用它填充`p`元素。

# 文件字段

文件字段允许我们通过表单从用户的机器上传文件。

田地让我们得到田地，做我们想做的事情。

例如，我们可以添加以下文件字段:

```
<input type="file">
```

然后，我们可以通过编写以下内容来获取文件值:

```
const input = document.querySelector("input");
input.addEventListener("change", () => {
  if (input.files.length > 0) {
    const {
      name,
      type
    } = input.files[0];
    console.log(name, type);
  }
});
```

文件对象具有带有文件名和类型的`name`和`type`属性。

`type`类似于`text/plain`。

一个文件字段可以支持使用`multiple`属性的多个文件提交。

我们可以用`FileReader`构造函数读取文件的内容。

例如，我们可以写:

```
const input = document.querySelector("input");
input.addEventListener("change", () => {
  for (const file of [...input.files]) {
    const reader = new FileReader();
    reader.addEventListener("load", () => {
      console.log(reader.result);
    });
    reader.readAsText(file);
  }
});
```

我们获取用`input.files`选择的文件，并用 spread 操作符将其转换成一个数组。

然后我们使用`FileReader`创建一个对象，我们可以监听文件的`load`事件。

然后我们使用创建的`reader`对象上的`readAsText`来读取文件。

# 在客户端存储数据

我们可以用本地存储在客户端存储数据。

为此，我们可以使用`localStorage`对象。

例如，要添加一个条目，我们可以使用`setItem`方法:

```
localStorage.setItem("fruit", "orange");
```

第一个参数是键，第二个是值。

该值必须是字符串。

然后我们就可以通过使用`getItem`得到物品了:

```
localStorage.getItem("fruit");
```

该值会一直存在，直到被覆盖、被`removeItem`删除或数据被清除。

浏览器对网站可以存储在本地存储器中的数据大小进行限制。

这可以防止数据占用太多空间。

还有一个类似于`localStorage`的物体叫做`sessionStorage`。

两者的区别在于`sessionStorage`的内容在每次会话结束时都会被遗忘。

这意味着当浏览器关闭时是清楚的。

![](img/5cef8f51f559c869dbcd1264958b7ace.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

选择字段可以让用户选择一个或多个选项。

文件字段让我们从用户那里获取文件。我们可以使用`FileReader`对象来读取文件。

本地存储允许我们在浏览器上存储数据，直到它被清除。

会话存储数据，直到会话关闭。