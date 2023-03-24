# JavaScript 表格排序(第 2 部分，共 2 部分)—增强！

> 原文：<https://levelup.gitconnected.com/javascript-table-sorting-part-2-of-2-enhancement-18fe607842b6>

如果您还没有读过，请务必阅读第 1 部分。

![](img/8becf93ff80d0999a94594bc5bb2c1a3.png)

乔治·德西普里斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在第 1 部分中，我展示了如何采用其他人的简单示例[并对其稍加修改，将代码大小几乎减半。因为它已经很小了，所以现在我们有空间用更多的功能来增强它。](https://medium.com/javascript-in-plain-english/easy-table-sorting-with-javascript-370d8d97cad8)

特别是使用包含我们的排序类型和数据收集方法的两个对象，为轻松添加更多类型的排序奠定了基础。

# 新标题和标题标记

为了展示我们的新过滤器，我们需要一个包含新数据的新表。

表格标题/表头是大部分更改发生的地方:

```
<table>
  <caption data-tablesort-reset>Regex, DateTime, Input sorting</caption>
  <thead>
    <tr>
      <th
        scope="col"
        data-tablesort="text"
       >Name</th>
      <th
        scope="col"
        data-tablesort="number"
        data-tablesort-rxmatch="^\s*([0-9]+)"
      >Place</th>
      <th
        scope="col"
        data-tablesort="date"
      >When</th>
      <th
        scope="col"
        data-tablesort="valueText"
      >Designation</th>
     </tr>
  </thead>
```

数据属性用于以下内容:

## **数据表排序复位**

如果出现在标题上，这个属性将告诉我们的 JS 制作一个按钮，将表格顺序重置为页面加载时的顺序。您不需要为此设置值，只要它的存在就足以触发。

## 数据表排序

列的排序类型。上一篇文章中“**日期**”、“**编号**”和“**文本**”的现有值仍然存在，但是我们在这里添加了一些新值。

“ **valueText** ”和“ **valueNumber** ”将深入表格单元格，查找第一个`INPUT`、`SELECT`、`TEXTAREA`或`BUTTON`并提取其值，而不是`th.textContent`。如果没有找到这样的标签，将使用单元格的文本内容。

## 数据表排序-rxmatch

一个正则表达式，输入到脚本端的`new regExp()`中，检索到的内容将根据该表达式进行过滤。例如，我们将有一个“位置”列

# 新示例表格单元格

```
<tr>
  <th scope="row">Luke</th>
  <td>33rd</td>
  <td><time datetime="1978-05-25T17:00:00-05:00">A New Hope</time</td>
  <td><input value="Red Five" readonly></td>
</tr><tr>
  <th scope="row">Mara</th>
  <td>54th</td>
  <td><time datetime="1991-08-07T17:00:00-05:00">Heir to the Empire</time</td>
  <td><input value="Mother" readonly></td>
</tr><!-- etc, etc -->
```

在这种情况下，因为我们的“place”列有多位数，所以我们希望按数字排序，但那里有文本。标题`^\s*([0–9]+)`中的正则表达式可以干净地提取它，把“st”、“nd”和“rd”留在剪辑室的地板上。

“when”包括一个`<time>`标签，其中的文本提供了一个描述，但是 datetime 具有我们想要排序的实际值。因为它被设置为`data-tablesort=”date”`，我们的新脚本将被设置为提取`datetime`，而不是文本。

# 脚本

对基础脚本进行了更多的修改，以进一步完善内容。许多更复杂的循环被“for/of”所抛弃，以减少代码量。实际上，速度差虽然存在，但在这里可以忽略不计。特别是比较功能:

```
comps = {
  number : function(a, b) {
    return a.value - b.value;
  },
  text : function(a, b) {
    return a.value > b.value ? 1 : a.value < b.value ? -1 : 0;
  }
},
```

进行了重构，这样我们就不需要额外的包装匿名来从排序对象中提取“值”。

## RegExp 内容过滤

这里我们首先需要的是一种将正则表达式传递给数据提取方法的方法。我廉价地使用了一个`rxMatch` 变量，我把它应用到生活中。我知道有些人会说把它作为一个参数来传递，但这是我们不需要的。尤其是因为处于会减慢速度的循环中。不要在循环中做任何不必要的事情。
它在我们的排序事件处理程序中获得一个值。首先，我们制作正则表达式本身:

```
regex = (
  th.dataset.tablesortRxmatch ?
  new RegExp(th.dataset.tablesortRxmatch) :
  false
),
```

然后，我们创建实际的匹配函数本身。

```
rxMatch = (
  regex ?
  function(text) {
    var result = text.match(regex);
    return result == null ? "" : result[0];
  } :
  function(text) { return text; }
);
```

`rxMatch` 又一次被限定为生命，而不是`tablesort` 函数。
`rxMatch` 如果没有有效的 regex 将简单地返回文本。与在每个该死的数据提取器中编写一个“if”语句相比，只调用一个除了盲返回之外什么也不做的函数更容易、更简单、更快。
这导致我们的提取器让它们的各种文本通过我们的过滤器，因此:

```
text : {
  get : function(cell) {
    return rxMatch(cell.textContent);
  },
  sort : comps.text
},
```

## “日期”和标签

当我们说按日期排序时，如果有时间标签，就向上拉。不难熬。我们只是修改了现有“日期”提取器:

```
date : {
  get : function(cell) {
    var time = cell.querySelector("time");
    if (time) {
      var datetime = time.getAttribute("datetime");
      return Date.parse(datetime || rxMatch(time.textContent));
    }
    return Date.parse(rxMatch(cell.textContent));
  },
  sort : comps.number
},
```

我们找到第一个时间标签，如果存在，我们看看它是否有日期时间。如果是的话，我们就把它用于我们的`Date.parse`，否则就给它一个`rxMatch`标签的内容。如果两者都没有找到，则用`rxMatch`代替`cell.textContent`。

## 表单元素值

这些例程类似于时间/日期时间代码。

```
valueText : {
  get : function(cell) {
    var formElement = cell.querySelector(
      "input, select, textarea, button"
    );
    return rxMatch(
      formElement ? formElement.value : cell.textContent
    );
  },
  sort : comps.text
}
```

我们使用 querySelector 获取单元格中的第一个表单元素，然后 rxmatch 该元素的值，如果没有找到表单元素，则 rx match 该单元格的 textcontent。

简单的柠檬榨汁机

## 重置表单

要添加我们的重置按钮，我们只需拉动所有具有我们想要的数据属性的`<caption>`。

在它们中的每一个上，我们都需要一种方法来存储它们的原始位置。如果 HTML 元素有某种存储数据的地方就好了…哦，等等，我们有数据属性！我们能用那个吗？

*种……*

`Element.dataset`属性只允许您在页面加载时设置在标记中声明的数据属性的值。您不能通过数据集创建新的。我们能做的是使用`setAttribute`和`getAttribute`来设置这样的值。这些`dataset`见不到它们，但大不了鸣笛！

```
for (var caption of d.querySelectorAll("caption[data-tablesort-reset]")) {
  var i = 0;
  for (var tr of caption.parentNode.querySelectorAll("tbody tr")) {
    tr.setAttribute("data-tablesortKey", i++);
  }
  makeButton("Reset Sort", tableReset, caption);
}
```

`makeButton`是我创建的一个新函数，因为我们的页面解析循环都有很多按钮。现在很多人痴迷于将所有东西分解成不同的功能，不管你是否需要。我发誓，除非每一行代码都有一个函数，否则他们是不会满意的，而那些令人费解的“箭头函数”在这方面一点帮助都没有。

我的思维模式是，如果你在两个不同的地方不止一次地做一件事，那么就做函数。否则就别想了，gimboid！

…如果创建一个功能有意义，就让它具有一些额外的功能，以便将来在其他地方使用。

```
function makeButton(text, click, parent, replace) {
  var e = d.createElement("button");
  e.type = "button";
  if (text) e.textContent = text;
  if (click) e.onclick = click;
  if (parent) {
   if (replace) parent.textContent = "";
   parent.appendChild(e);
  }
  return e;
 }
```

创建按钮标签，将类型设置为“button ”,因为默认值是“submit ”,我们不想用`Event.preventDefault();`来瞎搞，如果我们传递了文本，则将它作为内容应用，如果我们传递了单击，将它作为我们的处理程序应用——同样，因为我们刚刚创建了它，所以我们可以只使用`onclick`而不是`addEventListener` —如果我们想要替换，如果有父检查，则删除内容。否则追加。最后，如果我们在未来需要添加其他属性到我们的新按钮，返回它。

这个函数给了我们一个类似的添加排序执行器的重构。

```
for (var th of d.querySelectorAll("th[data-tablesort]")) {
  if (!sorts[th.dataset.tablesort]) continue;
  makeButton(th.textContent, tableSort, th, true);
}
```

比我们在第 1 部分中所做的要简单得多。

现在重置回调本身:

```
function tableReset(event) { var
    table = event.currentTarget.parentNode.parentNode,
    e = table.tHead.firstElementChild.firstElementChild; if (e) do {
    if (e.firstElementChild.tagName == "BUTTON") {
      e.firstElementChild.value = "";
    }
  } while (e = e.nextElementSibling); sortAndSet(
   table.tBodies[0],
   comps.number,
   function(row) {
     return +row.getAttribute("data-tablesortKey");
   });} // tableReset
```

非常简单。我们必须走着离开餐桌和第一次约会。我们循环查找 thead 中的按钮，并清空它们的值，说它们没有被排序，并触发 CSS 删除升序/降序指示符。我们称之为一个新函数`sortAndSet`，它和`makeButton`非常相似，我们也为普通的排序做同样的事情。

第一个参数是要排序的 TBODY，第二个是比较函数，第三个是我们的数据提取器。

这导致我们的表排序例程也被重构以使用这个新函数。

```
function tableSort(event) { var
    button = event.currentTarget,
    th = button.parentNode,
    type = sorts[th.dataset.tablesort],
    regex = (
      th.dataset.tablesortRxmatch ?
      new RegExp(th.dataset.tablesortRxmatch) :
      false
    ),
    e = th; while (e = e.previousElementSibling) e.firstElementChild.value = "";
  e = th;
  while (e = e.nextElementSibling) e.firstElementChild.value = ""; rxMatch = (
    regex ?
    function(text) {
     var result = text.match(regex);
     return result == null ? "" : result[0];
    } :
    function(text) { return text; }
  ); sortAndSet(
    th.parentNode.parentNode.parentNode.tBodies[0],
    (
      (button.value = button.value == 1 ? 0 : 1) ?
      type.sort :
      function(a, b) { return type.sort(b, a); }
    ),
    function(row) {
      return type.get(row.cells[th.cellIndex]);
    }
  );} // tablesort
```

和以前一样，我们不需要遍历来引用表上的各种元素。您可以在添加 regex 和 rxMatch 声明的地方看到它们，最后是 sortAndSet。

因为我们将回调传递给 sortAndSet，所以我们可以根据需要将各种经过评估的匿名函数放入其中。

这就是我们的附加功能。

# 现场演示

 [## 使用 JavaScript 的高级表格排序

cutcodedown.com](https://cutcodedown.com/for_others/medium_articles/tableSort/robust/tableSort.html) 

同我所有的例子目录:
[https://cutcodedown . com/for _ others/medium _ articles/table sort/robust/](https://cutcodedown.com/for_others/medium_articles/tableSort/robust/)

是开放的，可以很容易地接触到后备箱里的垃圾，里面有一个完整的文件。

对于那些喜欢钢笔的人，给你！

# 结论

我们有将近 4k 的代码，但是它有很多额外的用处。最棒的是，对标记的侵入仅限于数据属性，这意味着您添加的任何页面也不会违反任何可访问性担忧。