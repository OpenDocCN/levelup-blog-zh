# 使用 JavaScript 定制链接行为

> 原文：<https://levelup.gitconnected.com/customising-link-behaviour-using-javascript-37658b35eea0>

## 偏好管理的静态站点方法

![](img/1925cf6677e851a3767938eeadaca42d.png)

克里斯·巴巴利斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在一个新窗口中打开链接——或者二十年来最好的一个新标签——一直是一个热点问题。许多作者认为这样做是明智的，目的是“让读者留在你的页面/网站上”，而可用性从业者对他们所认为的恼人的、违反直觉的、忽视用户意愿的行为感到绝望。在[链接到新标签与同一个标签](https://uxdesign.cc/linking-to-a-new-tab-vs-same-tab-f88b495d2187)、[中，杰西·萨默斯](https://medium.com/@summertimecoolj)认为:

> 如果用户点击一个链接，他们不应该对发生的事情感到惊讶。

我基本同意。当然也有例外，但是总的来说，web 的默认设置总是用链接的目标来替换页面，这种期望应该保持下去。

话虽如此，用户有不同的偏好，因此浏览器通常会提供不同的行为。根据具体情况，Chrome 允许我在新的标签页或窗口中打开链接，但这是一个笨拙的操作:右键单击，从上下文菜单中选择选项。如果我们可以在我们网站的所有页面上为用户提供一些简单的自定义行为，会怎么样？

# 客户端定制

真的，我只是在这里建立一个更广泛的话题的讨论。虽然如果你愿意的话，你可以专门关注链接目标的问题，但我想探讨一下静态网站如何实现定制化这个更普遍的问题。

通常，当面临这样的需求时，动态站点会直接访问数据库:登录的用户可以选择某些设置，他们的选择存储在服务器端，并在每次访问页面时读取，或者可能在第一次登录时读取。

虽然这种方法可行——甚至可能有一两个优点——但对于静态站点来说是不可用的。我们需要找到我们的老朋友 JavaScript，看看我们可以在客户端的静态页面上添加什么功能。

## 静态设置的蓝图

这里有一个我们可以用来处理静态站点设置的通用方法:

1.  创建一个使用 JavaScript 处理的通用设置表单。不言而喻，该表单是通过[渐进增强](https://medium.com/remys-blog/progressive-enhancement-bec78a083763)提供的(如链接到)。
2.  在提交时，表单应该通过 JavaScript 更新 [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) 中的设置。我目前正在改进我用来在本地存储数据的模式，但是基本方法非常简单，我很快就会谈到。
3.  在适当的情况下，使用 JavaScript 检查 localStorage 中的设置，并采取相应的措施。在链接示例中，这将涉及到向外部链接添加一个适当的`target`属性。

就是这样:非常快速简单。

## 利益

1.  **读写设置非常快** —无需往返服务器，一切都通过本地存储
2.  **读写设置简单**。即使你用这些设置做的事情最终变得稍微复杂一点，与建立一个数据库和编写 SQL 相比，在 localStorage 中存储 JSON 还是轻而易举的。
3.  **没有用户数据被发送到服务器**。就隐私问题而言，偏好的风险相当低，但从更大的角度考虑:这种方法也可以用于更敏感的数据。
4.  **无需注册**。无论是特定站点注册，还是第三方登录，当数据仅在客户端存储和使用时，用户身份验证都是一个根本不存在的障碍。

## 缺点

1.  **持久性**。理论上，本地存储是临时的——如果用户擦除了他们的硬盘，他们就会丢失那些数据。实际上，我认为这并不重要，因为网站的首选项设置值很低，但是如果对其他类型的数据使用相同的技术，您可能需要记住这一点。
2.  **加工速度**。一旦我们从服务器获得了“应用设置”页面，就这样，我们就完成了。客户端处理和应用设置很可能会导致*非常小的*延迟，但是首先考虑一下服务器更快的响应所带来的好处几乎肯定会超过延迟。

# 将这些原则应用于链接打开

以下是处理“链接打开”首选项设置的一个非常基本的设置。我将允许用户保持默认设置(所有链接都在同一个页面中打开)，或者每次在一个全新的选项卡中打开外部链接，或者为此目的保留同一个选项卡。

如果你愿意，你可以先在我自己的网站上看到这一点；表单下方有一个外部链接，您可以使用它来查看不同的行为。

*为了可读性，本文中的代码示例包含了减少代码行长度的最小重写。它们在功能上与实时代码相同，但是您可能想要参考下面链接的实时版本。*

## 设置表单的标记

```
<form id="settings" method="get" action="**/no-javascript**">
    <label>
        <input type="radio" name="links" value=""
            checked="checked" />
        Open all links in the original tab
    </label> <label>
        <input type="radio" **name="links" value="external"** />
        Open external links in the same new tab
    </label> <label>
        <input type="radio" name="links" value="_blank" />
        Open each external link in a separate new tab
    </label> <button type="submit">Save</button>
</form>
```

我将表单的动作设置为一个 URL ( `**/no-javascript**`)，理论上，我可以用它来通知读者这个过程需要 JavaScript。实际上，如果您使用 JavaScript 注入这个标记*，那就没有必要了。用户在收到标记后仍然可以禁用 JavaScript(不太可能)，或者错误会导致提交处理程序失败(更有可能)，因此构建一个后备肯定没有坏处。*

每个无线电输入仍然有一个有效的`**name**`和`**value**`，即使数据不会以传统方式提交。这有利于我们内心的平静，但是，正如我们马上会看到的，这通常是实现我们最终目标的最简单的方式，即使我们没有使用浏览器提供的默认表单处理机制。如果我们*可以*在服务器端提交上做一些合理的事情，这就更加重要了，所以我们不妨养成这个习惯。

## 处理设置表单的 JavaScript

在静态站点上，表单必须在客户端处理。在这种情况下，这意味着处理表单提交以及表单的初始设置以反映当前设置。原代码见`[settings-form.js](https://bobbyjack.me/js/settings-form.js)`。

```
var formElement = document.getElementById("settings");*// Save form data to localStorage on submission* 
formElement.addEventListener("submit", function(ev) {
    var sel = "**#settings [name='links']:checked**";
    var node = document.**querySelector**(sel); settings.data.links = node.getAttribute("value");
    localStorage.setItem("settings", **JSON.stringify(settings)**); window.alert("saved");
    window.location.reload();
    ev.preventDefault();
});var sel2 = "input[name='links']";*// Ensure form inputs reflect current settings*
formElement.querySelectorAll(sel2).forEach(function(node) {
    if (settings.data.links == node.getAttribute("value")) {
        node.setAttribute("checked", "checked");
    } else {
        node.removeAttribute("checked");
    }
});
```

通过`**querySelector**`使用 CSS 选择器，识别当前选中的单选按钮。`[**:checked**](https://developer.mozilla.org/en-US/docs/Web/CSS/:checked)`是一个非常方便的伪类。注意这个有限的表单目前只处理`**name='links'**` 输入；对于更复杂的形式，这将进一步推广。

我重用了一种标准技术来将结构化数据存储在 localStorage 中，并通过`**JSON.stringify()**`将其转换成 JSON。下面介绍`**settings**`的结构。

保存设置后，使用`[**window.location.reload()**](https://developer.mozilla.org/en-US/docs/Web/API/Location/reload)`重新加载页面。这是一种简单廉价的方式，可以确保我们正在查看的页面上的所有链接都按照新的设置进行更新。

最后，当这个脚本被加载时，有一个小的进程开始运行，根据现有的设置值选择适当的单选按钮。

这段代码非常接近于能够处理任何通用的“设置”表单；但是，这需要理解为各种类型的设置提供哪种类型的 HTML 表单输入，这超出了本文的范围。

## 应用链接设置的 JavaScript

现在有了一种更新设置的方法，它们只需要被执行。当然，这将取决于每个环境的性质，但有一些共性。在 URL 设置的情况下，这完全是关于目标属性的。原代码见`[settings.js](https://bobbyjack.me/js/settings.js)`。

```
var **default_settings** = {
    "version": 1.0,
    "data": {
        "links": ""
    }
};var settings = JSON.parse(localStorage.getItem("settings"))
    || default_settings;if (**settings.data.links**) {
    document.querySelectorAll("a").forEach(function(node) {
        var href = node.getAttribute("href");
        var url = new URL(href, window.location); if (**window.location.origin** != url.origin) {
            node.setAttribute("target", **settings.data.links**);
        }
    });
}
```

`**default_settings**`结构用于在设置任何设置之前处理初始情况；如果 localStorage 中没有任何内容，那么就使用它。我目前正在试验这种结构在一般情况下的格式，但是我计划将来使用一个`**version**`属性，这样如果需要的话，localStorage 结构以后可以被“升级”。

注意(参考原始标记),我对`**settings.data.links**`使用的值可以直接转化为`target`属性的可用值。这避免了任何一种尴尬的“`if (setting == "new_window") { ... }` ”条件。

我使用`[**window.location.origin**](https://developer.mozilla.org/en-US/docs/Web/API/Location/origin)`来确定链接是否是“外部”的。这是一个相当现代的添加，但是大多数浏览器都支持它，并且极大地简化了这个过程。

# 结论

这就是我处理客户端定制的蓝图。当然，使用这种方法有更多的可能性，甚至仅仅考虑一下可以用超链接做什么:

*   允许用户选择特定位置的首选站点(可能通过地理定位 api 的[默认)并相应地修改链接目的地(例如 nintendo.co.uk 对 nintendo.com)](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API)
*   如果用户更喜欢下载而不是在浏览器中查看，自动为 pdf 链接添加一个`download`属性
*   将每个链接的`href`直接注入到文档中(使用 CSS 很好地设计了样式)，这样它们就可见了——如果用户选择格外警惕的话

我真的很喜欢在客户端处理网站定制的想法，只要有可能。它很快，很简单，这意味着我们的读者掌握着他们的数据，所以我们的服务器永远不需要担心存储它，它首先更隐私。