# 引导 5-更多表格样式

> 原文：<https://levelup.gitconnected.com/bootstrap-5-more-table-styles-5b970e0f78b8>

![](img/e90ca9d2b0d1c8a6483911bb4306d9fb.png)

照片由 [amirali mirhashemian](https://unsplash.com/@amir_v_ali?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

**写这篇文章时，Bootstrap 5 处于 alpha 状态，可能会更改。**

Bootstrap 是任何 JavaScript 应用程序的流行 UI 库。

在本文中，我们将了解如何使用 Bootstrap 5 设计表格样式。

# 无边框表格

我们可以用`.table-borderless`类创建一个没有边框的表格:

```
<table class="table table-borderless">
  <thead>
    <tr>
      <th scope="col">#</th>
      <th scope="col">First</th>
      <th scope="col">Last</th>
      <th scope="col">Age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">1</th>
      <td>james</td>
      <td>smith</td>
      <td>20</td>
    </tr>
    <tr>
      <th scope="row">2</th>
      <td>mary</td>
      <td>jones</td>
      <td>20</td>
    </tr>
    <tr>
      <th scope="row">3</th>
      <td colspan="2">Larry</td>
      <td>50</td>
    </tr>
  </tbody>
</table>
```

它也适用于其他变体:

```
<table class="table table-dark table-borderless">
  <thead>
    <tr>
      <th scope="col">#</th>
      <th scope="col">First</th>
      <th scope="col">Last</th>
      <th scope="col">Age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">1</th>
      <td>james</td>
      <td>smith</td>
      <td>20</td>
    </tr>
    <tr>
      <th scope="row">2</th>
      <td>mary</td>
      <td>jones</td>
      <td>20</td>
    </tr>
    <tr>
      <th scope="row">3</th>
      <td colspan="2">Larry</td>
      <td>50</td>
    </tr>
  </tbody>
</table>
```

# 小桌子

我们可以使用`.table-sm`类通过将填充减少一半来使表格更加紧凑:

```
<table class="table table-sm">
  <thead>
    <tr>
      <th scope="col">#</th>
      <th scope="col">First</th>
      <th scope="col">Last</th>
      <th scope="col">Age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">1</th>
      <td>james</td>
      <td>smith</td>
      <td>20</td>
    </tr>
    <tr>
      <th scope="row">2</th>
      <td>mary</td>
      <td>jones</td>
      <td>20</td>
    </tr>
    <tr>
      <th scope="row">3</th>
      <td colspan="2">Larry</td>
      <td>50</td>
    </tr>
  </tbody>
</table>
```

这也适用于其他表格变体:

```
<table class="table table-dark table-sm">
  <thead>
    <tr>
      <th scope="col">#</th>
      <th scope="col">First</th>
      <th scope="col">Last</th>
      <th scope="col">Age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">1</th>
      <td>james</td>
      <td>smith</td>
      <td>20</td>
    </tr>
    <tr>
      <th scope="row">2</th>
      <td>mary</td>
      <td>jones</td>
      <td>20</td>
    </tr>
    <tr>
      <th scope="row">3</th>
      <td colspan="2">Larry</td>
      <td>50</td>
    </tr>
  </tbody>
</table>
```

# 竖向定线

使用 Bootstrap 5 类可以改变表的垂直对齐方式。

它们可以在单元格、表格或表格行中进行更改:

```
<table class="table table-sm table-dark">
  <div class="table-responsive">
    <table class="table align-middle">
      <thead>
        <tr>
          ...
        </tr>
      </thead>
      <tbody>
        <tr class="align-bottom">
          ...
        </tr>
        <tr>
          <td>...</td>
          <td class="align-top">aligned to the top.</td>
          <td>...</td>
        </tr>
      </tbody>
    </table>
  </div>
</table>
```

我们有`align-top`、`align-middle`和`align-bottom`类来进行比对。

# 嵌套

嵌套表格不会继承边框样式、活动样式和表格变体。

例如，我们可以写:

```
<table class="table table-striped">
  <thead>
    <tr class="align-bottom">
      <th scope="col">#</th>
      <th scope="col">First</th>
      <th scope="col">Last</th>
      <th scope="col">Age</th>
    </tr>
  </thead>
  <tbody> <tr>
      <th scope="row">1</th>
      <td>james</td>
      <td>smith</td>
      <td>20</td>
    </tr> <tr>
      <td colspan="4">
        <table class="table align-bottom">
          <thead>
            <tr class="align-bottom">
              <th scope="col">#</th>
              <th scope="col">First</th>
              <th scope="col">Last</th>
              <th scope="col">Age</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <th scope="row">1</th>
              <td>james</td>
              <td>smith</td>
              <td>20</td>
            </tr>
            <tr>
              <th scope="row">2</th>
              <td>mary</td>
              <td>jones</td>
              <td>20</td>
            </tr>
            <tr>
              <th scope="row">3</th>
              <td colspan="2">Larry</td>
              <td>50</td>
            </tr>
          </tbody>
        </table>
      </td>
    </tr>
  </tbody>
</table>
```

将样式应用于嵌套表格。

样式必须单独应用于每个表格。

# 桌面

我们可以使用`.table-light`或`.table-dark`类使`thead`呈现浅灰色或深灰色。

例如，我们可以写:

```
<table class="table">
  <thead class="table-light">
    <tr>
      <th scope="col">#</th>
      <th scope="col">First</th>
      <th scope="col">Last</th>
      <th scope="col">Age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">1</th>
      <td>james</td>
      <td>smith</td>
      <td>20</td>
    </tr>
    <tr>
      <th scope="row">2</th>
      <td>mary</td>
      <td>jones</td>
      <td>20</td>
    </tr>
    <tr>
      <th scope="row">3</th>
      <td colspan="2">Larry</td>
      <td>50</td>
    </tr>
  </tbody>
</table>
```

使表格标题变成浅灰色。

我们可以用`table-dark`类让它变成深灰色:

```
<table class="table">
  <thead class="table-dark">
    <tr>
      <th scope="col">#</th>
      <th scope="col">First</th>
      <th scope="col">Last</th>
      <th scope="col">Age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">1</th>
      <td>james</td>
      <td>smith</td>
      <td>20</td>
    </tr>
    <tr>
      <th scope="row">2</th>
      <td>mary</td>
      <td>jones</td>
      <td>20</td>
    </tr>
    <tr>
      <th scope="row">3</th>
      <td colspan="2">Larry</td>
      <td>50</td>
    </tr>
  </tbody>
</table>
```

# 工作台台脚

我们可以用`tfoot`元素给表格添加一个页脚。

例如，我们可以写:

```
<table class="table">
  <thead class="table-dark">
    <tr>
      <th scope="col">#</th>
      <th scope="col">First</th>
      <th scope="col">Last</th>
      <th scope="col">Age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">1</th>
      <td>james</td>
      <td>smith</td>
      <td>20</td>
    </tr>
    <tr>
      <th scope="row">2</th>
      <td>mary</td>
      <td>jones</td>
      <td>20</td>
    </tr>
    <tr>
      <th scope="row">3</th>
      <td colspan="2">Larry</td>
      <td>50</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <th>footer</th>
      <td>footer</td>
      <td>footer</td>
      <td>footer</td>
    </tr>
  </tfoot>
</table>
```

添加页脚。

# 字幕

我们可以添加`caption`标签来添加一个标题来标记表格。

例如，我们可以写:

```
<table class="table">
  <caption>List of people</caption>
  <thead class="table-dark">
    <tr>
      <th scope="col">#</th>
      <th scope="col">First</th>
      <th scope="col">Last</th>
      <th scope="col">Age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">1</th>
      <td>james</td>
      <td>smith</td>
      <td>20</td>
    </tr>
    <tr>
      <th scope="row">2</th>
      <td>mary</td>
      <td>jones</td>
      <td>20</td>
    </tr>
    <tr>
      <th scope="row">3</th>
      <td colspan="2">Larry</td>
      <td>50</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <th>footer</th>
      <td>footer</td>
      <td>footer</td>
      <td>footer</td>
    </tr>
  </tfoot>
</table>
```

向表格底部添加标签。

![](img/3d60b8d9eac217d9eb11c9cd8ffe5a14.png)

[sheri silver](https://unsplash.com/@sheri_silver?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以添加各种风格的表格。

嵌套表格必须单独应用样式。