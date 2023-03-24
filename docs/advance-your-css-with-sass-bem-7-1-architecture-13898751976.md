# 使用 Sass、BEM 和 7–1 架构提升您的 CSS

> 原文：<https://levelup.gitconnected.com/advance-your-css-with-sass-bem-7-1-architecture-13898751976>

使用 BEM 和带有 Sass 的 7–1 架构来帮助您更好地组织应用程序中的 CSS 样式。

![](img/a806d7248c49f59c47bc91cbaa14a40b.png)

当涉及到开发大型应用程序时，确保组织结构优雅成为首要任务之一。检查组件文件是否是 PascalCase，变量听起来很合理(没有奇怪的名字，你不知道谁在看你的 Github)，没有太多大块注释掉的代码/备注。你知道你是谁。那些写着“这个代码很丑，idk 它工作，但我花了 5 个小时，所以他妈的。”

老实说，你们当中有多少人以前有过这种感觉？没关系，这是一个安全的空间。

大型应用程序也意味着大量的样式。这就是使用 [Sass](https://sass-lang.com/) 的好处。

Sass 是 CSS 的预处理器。它使您能够在样式中使用 CSS 没有的特性(例如:嵌套，我们将在后面提到)，这反过来帮助您编写代码。推荐使用两种技术来构建 Sass 样式:块元素修改器(BEM)方法和 7–1 架构。将 Sass 与这两种技术结合使用将创建一个更干净、更整洁的代码库。更不用说，它使你的应用程序更容易维护，并允许团队之间更容易的合作。

# 不列颠帝国勋章

BEM 是一种关于 HTML 元素类应该如何命名的方法。BEM 类名应该简单明了，不言自明。类名的格式为:`block__element--modifier`(块双下划线元素双破折号修饰符)。

让我们看看这个例子:

```
<nav className**=**"nav">
<h1 className**=**"nav__h1">Big Cat Refuge</h1>
<ul className**=**"nav__ul">
<li className**=**"nav__li">Home</li>
<li className**=**"nav__li">About</li>
<li className**=**"nav__li">Contact</li>
</ul>
<button className="nav__btn--hover">Change Theme</button>
</nav>
```

*   块可以被认为是其嵌套/子元素的“容器”或“父元素”。它在块部件之间提供了清晰的连接。这里的块是导航栏`<nav>`，带有一个“nav”类。
*   元素是块的“子元素”。这里的孩子是`h1` `ul` `li` `button`。类名为`nav__h1`、`nav__ul`、`nav__li`、`nav__btn--hover`。
*   修饰符描述元素外观或行为的变化。这里的导航按钮有一个修饰符，就是`hover`。按钮的类名是`nav__btn--hover`。

## 嵌套边界元法

Sass 的一个流行特性是嵌套。BEM 与 Sass 的筑巢密切相关。嵌套允许您定义选择器的层次结构，这是编写 CSS 样式规则的捷径。换句话说，更少的代码行。例如:

`&`表示父元素，即块，它是`nav`。在 CSS 中，代码如下所示:

你注意到萨斯的方式不那么重复了吗？我们不必每次都打出`.nav`。

# 7–1 架构

构建 Sass 应用程序的另一种流行方式是使用 7–1 架构。7–1 模式表示我们创建 7 个文件夹和 1 个主 Sass 文件，从这 7 个文件夹中导入所有文件。这种技术通常用于具有多个页面的大型项目。看一下这个示例结构:

```
Sass/
|
|– abstracts/
|   |– _variables.scss    # Sass Variables
|   |– _mixins.scss       # Sass Mixins
|   |– _functions.scss    # Sass Functions
|
|– vendors/
|   |– _icons.scss        # Font-Awesome Icons
|
|– base/
|   |– _reset.scss        # Reset
|   |– _typography.scss   # Typography rules
|   |– _animations.scss   # Animation rules
|   |– _utilities.scss    # Utility rules
|
|– layout/
|   |– _navigation.scss   # Navigation
|   |– _header.scss       # Header
|   |– _footer.scss       # Footer
|
|– components/
|   |– _buttons.scss      # Buttons
|   |– _card.scss         # Card
|   |– _form.scss         # Form
|
|– pages/
|   |– _home.scss         # Home page style
|   |– _contact.scss      # Contact page style
|   |– _about.scss        # About page style
|
|– themes/
|   |– _pink.scss         # Pink theme
|   |– _mint.scss         # Mint theme
|
 – main.scss              # Main Sass input file
```

如您所见，我们在这里有 7 个文件夹和 1 个 main.scss 文件，它们都在父 Sass 文件夹下。**注意:**你做**不是**需要正好 7 个文件夹。你可以少吃点。

请注意文件名中的前导下划线。这些文件被称为**部分**。片段是 Sass 片段的小文件。下划线表示该文件是分部文件，不应该被编译成 CSS。partials 的目的是避免用一个非常大的 CSS 文件包含所有的应用程序样式。这可能很难通读和维护。

## 将片段导入 main.scss

在 main.scss 文件中，我们导入所有部分。秩序很重要。您的导入顺序就是 Sass 在处理过程中遵循的顺序。一般的规则是将所有的“逻辑”文件(混合、变量、函数)放在顶部。main.scss 文件应该如下所示:

```
@import 'abstracts/variables';
@import 'abstracts/mixins';
@import 'abstracts/functions';
@import 'vendors/icons';//etc..
```

在探索了更多关于 Sass 的内容后，我发现它很有趣，也很容易使用！它让我的代码神奇地变得更加光滑和精确。我希望你也能学会它，并将其融入你的下一个项目中！

回头再聊，✌️