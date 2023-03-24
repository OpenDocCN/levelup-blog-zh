# 一直生活在黑暗中的 10 个 HTML 元素！

> 原文：<https://levelup.gitconnected.com/10-html-elements-that-have-been-living-in-the-dark-a7e6fadf7ab6>

## 认识未被认识的人

![](img/b510852be3ba2917e105d753627f860f.png)

照片由[昆廷·格里尼特](https://unsplash.com/@qgrignet?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

HTML 是我们日常使用的语言。库和框架使用 CSS 类和定制的预构建组件使编码变得方便，减少了我们编写大量 HTML 的需要。

虽然我们经常使用这些 HTML 标签，但今天我们将揭示 10 个 HTML 元素！

# 最被低估的标签

## 1.基础

`base`标签位于`head`元素内部。它指定文档中所有相对 URL 的基本 URL 和/或目标 URL。

它必须有一个`href`或一个目标属性，或者两者都有。

```
<head>
  <base target="_top" href="[https://www.google.com/](https://example.com/)">
</head>
```

## 2.字段集

`fieldset`标签用于对表单中的相关元素进行分组。它会在相关元素周围标记一个框。

`legend`标签用于定义字段集元素的标题。

```
<form action="/login_page.php">
  <fieldset>
    <legend>Login:</legend>
    <label for="uname">Username:</label>
    <input type="text" id="uname" name="uname"><br><br>
    <label for="pword">Password:</label>
    <input type="text" id="pword" name="pword"><br><br>
    <input type="submit" value="Submit">
  </fieldset>
</form>
```

## 3.WBR

`wbr`(断词机会)标签确定了文本在哪里是理想的换行

如果一个单词太长，浏览器可能会在错误的地方打断它。您可以使用 WBR 元素添加断词机会。

```
<p>Here is an example <wbr>Thistextwillbreakwhenresizingthebrowserscreenbybreakingbyeachword<wbr></p>
```

## 4.keyboard 键盘

`kbd`标签用于定义键盘输入。里面的上下文以浏览器默认的等宽字体显示。

```
<p>Press <kbd>Ctrl</kbd> + <kbd>C</kbd> to copy text (Windows).</p>
```

## 5.S

标签指定不再正确、准确或相关的文本。该文本将显示一条交叉线条。

```
<p><s>Only 50 tickets left!</s></p>
<p>SOLD OUT!</p>
```

## 6.OPTGROUP

`optgroup`标签用于对`select`元素中的相应选项进行分组。

```
<label for="cars">Select car:</label>
<select name="cars" id="cars">
  <optgroup label="Swedish Cars">
    <option value="volvo">Volvo</option>
    <option value="saab">Saab</option>
  </optgroup>
  <optgroup label="German Cars">
    <option value="mercedes">Mercedes</option>
    <option value="audi">Audi</option>
  </optgroup>
</select>
```

## 7.数据列表

标签为输入元素指定了一个预定义选项列表。

它用于为输入元素提供一个“*自动完成*功能。用户在输入数据时会看到预定义选项的下拉列表。

```
<label for="browser">Select Browser:</label>
<input list="browsers" name="browser" id="browser">

<datalist id="browsers">
  <option value="Edge">
  <option value="Firefox">
  <option value="Chrome">
  <option value="Opera">
  <option value="Safari">
</datalist>
```

## 8.引用

这个`cite`标签被定义为一个“*创意作品*的标题。引用元素中的文本通常以斜体显示。

```
<p><cite>The Scream</cite> by Edward Munch. Painted in 1893.</p>
```

## 9.标记

`mark`标签定义了应该标记或突出显示的文本

```
<p>Do not forget to buy <mark>milk</mark> today.</p>
```

## 10.缩写的/缩写词

`abbr`标签定义一个缩写或首字母缩略词

```
The <abbr title="World Health Organization">WHO</abbr> was founded in 1948.
```

# 结论

现代前端库和框架使得编程变得非常容易，并且减少了对深层 HTML 的依赖。然而，作为一个充满激情的开发者和学习者，深入地获取知识并理解所学的东西总是更好的。

更多 HTML 标签参考[W3Schools.com](https://www.w3schools.com/tags/)。

分享知识是获得知识的唯一途径。对于那些可能从这个故事中受益的人，请随意添加并通过评论做出贡献。

玩得开心！享受编码！