# 使用设计模式和 execCommand 摆弄网页

> 原文：<https://levelup.gitconnected.com/using-design-mode-and-execcommand-to-fiddle-with-web-pages-6f4039f7f406>

![](img/e421d5e676a59c858f862b8c27eaad34.png)

照片由 [Remy Baudouin](https://unsplash.com/@philmouss?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

为了简化网页设计，现代浏览器内置了一种设计模式。我们可以通过将`document.designMode`属性设置为`'on'`来打开它。

此外，当页面处于设计模式时，我们可以用`document.execCommand`方法发送命令来改变页面。

在本文中，我们将看看如何使用`document.execCommand`方法在设计模式下动态改变网页。

# 使用

`document.execCommand`方法有 3 个参数。语法如下:

```
document.execCommand(aCommandName, aShowDefaultUI, aValueArgument)
```

这些参数是:

*   `aCommandName` —指定要运行的命令的名称的字符串。
*   `aShowDefaultUI` —一个布尔值，指示是否应显示默认用户界面。Mozilla 不支持这一功能
*   `aValueArgument` —为命令提供信息的字符串需要输入。例如，`insertImage`命令需要一个图像的 URL。

如果命令不受支持或被禁用，它将返回`false`。

我们可以从浏览器控制台或在我们的代码中运行命令。一旦我们刷新，更改将被清除。

# 命令

以下命令可以使用`execCommand`运行:

## **背景颜色**

更改所选块的背景颜色。

我们可以将选定的背景更改为蓝色，如下所示:

```
document.execCommand('backColor', false, 'blue');
```

## 大胆的

打开和关闭选定文本的粗体。

例如，我们可以如下使用它:

```
document.execCommand('bold')
```

## ClearAuthenticationCache

从缓存中清除身份验证凭据。

例如，我们可以运行:

```
document.execCommand("ClearAuthenticationCache","false");
```

以确保我们必须再次认证。

## contentReadOnly

将内容标记为只读或可编辑。需要一个布尔值作为值参数。Internet Explorer 不支持此功能。

我们可以像下面的代码一样使用它:

```
document.execCommand('contentReadOnly', false, true)
```

## **复制**

将当前选择复制到剪贴板。支持可能因浏览器而异。

例如，我们可以写:

```
document.execCommand('copy')
```

## **创建链接**

从选择中创建链接。value 参数中需要一个 URI 字符串来设置链接的`href`属性。URI 必须至少有 1 个字符，可以是空格。IE 还会创建一个带有`null`值的链接。

我们可以如下使用它:

```
document.execCommand('createLink', true, '[http://www.google.com'](http://www.google.com'))
```

## **切**

删除所选文本并将其复制到剪贴板。支持可能因浏览器而异。

我们可以如下使用它:

```
document.execCommand('cut')
```

## **减少字体大小**

在所选文本周围添加一个`small`标签。IE 不支持此命令。

例如，我们可以这样使用它:

```
document.execCommand('decreaseFontSize')
```

## 默认段落分隔符

更改在可编辑文本区域中创建新段落时使用的段落分隔符。

我们可以通过书写来使用:

```
document.execCommand('defaultParagraphSeparator', false, 'br')
```

将分离器改为`br`。

## **删除**

删除当前选择。我们可以通过书写来使用它:

```
document.execCommand('delete')
```

## enableAbsolutePositionEditor

切换抓取器，允许绝对定位的元素四处移动。在 Firefox 63 测试版或开发版中，默认情况下是禁用的。

我们可以通过运行来使用它:

```
document.execCommand('enableAbsolutePositionEditor', true, true)
```

## **字体名称**

更改所选内容或插入点的字体名称。

例如，我们可以这样使用它:

```
document.execCommand('fontName', true, 'Arial')
```

## **前景色**

更改所选内容或插入点的字体颜色。十六进制颜色值需要作为值参数。

例如，我们可以写:

```
document.execCommand('foreColor', true, '#4287f5')
```

## 格式块

在包含当前选择的行周围添加一个 HTML 块级元素。

我们可以通过书写来使用它:

```
document.execCommand('formatBlock', true, 'blockquote')
```

其中`'blockquote'`可以用其他块级元素代替。

## 转发删除

删除光标位置前面的字符。这和在 Windows 键盘上按 Delete 键是一样的。

例如，我们可以运行以下命令来使用它:

```
document.execCommand('forwardDelete')
```

## **标题**

在选择或插入点周围添加标题元素。需要标记名字符串作为值参数。IE 或 Safari 不支持此命令。

例如，我们可以在下面的代码中使用它:

```
document.execCommand('heading', true, 'H1')
```

## **hiliteColor**

更改所选内容或插入点的背景色。它需要一个颜色值字符串作为值参数。IE 不支持。

我们可以跑:

```
document.execCommand('hilitecolor', true, 'red')
```

将突出显示颜色更改为红色。

## **增加字体**

在选择器周围或插入点添加一个`big`标签。IE 不支持。

```
document.execCommand('increaseFontSize')
```

## 缩进

缩进包含选定内容或插入点的行。如果有多行处于不同的缩进级别，缩进量最小的行将被缩进。

我们可以跑:

```
document.execCommand('indent')
```

缩进文本。

## insertBrOnReturn

`insertBrOnReturn`添加换行符或`<br>`元素。IE 不支持。

我们可以跑:

```
document.execCommand('insertBrOnReturn')
```

## insertHorizontalRule

在插入点插入一个`hr`元素或用它替换选择。

我们可以跑:

```
document.execCommand('insertHorizontalRule')
```

## 插入 HTML

在插入点插入 HTML 字符串，这将删除所选内容。IE 不支持，需要有效的 HTML 字符串。

我们可以通过键入以下内容来运行它:

```
document.execCommand('insertHTML', false, '<b>foo</b>')
```

## 插入图像

在插入点插入图像。图像的`src`属性需要字符串的 URL。

例如，我们可以运行:

```
document.execCommand('insertImage', false, '[https://images.unsplash.com/photo-1496096265110-f83ad7f96608?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=750&q=80'](https://images.unsplash.com/photo-1496096265110-f83ad7f96608?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=750&q=80'))
```

![](img/493dc17643918efeae8b1dd87104b754.png)

[优 X 创投](https://unsplash.com/@youxventures?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## insertOrderedList

为选定内容或在插入点插入一个编号有序列表。

我们可以如下使用它:

```
document.execCommand('insertOrderedList');
```

## insertUnorderedList

为选定内容或在插入点插入一个带项目符号的无序列表。

我们可以如下使用它:

```
document.execCommand('insertUnorderedList');
```

## 插入段落

在所选内容或当前行周围添加一个段落。IE 在插入点插入一个段落并删除所选内容。

我们可以如下使用它:

```
document.execCommand('insertParagraph');
```

## 插入文本

在插入点添加给定的纯文本并删除所选内容。

我们可以如下使用它:

```
document.execCommand('insertText');
```

## 斜体的

切换所选内容或插入点的斜体。IE 使用了`em`而不是`i`。

```
document.execCommand('italic');
```

## 司法中心

将所选内容或插入点居中。

我们可以如下使用它:

```
document.execCommand('justifyCenter');
```

## `justifyFull`

对齐选择或插入点。

我们可以如下使用它:

```
document.execCommand('justifyFull');
```

## `justifyLeft`

左对齐选择或插入点。

我们可以如下使用它:

```
document.execCommand('justifyLeft');
```

## **justifyRight**

将所选内容或插入点右对齐。

我们可以如下使用它:

```
document.execCommand('justifyRight');
```

## `outdent`

突出显示包含选定内容或插入点的行。

我们可以如下使用它:

```
document.execCommand('outdent');
```

## `paste`

在插入处粘贴剪贴板内容并替换当前选择。它对网页内容是禁用的。

我们可以如下使用它:

```
document.execCommand('paste');
```

## `redo`

重做先前撤消的命令。

我们可以如下使用它:

```
document.execCommand('redo');
```

## `removeFormat`

删除当前选定内容的所有格式。

我们可以如下使用它:

```
document.execCommand('removeFormat');
```

## 选择全部

选择可编辑区域的所有内容。

我们可以如下使用它:

```
document.execCommand('selectAll');
```

## 删除线

打开或关闭选择点或插入点的删除线。

我们可以如下使用它:

```
document.execCommand('strikeThrough');
```

## 下标

打开或关闭选择点或插入点的下标。

我们可以如下使用它:

```
document.execCommand('subscript');
```

## 上标

打开或关闭选择点或插入点的上标。

我们可以如下使用它:

```
document.execCommand('superscript');
```

## 强调

打开或关闭选定内容或插入点的下划线。

我们可以如下使用它:

```
document.execCommand('underline');
```

## `undo`

撤消到上次运行的命令。

我们可以如下使用它:

```
document.execCommand('undo');
```

## 使分开

从选定的超链接中删除`a`元素。

我们可以如下使用它:

```
document.execCommand('unlink');
```

## styleWithCSS

为生成的标记打开或关闭 HTML 标签或 CSS 的使用。`true`修改/生成标记中的`style`属性，`false`生成表示元素。

我们可以如下使用它:

```
document.execCommand('styleWithCSS', true, true);
```

`document.execCommand`方法对于处理打开了设计模式的网页非常有用。通过运行上面列出的各种命令，我们可以对格式进行许多更改。

如果我们想做大量的修改，这比检查元素然后手动修改它要方便得多。