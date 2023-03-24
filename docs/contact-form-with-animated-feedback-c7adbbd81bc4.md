# SVG 交互以改善用户界面的 UX:带有动画反馈的联系表单

> 原文：<https://levelup.gitconnected.com/contact-form-with-animated-feedback-c7adbbd81bc4>

![](img/b764f48837625dcc4a330b50237f3d0e.png)

动画是给我们的网站带来活力的最好方式:它们帮助我们讲述故事，传达当时正在发生的事情。它们自然会引起我们的注意，所以学习如何以及何时应用它们是很重要的。如果我们以正确的方式使用动画，我们可以提高网站的可用性，如果我们滥用它们，我们会破坏用户体验。

**在本文中，我们将展示一个交互式动画的例子**，这些动画在用户采取行动后立即出现，可以帮助他们注意到结果。

使用 SVG 交互的一个好例子是表单验证。我们可以**让用户知道在他们与输入交互时发生了什么**。你可以在我们的 [Leniolabs](http://leniolabs.com) 网站上找到这个例子。

![](img/0dea4ff3e168cdce5a682b18eff3ebd8.png)

Leniolabs.com 与联系人的互动形式

在本例中，我们需要填写两个输入。一旦用户输入任何字母，绿色的复选图标将确认输入是有效的。这是一个很小的交互，我们可以通过向文本`input`或`textarea`添加`required` HTML5 属性来实现，如下所示:

```
<input type=”text” name=”name” id=”name” aria-required=”true” autocorrect=”off” **required**>
```

在 CSS 中，我们使用选择器+伪类:`input**:valid**`或`textarea**:valid**`，并在后台显示一个 SVG，该 SVG 仅在元素有效时显示:

```
input#name**:valid**, textarea**:valid** {
 background: white url(‘data:image/svg+xml,\
 <svg ae kw" href="http://www.w3.org/2000/svg" rel="noopener ugc nofollow" target="_blank">http://www.w3.org/2000/svg" width=”26" height=”26">\
 <circle cx=”13" cy=”13" r=”13" fill=”%23abedd8"/>\
 <path fill=”none” stroke=”white” stroke-width=”2" d=”M5 15.2l5 5 10–12"/>\
 </svg>’) no-repeat 98% 5px;
 border: 3px solid $lightgreen;
 transition: background ease 0.2s;
}
```

当在 CSS 中添加内联 SVG 时，使用名称空间`xmlns=”[http://www.w3.org/2000/svg](http://www.w3.org/2000/svg)"`很重要，否则图像不会显示。

背景的过渡将使 SVG 从其初始位置向右移动 98%,如 CSS 中的背景简写中所定义的。

在 JavaScript 中，我们可以检查这些验证，并在两个字段都完成时向表单(和 SVG)添加一个类，如果至少有一个输入为空，则添加另一个类。每个类将绑定到一个 CSS 动画:一个将使窗体摇动，信封从*发送*按钮中掉出；另一个会让信封直接飞到邮箱，红旗就起来了。

```
document.getElementById(‘sendBtn’).addEventListener(‘click’, function (e) { 
 if (submitName[‘0’].validity.valid && submitText[‘0’].validity.valid) {
 e.preventDefault();
  message.classList.remove(‘not-active’);
  message.classList.add(‘active’);
  mailbox.classList.add(‘active’);
 }
 else {
  message.classList.add(‘not-active’);
  formshake.classList.add(‘active’);
 }
});
```

**这里是完整的代码:**

[https://codepen.io/marianab/pen/xywxNg](https://codepen.io/marianab/pen/xywxNg)