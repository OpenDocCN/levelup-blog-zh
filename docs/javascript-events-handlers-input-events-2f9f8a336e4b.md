# JavaScript 事件处理程序—输入事件

> 原文：<https://levelup.gitconnected.com/javascript-events-handlers-input-events-2f9f8a336e4b>

![](img/1c99ae8c1658da3224101ab28a319fb5.png)

斯科特·布雷克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 JavaScript 中，事件是应用程序中发生的动作。它们是由各种事情触发的，比如输入、提交表单、调整大小等元素变化，或者应用程序运行时发生的错误等。我们可以分配一个事件处理程序来响应和处理这些事件。发生在 DOM 元素上的事件可以通过为相应事件的 DOM 对象的属性分配一个事件处理程序来处理。在本文中，我们将看看如何使用可编辑输入元素的`oninput`和`oninvalid`属性。

# oninput

DOM 元素的`oninput`属性允许我们分配一个事件处理程序来处理`input`事件，当`input`、`select`或`textarea`元素的值被更改时，该事件被触发。当启用了`contenteditable`属性的元素中有任何内容时，它也会被触发；当`designMode`打开时，它也会被触发。在`contentEditable`和`designMode`的情况下，事件目标是编辑主机。如果这些属性应用于多个元素，则编辑宿主是其父元素不可编辑的最近的祖先元素。

对于`type`属性设置为`checkbox`或`radio`的`input`元素，每当用户切换控件时，将触发`input`事件。然而，并非每个浏览器都是如此。

当值改变时，触发`input`事件，不像`change`事件只在输入值被提交时触发，例如当用户按下回车键或从选项列表中选择一个值时，等等。

例如，我们可以分配一个事件处理函数来记录输入事件的输入值，如下面的 HTML 代码所示:

```
<input placeholder="Enter some text" name="name"/>
<p id="value-log"></p>
```

然后在 JavaScript 代码中，我们可以写:

```
const input = document.querySelector('input');
const log = document.getElementById('value-log');const updateValue = (e) => {
  log.textContent = e.target.value;
}input.oninput = updateValue;
```

然后我们应该看到一个带有`p`元素的输入文本框，显示输入的值，因为它已经改变。

同样，我们可以将它用于一个`select`元素。首先，我们编写以下 HTML 代码:

```
<select name="name">
  <option value='Apple'>Apple</option>
  <option value='Orange'>Orange</option>
  <option value='Grape'>Grape</option>
</select>
<p id="value-log"></p>
```

然后 JavaScript 与我们之前的非常相似:

```
const select = document.querySelector('select');
const log = document.getElementById('value-log');const updateValue = (e) => {
  log.textContent = e.target.value;
}select.oninput = updateValue;
```

请注意，该值仅在我们进行选择时显示，因此当我们第一次加载页面时不会显示任何值，因为我们没有选择任何内容。

对于复选框，代码将与文本输入有很大的不同。如果我们想要记录选中的值，我们必须检查复选框是否被选中，然后获取值。

例如，如果我们有下面的一些复选框的 HTML 代码:

```
<input type='checkbox' value='Apple' name="name" id='0' /> Apple
<input type='checkbox' value='Grape' name="name" id='1' /> Grape
<input type='checkbox' value='Banana' name="name" id='2' /> Banana
<p id="value-log"></p>
```

然后，我们必须为每个复选框附加一个`oninput`事件处理函数，如下面的代码所示:

```
const checkboxes = document.querySelectorAll('input');
const log = document.getElementById('value-log');let choices = [];const updateValue = (e) => {  
  choices[e.target.id] = e.target.checked ? e.target.value : undefined;
  log.textContent = choices.join(' ');
}checkboxes.forEach((c, i) => {
  c.oninput = updateValue;
})
```

在上面的代码中，我们遍历了所有的复选框元素，并将`updateValue`事件处理函数设置为每个复选框元素的`oninput`属性，然后在`updateValue`函数中，我们通过检查具有给定 ID 的复选框的`checked` 值来设置`choices`数组中的条目，然后我们仅在通过检查`e.target.checked`属性来检查`choices`数组中的`value`时设置它。

对于单选按钮，这比处理复选框更容易，因为一组单选按钮只有一个可能的选择。HTML 代码类似于下面代码中的复选框:

```
<input type='radio' value='Apple' name="name" id='0' /> Apple
<input type='radio' value='Grape' name="name" id='1' /> Grape
<input type='radio' value='Banana' name="name" id='2' /> Banana
<p id="value-log"></p>
```

然后，在 JavaScript 代码中，我们将一个事件处理函数附加到每个单选按钮元素，如下面的代码所示:

```
const radioButtons = document.querySelectorAll('input');
const log = document.getElementById('value-log');const updateValue = (e) => {  
  log.textContent = e.target.value;
}radioButtons.forEach((c, i) => {
  c.oninput = updateValue;
})
```

在上面的代码中，我们直接在`value-log`元素中设置值，因为我们只需要当前选中的选项。

![](img/c9201f8a9b8b3959212b8d79ccef1618.png)

[史蒂夫·哈维](https://unsplash.com/@trommelkopf?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# on 无效

我们可以通过设置 DOM 元素的`oninvalid`属性来分配一个事件处理程序来处理`invalid`事件。当 submittable 元素被检查并且输入验证失败时，触发`invalid`属性。例如，当一个`input`元素的输入不符合 HTML 代码中定义的要求时，它将被触发。通过检查`input`或`textarea`元素的`required`和`pattern`属性来完成验证。

例如，我们可以用它来验证一个`form`元素的输入:

```
<form id="form">
  <label for="city">First Name</label>
  <input type="text" id="first-name" required>
  <p id="first-name-error" hidden>Please fill out first name.</p>
  <br> <label for="city">Last Name</label>
  <input type="text" id="last-name" required>
  <p id="last-name-error" hidden>Please fill out last name.</p>
  <br> <label for="city">Email</label>
  <input type="text" id="email" required pattern='[^@]+@[^\.]+\..+'>
  <p id="email-error" hidden>Please fill out email.</p>
  <br> <button type="submit">Submit</button>
</form>
```

然后在相应的 JavaScript 代码中，我们可以设置每个`input`元素的`oninvalid`属性来处理每个`input`元素的`invalid`事件，如下面的代码所示:

```
const form = document.querySelector('form');
const inputs = document.querySelectorAll('input');inputs.forEach(input => {
 input.oninvalid = (e) => {    
    document.getElementById(`${e.target.id}-error`).removeAttribute('hidden');
  }
})form.onsubmit = (e) => {
  console.log(e);
  e.preventDefault();
}
```

此外，我们必须调用`onsubmit`事件处理程序上的`preventDefault()`,因为我们不想在本例中的任何地方提交表单值。为了将`invalid`事件处理程序附加到所有的`input`元素，我们使用`document.querySelectorAll`方法获取所有的`input`元素，然后我们循环每个 DOM 元素对象，然后将`oninvalid`属性设置为我们的事件处理程序函数。在事件处理函数中，每当为`input`元素触发`invalid`事件时，我们只需关闭`hidden`属性。

DOM 元素的`oninput`属性允许我们分配一个事件处理程序来处理`input`事件，当`input`、`select`或`textarea`元素的值被更改时，该事件被触发。当元素中的任何内容启用了`contenteditable`属性时，它也会被触发；当`designMode`打开时，它也会被触发。我们可以用它来处理输入或选择值的情况。在提交输入之前，只要输入发生变化，就会运行事件处理函数。

我们可以通过设置 DOM 元素的`oninvalid`属性来分配一个事件处理程序来处理`invalid`事件。当 submittable 元素被检查并且输入验证失败时，触发`invalid`属性。例如，当一个`input`元素的输入不符合 HTML 代码中定义的要求时，它将被触发。通过检查`input`或`textarea`元素的`required`和`pattern`属性来完成验证。