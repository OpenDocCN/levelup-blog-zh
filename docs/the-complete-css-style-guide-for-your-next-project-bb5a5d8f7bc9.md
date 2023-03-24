# 下一个项目的完整 CSS 样式指南

> 原文：<https://levelup.gitconnected.com/the-complete-css-style-guide-for-your-next-project-bb5a5d8f7bc9>

![](img/6bf08a7ded59b8f6b8df5533e73b523d.png)

伊森·塞克斯顿在 [Unsplash](https://unsplash.com/search/photos/art?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

*更新 2021:这些指南的更新版本可以在* [*这个 Github 库*](https://github.com/gandreini/css-guidelines) *获得。*

*几年前，我第一次起草了这个 CSS 样式指南，当时我正在为权威人士* *和* [*工作。从第一个版本开始，我就一直在更新它并添加新的部分。这份风格指南反映了我们如何在*](http://www.muruca.org/)[*net 7*](https://www.netseven.it/)*管理和编写 CSS，它是保持项目可维护性的重要资源，尤其是复杂的长期项目和设计系统的代码。风格指南也是一个很好的资源，可以让新开发人员加入到团队中来，并让他们适应我们编写 CSS 的方式。*

这个风格指南是完全开放的，欢迎你复制它，重用它，修改它，并留下评论来帮助我改进它。

# **1。简介**

为了保持为项目编写的 CSS 代码的一致性和可维护性，我们提供了在实现和维护过程中要遵循的 CSS 指南。这些指导方针的目的是加速开发，使新开发人员更容易开始项目工作，保持代码由单个开发人员编写，保持文件简短易读。

## **1.1 预处理器**

CSS 代码将使用带有 SCSS 语法的 [**SASS 预处理器**](https://sass-lang.com/) 编写。这些指南也适用于基于**减去**或**普通 CSS** 的项目。

## **1.2 大口**

萨斯将编制一个**大口任务**。这允许所有从事同一项目的开发人员使用相同的工具来编译 SASS。 [NPM 任务跑者](https://blog.teamtreehouse.com/use-npm-task-runner)或者[咕噜](https://gruntjs.com/)也可以。更多关于如何使用 Gulp.js 来自动化 CSS 任务的信息[在这里](https://www.sitepoint.com/automate-css-tasks-gulp/)。

## **1.3 制表符和空格**

对于代码的缩进和格式，**我们使用空格而不是制表符**。请用这些设置设置您的代码编辑器:

*   **没有标签页**；
*   **tab size = 4**；
*   **缩进尺寸= 4** 。

空格是保证代码在任何人的环境中呈现相同的唯一方法。

## **1.4 HTML 内嵌样式**

**永远不要在 HTML 代码中写内联样式。内联样式会产生关键的 [**特异性问题**](http://www.smashingmagazine.com/2007/07/27/css-specificity-things-you-should-know/) ，这些问题可能很难修复并强制使用`!important`。**

## **1.5 临时 CSS**

有时需要快速编写 CSS 声明，但可能无法编译 SASS 文件。强制不要编辑由 SASS 编译的 style.css:这些修改将在下次开发者编译 css 时丢失。

出于这个原因，我们创建一个静态的 **temporary.css** 文件，它在 HTML 中链接在 style.css 之后:在这个文件中，当不可能编译 SASS 文件时，可以编写 css 声明。这只能用于紧急情况和紧急错误修复。那么这个文件中的声明必须被移动到 SASS 文件中:临时 CSS 文件在大多数时候必须是空的。

## **1.6 避免撤销/覆盖**

当你写的声明覆盖了写在代码中其他地方的声明时，你就是在撤销 CSS。这意味着 HTML 元素受到太多冲突的 CSS 声明的影响，你需要依靠特殊性来正确设计元素的样式。

撤销是一种糟糕的编码方式，我们必须尽可能减少撤销。这通常发生在我们从 CSS 框架开始的项目中，然后我们意识到我们正在编写大量的声明来覆盖默认框架的样式(参见 2.6 外部框架)。

## **1.7 80 字符宽**

尽可能将 CSS 文件的宽度限制在 80 个字符以内。原因包括:

*   并排打开多个文件的能力；
*   在 GitHub 之类的网站上，或者在终端窗口中查看 CSS
*   为评论提供合适的行长度。

## **1.8 款式棉绒**

SASS 文件必须用林挺工具[样式的棉绒](http://stylelint.io/)进行验证。样式林挺必须在 Gulp 文件中设置为一个任务，并在修改 SASS 文件时运行。更多关于如何设置林挺风格的信息请点击。我们的 stylelint 文件可以在这里[找到](https://github.com/gandreini/css-guidelines/blob/master/stylelintrc)。

## **1.9 间距单位**

所有边距和填充必须使用变量来表示:

`$space: 10px;`

基本值为`10px`，但您也可以将变量设置为您自己的基本值(如`8px`)。如果需要不同的值，可以将变量相乘:

`margin-bottom: $spacing-unit*2;`

建议将`$spacing-unit`乘以整数或 0.5 的倍数。

使用此变量的好处是:

*   更快的 CSS 编码；
*   更连贯的元素间距；
*   改变几个像素的`$spacing-unit`值，就可以在几秒钟内改变你 UI 的整体观感。

## **1.10 HTML 5**

编写 HTML 标记时必须使用 HTML 5 标记。这些语义元素清楚地向浏览器和开发人员描述了它们的含义。

# 2.文件组织

我们管理项目 CSS 的方法是基于模块化的 CSS，你可以在这里找到更多信息。我们还采用了倒三角形 CSS (ITCSS)理念，只是稍微简化了一些。这里有更多关于 ITCSS [的信息](https://www.xfive.co/blog/itcss-scalable-maintainable-css-architecture/)。

SASS 文件被组织在 5 个主要文件夹中，这些文件夹以逻辑结构组织它们:

**通用、全局、组件、布局、实用程序**

## **2.1 通用**

不直接样式化 HTML 中元素的文件:**配色**、**变量**、 **mixins** 。 **mixins** 的例子包括:

保持浮动元素一致的 mixin，不使用`overflow: hidden;`:

```
@mixin consistent {
    &:after {
        content: ' ';
        clear: both;
        display: block;
    }
}
```

使用图标字体的混合:

```
@mixin icon-font($icon) {
    content: $icon;
    font-family: 'icon-font';
    speak: none;
    font-style: normal;
    font-weight: normal;
    font-variant: normal;
    text-transform: none;
    line-height: 1;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
}
```

## **2.2 全球**

样式化 HTML 标签的公共元素。**这里没有使用类**，只有 HTML 标签。举几个例子:**规格化**、**排版**、**表格**、**表格**。

## **2.3 组件**

组件(或模块)的样式。什么是组件？**一个组件是一个独特的、独立的单元**，可以与其他组件组合形成一个更复杂的结构。**组件可在不同的环境中重复使用**，可以相互嵌套或直接插入布局。组件的风格也独立于包装它们的父元素。

## **2.4 布局**

布局是每个页面的结构，并创建放置组件的主要区域。由于布局只是结构的事实，我们期望 HTML 和 CSS 是非常有限的。

## **2.5 实用程序**

能够覆盖全局、组件和布局中定义的样式的实用程序和助手类。实用程序包括用于**隐藏元素**、**标签和值**元素的类，用于显示元数据的类，用于设置**粘性页脚**的类。

## **2.6 外部框架**

不鼓励你在复杂和长期的项目中使用 Bootstrap of Foundation 这样的框架，因为你会花很多时间来撤销样式(参见 1.6 避免撤销/覆盖)。如果你使用一个框架，用一个包管理器(像 NPM 或鲍尔)导入它，不要修改原始文件。导入主 **style.scss** 中的 SASS 文件，只选择你真正需要的文件(不要导入整个 css)。要知道，使用框架的主题化选项(比如 Bootstrap-Bootstrap 的主题)会增加另一层风格。

## **2.7 配色方案**

使用有限的颜色是一个好习惯:使用太多的颜色会导致界面不连贯，让用户感到困惑。出于这个原因，我们有一个 **color-scheme.scss** 文件，其中包含了项目中使用的所有颜色的变量**。这将限制所使用的颜色数量，应少于 15，包括所有可能的灰色阴影用于边界和背景。该文件应该在**变量. scss** 之前导入。**

## **2.8 Style.scss 文件**

在文件夹 **Generic、Global、Components、Layouts、Utilities** 之外，我们创建了 **style.scss** 文件，它负责导入所有需要的 scss 文件(将由 SASS 编译)。

**style.scss** 文件以一个注释开始，我们在注释中写下项目的名称，并解释该文件的作用:

```
//
// <NAME_OF_THE_PROJECT>
//
// Sass styles import for whole project
//
```

下一部分是我们导入外部库(如 Susy、Bootstrap)的地方。它看起来是这样的:

```
// ------------------------------------ //
// #LIBRARIES
// ------------------------------------ //
@import "jspm_packages/npm/hint.css@2.2.1/src/hint.scss";
@import "jspm_packages/npm/susy@2.2.9/sass/susy";
```

然后我们有 5 个部分用于**通用、全局、组件、布局、实用程序**，由一个注释打开，然后是所有的**导入**指令。**导入按字母顺序排列，以便于检查文件是否导入**。

这里我们提供了**组件**导入部分的部分示例:

```
// ------------------------------------ //
// #COMPONENTS
// ------------------------------------ //
@import "components/boxview";
@import "components/boxview-item";
@import "components/buttons";
@import "components/carousel";
@import "components/document-reader";
```

其他部分(**通用、全局、布局、实用程序**)看起来都一样。

# 3.命名规格

CSS 中的命名约定有助于使您的代码保持一致并提供更多信息。根据事物的本质来命名，而不是根据其外观或行为来命名。永远不要使用像`.red`这样的类:这将在你希望该元素为绿色的那天给你带来问题。永远不要使用像`.left-bar`这样的类:当你决定移动右边的滚动条时，这是没有意义的。

**骆驼箱不得用于**类。

## 3.1 组件和布局

我们使用简化版的[边界元法指南](http://getbem.com/)(我们不使用边界元法的“修改器”)。组件和布局类是使用简单的**连字符** `**-**` **分隔的字符串**构建的；它们的子元素的类由父元素的类加上一个使用双下划线**`**__**`分隔的自定义部分组成。**

**例如，你将有一个类为`item-preview`的组件和一个类为`item-preview__metadata`的子元素。**

**示例代码:**

```
<article class="item-preview"> <div class="item-preview__thumb-caption-wrapper" >
        <a class="item-preview__thumb" href="...">
            <img class="item-preview__image">
            <p class="item-preview__caption" >
                Caption
            </p>
    </div> <div class="item-preview__metadata">
        <a class="item-preview__title-link">
            <h1 class="item-preview__title">
                Title
            </h1>
        </a>
        <h2 class="item-preview__subtitle">
            Subtitle
        </h2>
        <p class="item-preview__description">
            Description
        </p>
    </div></article>
```

**当您有许多嵌套元素时，请始终创建一个两级类，使用根组件类作为第一部分:**

**`component-root-class__child-or-grandchild-element-class`**

**我们**从不**依赖 HTML 标签作为 CSS 选择器:这保证了如果另一个开发者改变了 HTML 标签，页面的样式不会受到影响。因此，每个 HTML 元素都必须有一个类。**

**这种 HTML 结构反映在组件和布局的 SCSS 文件中:这些文件通常以根类(如`.item-preview`)打开，所有子元素(如`.item-preview__thumb`、`.item-preview__*`)嵌套在根类内的第一层。为了避免在子类中重复根类前缀，请使用 Sass 引用父选择器(`.&`)，就像这样的`.&__thumb`、`.&__title`。这种解决方案允许编写更少的代码，并且在根类发生变化的情况下，只需更新一行 CSS 代码。前面看到的组件示例的 SCSS 代码是:**

```
item-preview {
    .&__thumb-caption-wrapper {
        ...
    }

    .&__thumb {
        ...
    } .&_image {
        ...
    }

    .&__caption {
        ...
    }
    ...
 }
```

## **3.2 类别前缀**

**在一些项目中，我们的 CSS 选择器可能会与其他样式表冲突:当我们使用 CSS 框架或开发必须嵌入到其他 HTML 页面中的应用程序时，就会发生这种情况。假设您有一个**分页组件**，根类是`pagination`。如果这个组件用在一个同时使用 Bootstrap 或 Foundation 的页面中，框架的风格会影响你的组件，反之亦然。**

**在这些情况下，给我们所有的类添加一个短的**前缀**是一个好习惯，以避免任何类型的冲突:前缀应该是短的(2 或 3 个字母)并由连字符`-`分隔。如果项目名为 **MY PROJECT** ，我们将为所有类添加前缀`mp-`，分页组件将为:**

```
<div class="mp-pagination">
    ...
</div>
```

## **3.3 变量**

**对于变量名，我们使用**连字符** `**-**` **作为字符串分隔符**。该字符串将由分层排序的**部分组成，从更高的级别开始，进入更详细的内容**。**

**举几个例子:**

```
@footer-margin-bottom: 20px;
@pagination-border-top-width: 1px;
```

**变量名从较高的级别开始，字符串的每个后续部分定义了更详细的级别。**

**定义颜色的变量将以`color-`开始:**

```
@color-text-link-hover: @color-main;
@color-header-border-top: @color-border-light;
```

**变量必须被分组到逻辑组中，这些逻辑组必须由如下注释引入:**

```
// ------------------------------------ //
//  COLORS
// ------------------------------------ //
```

**名称不易理解的变量必须以这样的注释开头:**

```
// Sets the margin between the content and the footer
@content-margin-bottom: $spacing-unit;
```

**有时用相对于其他变量的值来定义变量是很有用的。这种情况发生在我们可以有一个链接的主色和一个悬停色的颜色上，悬停色是**一个相同颜色的浅色阴影**:**

```
@color-text-link: #0071bc;
@color-text-link-hover: lighten(@color-text-link, 25);
```

**这允许改变单个变量并影响我们 **variables.scss** 中的多个变量。**

## **3.4 文件命名约定**

**文件组织、文件命名和类命名是严格相关的，对于轻松管理项目非常重要:命名约定不仅影响类名，还影响一些文件名。对于**组件**和**布局**来说尤其如此。**

**例如，相同的**组件/布局名称**将用于:**

*   **组件/布局的 HTML 文件的**名称(不带前缀，见 3.2 类前缀)；****
*   **组件/布局对应 SASS 文件的**名称(不带前缀，见 3.2 类前缀)；****
*   **HTML 中根元素的**类；****
*   **根 SASS 类(文件中的第一个类)。**
*   **相关 **JS/PHP/*文件名**，视技术而定(无前缀，见 3.2 类前缀)。**

# **4.州**

**状态用于设计处于特殊瞬时条件的组件，如**激活**、**禁用**或**加载**。在这种情况下，我们不遵循 BEM 准则，但是我们**在组件**的**根元素添加了**一个独立的状态类。以前缀**“is-”**或**“has-”**开头的状态类。**

**一些例子:**

*   **`is-active`**
*   **`has-loaded`**
*   **`is-loading`**
*   **`is-visible`**
*   **`is-disabled`**
*   **`is-expanded`**
*   **`is-collapsed`**

**这些类不能有自己的样式，但总是在使用它们的组件的上下文中进行样式化。**

# **5.网格**

**网格样式不能像某些框架(比如 Bootstrap)那样硬编码成 HTML 中的类:虽然这实现起来很快，但是如果你想改变布局，你必须修改 HTML。此外，使用这种解决方案，在为响应式布局实现网格时，您没有充分的灵活性。**

**取而代之的是，我们使用 Suzy ，这是一个只用于网格的最小框架，它非常依赖于原生 CSS 属性，并帮助过渡到原生 CSS 网格布局。**

## **5.1 响应列布局基础**

**让我们看看如何建立一个 4 列布局和一个响应版本(内联注释)。**

```
/* 4 columns */
.list-container { // Parent container
    > .list-container__item { // Child elements to be displayed in 4 columns               float: left; // You can also use Flexbox
       width: span(3 of 12); // Susy
       margin-right: gutter(of 12); // Susy &:nth-child(4n) {
           margin-right: 0; // Sets 0 right margin to each element in the last column
       } &:nth-child(4n + 1) {
           clear: both; // Clears the float for each first element in a new row
       }
    }
}
```

**然后，同一布局可以在平板电脑中显示为两列布局(纵向)。**

```
/* ------------------------------------ *\
   #MEDIA-QUERIES
\* ------------------------------------ */
@media all and (max-width: $breakpoint-ipad-portrait) { /* 2 columns */
    .list-container {
        > .list-container__item { // Child elements to be displayed in 2 columns            &:nth-child(1n) { // Needed for specificity
                float: left;
                clear: none;
                width: span(6 of 12); // Susy
                margin-right: gutter(of 12); // Susy
            } &:nth-child(2n) { // Susy
               margin-right: 0; // Sets 0 right margin to each element in the last column
           } &:nth-child(2n + 1) { // Susy
               clear: both; // Clears the float for each first element in a new row
           }
        }
    }
}
```

# **6.格式化**

**在讨论如何编写规则集之前，让我们先熟悉一下相关的术语:**

```
[selector] {
    [<--declaration--->]
    [property]: [value];
}
```

**以下是一些格式指南:**

*   ****左括号 **( { )** 前的空格；****
*   **与最后一个选择器在**同一行的左括号**({)**；****
*   **左大括号 **( { )** 后新行的第一个声明**；****
*   ****属性和值总是在同一行**；**
*   **后的一个**空格——属性值定界冒号**(:)**；****
*   **每个**声明在自己的新行**(声明之间换行)；**
*   **将**闭合拉条** **( } )** 靠在自己的**新线上**；**
*   **每个**声明将**缩进四(4)个空格。**

**让我们看一个例子:**

```
.footer, .footer-bar {
    display: block;
    background-color: @color-page-background;
    color: @color-footer-text;
}
```

**避免为零值指定**单位:****

```
margin: 0; /* Good */
margin: 0px; /* No good */
```

**对于嵌套规则集，我们也在嵌套规则集前留一个空行。这里有一个例子:**

```
.footer {
    color: @color-footer-text; .bar {
        color: @color-footer-bar-text;
    }
}
```

**应该尽可能避免在 SASS 中嵌套，只在必要时使用，以获得期望的特性:根据经验，我们在每个 SCSS 文件的开头有根类，所有子元素都在第一层。**

## **6.1 空白**

**我们在规则集之间有**一个**空行，在代码的每个主要部分的末尾有**两个**空行。**

## **6.2 颜色**

**我们使用十六进制颜色代码(`#f0f0f0`)。不允许使用短的十六进制颜色和颜色名称(如**红色**)。十六进制颜色的字母必须小写。**

## **6.3 申报顺序**

**规则集中的声明将按照以下顺序组织:**

1.  ****箱子型号:** `display: block;
    float: right;
    box-sizing: border-box;
    width: 100px;
    height: 100px;
    margin: 0;
    padding: 0;`**
2.  ****定位:** **
3.  ****视觉**(边框&背景):
    `background-color: #f5f5f5;
    border: 1px solid #e5e5e5;
    border-radius: 3px;`**
4.  ****排版:** `font-weight: 100;
    font-size: 13px;
    font-family: Helvetica;
    line-height: 1.5;
    color: #333;
    text-align: center;
    text-transform: uppercase;`**
5.  ****杂项:** `animations, transforms, etc.`**

**这不是一个严格的规则(并且不会被 StyleLint 检查),但是我们鼓励您遵循这个原则。**

# **7.注释和代码部分**

**CSS 没有很好地讲述自己的故事，它是一种受益于大量评论的语言。您应该注释任何从代码本身看不明显的东西:没有必要告诉别人`display: none;`将隐藏一个元素，但是如果您使用`overflow: hidden;`来清除浮动——而不是剪裁一个元素的溢出——这可能是值得记录的事情。**

**在 SASS 中，我们可以以两种不同的方式使用注释:**

```
/* */
```

**和**

```
//
```

**主要的区别是**首先被处理并包含在结果中。css 文件**，而第二个**将被预处理器**忽略。由于这个原因，我们必须使用`/* */`语法来处理我们想要包含在。css 文件，而我们使用`//`来处理我们不想被编译的注释。当我们为生产编译一个**缩小的**和无注释版本的文件时，这没有什么区别，但是当我们开发时，我们可能会有一个未缩小的 CSS 文件，注释可能会非常有用。**

**一个明显的例子是 **variables.scss** :这在编译后的 css 文件中是透明的，所以使用`//`插入所有注释是一个很好的实践，否则，将会有没有相关代码的注释到处浮动。**

**我们确定了四种不同类型的评论。**

## **7.1 文件级注释**

**这些注释是**强制的**，必须解释当前 SASS 文档的内容:我们将它们放在每个 SASS 文件的开头。**

```
/**
 * DOCUMENT TITLE
 *
 * Text describing in detail what this file is doing...
 */
```

**如果我们想写同样的注释，但它不能被预处理器编译，请这样写:**

```
//
// DOCUMENT TITLE (COMMENT HIDDEN FROM COMPILER, LIKE VARIABLES)
//
// Text describing in detail what this file is doing...
//
```

## **7.2 章节级注释**

**每个文件中的 CSS 声明必须划分并组织在逻辑部分中。这些章节必须通过带有章节标题的**强制**注释进行介绍。只有当内容非常难以理解阅读代码，请提供一些额外的文本行。**

```
/* ------------------------------------ *\
   #SECTION-TITLE
\* ------------------------------------ */
```

**如果我们想写同样的注释，但它不能被预处理器编译，请这样写:**

```
// ------------------------------------ //
// #SECTION-TITLE
// ------------------------------------ //
```

## **7.3 选择器级注释**

**这些注释被插入选择器之前:它们并不总是强制性的，但是在编写不容易理解的 CSS 时是需要的。**

**示例:**

```
/* This set of declarations is doing this and that */
.selector {
}
```

**如果我们想写同样的注释，但它不能被预处理器编译，请这样写:**

```
// This set of declarations is doing this and that
.selector {
}
```

## **7.4 行内注释**

**这些是在编写难以理解的代码时我们需要的每个 CSS 声明级别上编写的注释。我们这样写:**

```
color: blue; /* The text color id blue */
```

**如果我们想写同样的注释，但它不能被预处理器编译，请这样写:**

```
color: blue; // The text color id blue
```

# **8.媒体查询**

**媒体查询**不应该写在一个单独的文件**中，而应该划分在每个 SASS 文件中，以定义每个特定组件、部分或布局的媒体查询行为。**

# **9.特征**

**保持**特异性低**。**

**总会有需要覆盖元素样式的时候，所以选择器的特异性越低，覆盖它就越容易。不仅如此，以这样一种方式覆盖，你甚至可以再次覆盖它，而不用疯狂地使用 **ID 选择器**或**！重要**。**

**要保持低特异性:**

*   ****限制 SCSS 文件中的**套料。组件和布局的经验法则是具有两级嵌套。**
*   **千万不要用**！重要**；**
*   **切勿使用 **ID 选择器**。**

# **10.胶料**

**必须使用像素设置单位和大小。**

****Font-size** 也会用像素表示(为什么？[看这里](https://hackernoon.com/rems-and-ems-and-why-you-probably-dont-need-them-664b9ce1e09f))。**

****行高**:无单位**行高**是首选，因为它不继承其父元素的百分比值，而是基于字体大小的倍数。**

# **11.资源**

*   **[编写合理的、可管理的、可伸缩的 CSS 的高级建议和指南](http://cssguidelin.es)**
*   **[CodePen 的 CSS](http://codepen.io/chriscoyier/blog/codepens-css)**
*   **[改进我们在 Trello 构建 CSS 的方式](http://blog.trello.com/refining-the-way-we-structure-our-css-at-trello/?utm_source=CSS-Weekly&utm_campaign=Issue-128&utm_medium=email)**
*   **Medium 的 CSS 其实相当不错。**
*   **[Evernote SASS 构建结构](https://github.com/evernote/sass-build-structure)**
*   **[可维护的 CSS](http://maintainablecss.com/)**

**[![](img/9914c5dd23ac08b70eea6f4f9ba6fed2.png)](https://levelup.gitconnected.com)****[](https://gitconnected.com/learn/css) [## 学习 CSS -最佳 CSS 教程(2019) | gitconnected

### 40 大 CSS 教程-免费学习 CSS。课程由开发者提交并投票，使您能够找到…

gitconnected.com](https://gitconnected.com/learn/css)**