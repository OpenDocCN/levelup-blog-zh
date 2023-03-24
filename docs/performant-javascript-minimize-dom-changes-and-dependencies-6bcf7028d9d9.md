# 高性能 JavaScript——最小化 DOM 变化和依赖性

> 原文：<https://levelup.gitconnected.com/performant-javascript-minimize-dom-changes-and-dependencies-6bcf7028d9d9>

![](img/d04a58d6e0f696068f8def5391afd4f5.png)

哈雷戴维森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

像任何程序一样，如果我们不小心编写代码，JavaScript 程序会变得很慢很快。

在本文中，我们将研究如何最小化 DOM 变化和减小文件大小，以使我们的应用程序在生产中更快。

# 批处理 DOM 更改

DOM 更改应该一起批处理，这样我们的应用程序就不会在每次 DOM 更改操作后刷新。

如果我们动态地设计应用程序的元素，我们可以很容易地批量修改 DOM。我们所要做的就是将所有的样式放入 CSS 的类选择器中。然后我们可以运行一些代码来应用这个类，而不是单独添加每个样式。

例如，不用编写如下代码来分别应用每种样式:

```
const p = document.querySelector('p');
p.style.fontSize = '20px';
p.style.color = 'red';
```

相反，我们可以将样式移动到它们自己的 CSS 文件中，如下所示，然后使用类列表 API 添加类名来应用带有样式的类。

CSS 将如下所示:

```
.foo {
  font-size: 20px;
  color: red;
}
```

JavaScript 可以简化为:

```
const p = document.querySelector('p');
p.classList.add('foo');
```

# 缓冲 DOM

如果我们有一个可滚动的，那么我们可以缓冲 DOM 中当前在我们的视口中不可见的项目。这有助于我们节省内存使用，减少遍历 DOM 的需要。

这被称为虚拟滚动。例如，我们可以使用 scroller.js 库制作一个带有虚拟滚动的表格。

例如，我们编写以下 HTML:

```
<script src='https://code.jquery.com/jquery-3.3.1.js'></script>
<script src='https://cdn.datatables.net/1.10.20/js/jquery.dataTables.min.js'></script>
<table id="example">
  <thead>
    <tr>
      <th>First Name</th>
      <th>Last Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>jane</td>
      <td>smith</td>
    </tr>
    <tr>
      <td>joe</td>
      <td>smith</td>
    </tr>
  </tbody>
</table>
```

然后编写下面的 JavaScript:

```
$(document).ready(() => {
  $('#example').DataTable();
});
```

我们的列表越长，我们获得的性能节省就越多。

像 scroller.js 这样的插件也有搜索功能和分页功能。

# 压缩构建的文件

当用户加载我们的应用程序时，如果我们想节省空间和加载时间，压缩文件是必须的。

最常见的压缩方法是 Gzip。gzipped 文件可以被大多数 web 服务器解码和托管。

在压缩我们的代码之前，我们也应该压缩我们的文件。缩小的文件被组合成一个或几个大的包，这样它们就比原始源代码小，因为空格和多余的字符都被删除了。

缩小是由大多数框架的命令行程序完成的。如果我们没有使用任何框架，我们也可以使用一些程序，比如 package，来创建和构建我们的项目。

# 限制库依赖性

应该尽量减少对库的依赖，这样我们就不必加载这么多的库。

这很重要，因为我们不应该使用我们不应该使用的库。

如果我们使用 Lodash 和 jQuery 这样的库，考虑用标准库中新的 JavaScript 方法替换它们。

数组方法、字符串方法、DOM 操作方法、语法等。在过去的几年里都有了很大的改进，所以是时候重新考虑在我们的代码中使用这些库了。

# 尽可能多地缓存

缓存很重要，因为我们不希望每次用户请求页面时都加载脚本。如果我们的代码没有太多变化，这一点尤其重要。

许多内容交付网络，如 Cloudfront 和其他许多网络，都将缓存作为内置特性。为了在我们向 CDN 部署新代码时清除缓存，我们可以确保构建的文件总是具有不同于先前版本的名称。

![](img/45da08c458632af583c750b0350c2ec1.png)

照片由 [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 事件处理程序

像`mousemove`和`resize`这样的事件每秒钟运行数百次，所以我们应该注意绑定到这些事件的任何事件处理程序。

因此，事件处理程序代码应该小而快。如果代码运行时间超过 2 到 3 毫秒，那么我们必须让它更快。

# 结论

批处理 DOM 更改非常重要，因为 DOM 操作是一项资源密集型操作。

事件处理程序还必须小而快，以便在短时间内运行。

如果我们有大的可滚动 div，那么我们需要虚拟滚动来缓冲屏幕外的项目。

最后，需要尽可能减少依赖项的数量。普通 JavaScript 可以实现许多其他库可以实现的事情。