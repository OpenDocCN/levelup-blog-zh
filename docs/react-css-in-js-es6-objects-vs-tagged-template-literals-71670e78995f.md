# React & CSS-in-JS: ES6 对象与标记模板文字

> 原文：<https://levelup.gitconnected.com/react-css-in-js-es6-objects-vs-tagged-template-literals-71670e78995f>

![](img/5032796e5176728b5d624d0055e8be75.png)

当谈到 CSS-in-JS 时，一个值得讨论的重要话题是使用标记模板文字(TTLs)来包含 CSS 语法与使用对象。我最近在[的另一篇文章](https://medium.com/@ESR360/why-i-dislike-existing-css-in-js-solutions-for-react-7b81786e0fd5)中提到“我个人无法忍受 TTLs”，但没有提供任何资格或理由。我没有意识到这个观点如此有争议…

![](img/3265eeda735b54491cd363dc0073279c.png)

我曾天真地认为，其他所有人都和我一样，认为他们有点不诚实，因此只不过是对一个没有更好解决方案的问题的一个可容忍的变通办法。事实上，很多人真的很喜欢它们，所以在这篇文章中，我想解释为什么我觉得在 TTLs 中使用对象是一种更好的 Sass 语法方法，但实际上，这确实取决于您的偏好以及您希望您的 API 是什么样的。

我想在 TTLs 中使用 Objects 而不是 Sass 的最初动机是，因为我使用 JavaScript 进行设计，所以对所有逻辑和抽象使用普通的 JavaScript 似乎更有意义，而不是在字符串中编写代码(从而编写逻辑),这本质上就是 TTLs 中的 CSS。

尽管如此，考虑下面的伪代码利用 TTLs 来包含一些 Sass 语法:

这段伪代码可能已经很好地适用于现有的解决方案，但是我想如果我发现自己在做这样的事情，我会问自己的第一件事是，为什么不直接使用 Sass，因为它看起来是一样的？除了作用域样式的潜在好处(使用一些定制的 Sass 库，甚至像 BEM 这样的命名约定，也可以解决作用域的问题)，[这篇文章](https://spin.atomicobject.com/2018/12/28/css-in-javascript-benefits/)指出，人们可能选择 CSS-in-JS 而不是 Sass 的其他原因包括:

*   用普通的 JavaScript 定义你的助手函数
*   将 CSS 和 React 组件放在一起
*   类型检查和内联文档
*   *懒惰！*

这些原因可能会(也可能不会)让某些人相信使用 CSS-in-JS 优于使用 Sass，但这并不一定解释为什么如果要使用 CSS-in-JS，他们会选择在 API 中使用包含 Sass 语法的标记模板文字。好的，当然，它*类似于* CSS/Sass，允许您从其他地方复制和粘贴一些现有的 CSS/Sass 代码，并使用允许您遵循熟悉的样式范例等的语法，这当然都是很好的理由，但让我们考虑一下，我们在这里使用 JavaScript 并做出反应…

JavaScript，尤其是 ES6 语法和其他语法，提供了很多简洁的方法，我们可以加以利用。React 还提供了许多做其他事情的简洁方法。由于我使用 JavaScript 处理我的样式，并结合使用 React，我的第一个自然选择是利用这些工具已经提供给我的范例。

所以我最初考虑的是 JavaScript 对象、箭头函数、React 组件、状态、道具、上下文等等。这些是我希望我的理想样式 API 所基于的概念。如果我的样式 API 是基于选择器嵌套、层叠、div、类名等(即 TTLs 中的 Sass 语法)，我就需要担心另一个抽象层。而且不仅仅是在思维方面。您或您的解决方案需要获取 React 组件及其相应的状态/属性/上下文等，并以某种方式将它们映射到您的 Sass 声明，通常以类名的形式。用简单的英语来说，大概是这样的:

1.  (JSX)在这个组件上，如果`props.someProp`为真，添加这个类名
2.  (TTLs 中的 Sass)在此组件上，如果应用了此类名，请添加这些样式

…这似乎或多或少就是在使用 React 时使用 TTLs 来包含 Sass 语法时所发生的情况(或者，实际上，如果您也使用常规 CSS/Sass)。

考虑下面的代码，它将解决与前面的标记模板文本示例完全相同的问题(即，当满足相同的条件时，将相同的样式应用于相同的组件)，除了利用前面提到的概念(对象、箭头函数、组件、状态、上下文等):

在这两个例子中，或多或少发生了相同的事情，以或多或少相同的方式。对我来说，后一个使用对象、箭头函数、状态和上下文等的例子更有吸引力。旅程现在变成了:

1.  (JS 对象)在这个组件上，如果`props.someProp`为真，添加这些样式

…不再需要额外的抽象层(即第二步)。这几乎感觉像是 API 对 React 组件进行样式化的圣杯；感觉干净，务实，懂事。我还觉得，当使用后一个例子时，传递了更多有用的信息(可能是因为去除了抽象层)，而且*看起来比 TTLs 例子更好*(再次声明，这只是我的观点)。它还可以让你的 JSX 更干净。

看看提议的 API，我们在这一点上不再真正处理 CSS，没有级联的证据，所以我对将解决方案称为 CSS-in-JS 感到有些不安。它更像是 JavaScript 样式，或者 JavaScript 样式表(JSS…JSSS？).这个名字似乎已经被[占用了](https://cssinjs.org/?v=v10.0.0-alpha.23)(请注意，是由一个非常好的工具使用的)，所以尽管没有层叠，但继续把它看作 CSS-in-JS 解决方案可能更实际。

## 结论

提议的使用对象的 API 的唯一有争议的缺点(我能想到的)是，它自然地推动使用内联样式，而不是真正的 CSS，一些人认为这是一个交易破坏者(鉴于其他好处，我个人对此没有太多意见)。虽然性能很重要，但我需要做一些实际的基准测试，以确定大规模使用内联样式是否有任何明显的缺点。在我的书中，开发者体验的好处是显而易见的。在任何情况下，我确信一些向导可以采用提议的 API，并且在必要的时候找到一种方法从它生成真正的 CSS。

如果您喜欢这篇文章，请查看其他相关文章:

*   [为什么我不喜欢 React 现有的 CSS-in-JS 解决方案💅🚫](https://medium.com/valtech-design/why-i-dislike-existing-css-in-js-solutions-for-react-7b81786e0fd5?source=your_stories_page---------------------------)
*   [美丽状态&基于上下文的样式与 React (CSS-in-JS)](https://medium.com/@ESR360/beautiful-state-context-based-styling-with-react-css-in-js-781a14cb2f9b?source=your_stories_page---------------------------)

![](img/0f4eaa82f76695a20f09ba4fa9fd37ba.png)

[Twitter](https://twitter.com/esr360) | [Github](https://github.com/esr360)