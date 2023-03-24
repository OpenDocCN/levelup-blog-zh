# 允许用户使用此角度指令编辑任何元素的内容

> 原文：<https://levelup.gitconnected.com/allow-users-to-edit-the-content-of-any-element-with-this-angular-directive-7b8fdb5bb800>

![](img/ea6cf363f00eae2467eaa07e45a1aea9.png)

图片鸣谢: [elnurfreepik](https://www.freepik.com/elnurfreepik) @Freepik

作为一个兼职项目，我正在用 Angular 开发一个笔记/学习辅助应用程序。我的一个主要目标是使它尽可能的用户友好，所以我决定做一个角度指令，它有如下作用:

1.  允许用户点击文本来编辑内容
2.  可以通过要求用户首先点击编辑图标来限制编辑，实质上是进入编辑模式
3.  检查当输入失去焦点或当用户点击“Enter”时文本是否被修改
4.  保存修改过的文本

# 该指令具有以下属性:

```
// Use this variable to store the original value
  value: string; // Use this input if you want to restrict editing to only be allowed in edit mode 
  @Input() allowEdit: boolean = true; // Use this input if you are saving the change and need to reference the identifier of the change
  @Input() id: number; // Use this input to indicate what field is being changed
  @Input() update: string; // Use this output to notify the parent that a change has been made
  @Output() onChange: EventEmitter<EditUpdate> = new EventEmitter();
```

# 对于构造函数，您需要:

```
// Automatically get a reference to the element you're changing with ElementRef
  private el: ElementRef // Safely update the element without accessing the DOM directly by using Renderer2
  private renderer: Renderer2
```

# 使用 HostListener 检测事件

使用 [HostListener](https://angular.io/api/core/HostListener) 的好处在于，你不必跟踪谁在发送事件，因为顾名思义，它是你添加了指令的任何对象的主机。(对我来说，这才是指令的真正力量！)

这意味着不再有多余的代码来单独分配事件侦听器，然后将它们挂接到包含回调的函数，这些回调是在事件发生时您希望发生的。不再需要构建层次关系，这样就可以相对于周围的元素来修改元素！

对于这个指令，我有三个 HostListeners，因为我想检测用户何时单击元素，何时离开元素，以及何时按下键盘上的“Enter”。

# 检测点击事件的主机监听器

如果您想切换编辑功能，可以在处理 click 事件的函数周围包装一个 If 语句。因为父元素决定了元素是否应该是可编辑的，所以您不必担心切换这个变量。(我将在本文后面介绍如何重置变量。)

```
@HostListener("click") onClick() {
  if (this.allowEdit) {
    this.makeEditable();
  }
}
```

# 默认情况下使编辑不自动的一些原因

1.  您希望只允许某些具有适当权限的用户编辑文本。
2.  您希望文本在未被编辑时是可选的。
3.  您希望限制意外编辑的可能性。

# HostListeners 检测用户何时完成编辑

当第一次制定这个指令时，我花了很多时间创建一个保存/撤销按钮交互，它隐藏/显示不同模式下的其他功能，但后来放弃了这种方法，因为它要求用户不必要地单击。毕竟，没有什么比因为忘记点击保存而丢失所有工作更令人沮丧的了。

我创建了一个用于当用户点击其他地方的时候，另一个用于监听“回车”键。

```
@HostListener('blur') blurred() {
  this.checkValues();
}@HostListener('keydown', [('$event')]) onKeydown(event) {
  if (event.keyCode === 13) {
     event.preventDefault();
     this.checkValues(); }
}
```

# 进入编辑模式

你可以使用 [ElementRef](https://angular.io/api/core/ElementRef) 来识别元素，而不是查询 DOM 和 [Renderer2](https://angular.io/api/core/Renderer2) 来操作它。这个函数简单地将编辑类添加到元素中，从而向用户提供可视的反馈，表明元素现在是可编辑的了。

```
makeEditable() {
  this.renderer.addClass(this.el.nativeElement, "editing");
  this.renderer.setAttribute(this.el.nativeElement, "contentEditable", "true");
  this.el.nativeElement.focus();
  this.setValue(this.el.nativeElement.innerText);
}
```

# 检查是否进行了编辑

接下来，将[‘内容可编辑’](https://www.w3schools.com/tags/att_global_contenteditable.asp)属性设置为‘真’。然后，给它焦点，保存内部文本的值。

在用户完成编辑后，我们将比较内部文本的值和之前的文本，看看是否有任何变化。这样，只有当有差异时，我们才会将数据发送给父节点，从而减少不必要的数据库访问次数。

```
checkValues(): Observable<EditUpdate> {
  const newValue = this.el.nativeElement.innerText; if (this.value !== newValue) {
    const edit: EditUpdate = {
    id: this.id,
    update: this.update,
    value: newValue
    }
    this.onChange.emit(edit);
  } this.reset();
  return this.onChange;
}
```

# 退出编辑模式

不要忘记通过移除“editing”类并将“contentEditable”设置为“false”来重置编辑模式

```
reset() {
  this.editing = false;
  this.renderer.removeClass(this.el.nativeElement, "editing");
  this.renderer.setAttribute(this.el.nativeElement, "contentEditable", "false");
}
```

# 最终产品

这是全部的代码。希望你喜欢！