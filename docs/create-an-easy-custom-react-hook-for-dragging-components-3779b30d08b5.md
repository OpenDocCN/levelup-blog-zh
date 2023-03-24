# 为拖动组件创建一个简单的自定义 React 挂钩

> 原文：<https://levelup.gitconnected.com/create-an-easy-custom-react-hook-for-dragging-components-3779b30d08b5>

## 或者，钩子会变成什么样？

![](img/6e07fcfae3704b0da6ff86b7cf07d972.png)

2003 年，著名的技术专家墨菲·李预言了我们当前基于模块化组件的 web 应用程序的状态，他问道:

*窟达钩 gon be？*

尽管他坚持认为自己既不需要也不想要钩子，但 web 开发人员今天面临的许多问题并不能简单地通过背景音乐、他们的耳机声音很大、很钝的声音来解决。

自从 2018 年末推出 Hooks 以来，它们已经成为 React 生态系统中至关重要的一部分，难怪它们*很棒。*

# 但是什么是钩子呢？

一个钩子可以是很多东西。

React 提供的基本挂钩允许您在功能组件中使用有状态逻辑和生命周期功能，在它们被引入之前，这些功能只能在基于类的组件中使用。有一些钩子允许你链接回调，记忆函数和对象，并直接与 DOM 的引用进行通信。

这只是 React 提供的开箱即用的挂钩。钩子真正开始发光的地方是当你把它们建造出来的时候。很有可能您想要重用的任何底层逻辑都可以表示为一个自定义钩子。React 社区知道这一点，并且已经创建了许多您现在就可以使用的定制挂钩。

正在访问`localStorage`？

[](https://www.npmjs.com/package/@rehooks/local-storage) [## @ re books/local-storage

### React 挂钩，用于启用与本地存储的同步。API 文档可以在这里找到。这可以是从…

www.npmjs.com](https://www.npmjs.com/package/@rehooks/local-storage) 

什么是钩子？

与`indexDB` api 同步？

[](https://www.npmjs.com/package/react-use-idb) [## 反应-使用-idb

### 管理单个 indexDB 项的 React 副作用挂钩。在旧仓库上的一次顺便更换。本地存储是…

www.npmjs.com](https://www.npmjs.com/package/react-use-idb) 

什么是钩子？

甚至像查看你的电脑是否在线这样简单的事情。

[](https://www.npmjs.com/package/@rehooks/online-status) [## @rehooks/online-status

### React 挂钩，用于订阅“在线”/“离线”事件和“navigator.onLine”属性，以查看当前状态

www.npmjs.com](https://www.npmjs.com/package/@rehooks/online-status) 

瓦特。达。钩子。耿。是吗？

# 听起来很棒！所以，下载一堆钩子吧？

你可以的。如果你时间紧迫，这就是我的建议。上面的挂钩和许多类似的挂钩工作良好，经过测试，应该可以帮助你解决你面临的任何问题。

但是那不会帮助你了解他们是如何工作的。

这就是为什么今天我要和你一起工作，帮助你构建你自己的可重用钩子来创建可拖动的组件。

你可以在 [GitHub](https://github.com/timmalstead/watDaHookGonBe) 上找到完整的代码。

# 1.准备和安装

在你的终端输入`npx create-react-app wat_da_hook_gon_be`开始吧。

![](img/07181cfb23d529531ceac6bce39d9bf0.png)

钩子会是什么？

下载上面 200 x 200 px 的图片，保存到你的`src`文件夹，重命名为`albumcover.jpg`，然后将下面的文字复制到你的`App`文件中。

如果你输入`yarn start`到你的终端，你应该在浏览器的左上角看到图片。

# 2.添加起始位置和状态

在我们正在构建的钩子中将会有相当多的事情发生。因此，为了我们双方的利益，我将把事情分解成几个步骤，并解释我在这个过程中所做的事情。

我知道当我学习新概念和新技术时，我喜欢缓慢的、一步一步的解释。所以我会尽可能简单地分解它。

用以下代码修改`App.js`:

我们来分析一下这是怎么回事。

在我们第一次导入的顶部，我们正在导入`useState`钩子。

`useState`是 React 中功能组件的主力。它接受一个参数，一个要使用的初始状态，并返回两个值:一个表示状态的变量和一个更新状态的函数。在使用函数更新状态时，会触发一个 rerender，并更新订阅状态变量的任何内容。

在我们上一步设置好内联样式之后，我们正在从全局`window`对象中析构`innerHeight`和`innerWidth`属性。

在下一行中，我们将这些析构值用于设置我们的初始位置值。我们将它们除以二，然后从中减去 100。由于我们的图像是 200 x 200 像素，这将设置我们的图片的中间到我们的浏览器的中间。

接下来我们有了`useState`的初始值。首先，我们将布尔值`isDragging`设置为`false.`,这将用于判断组件是否被主动拖动。接下来的三个属性将始终是包含 x 和 y 值的普通对象。这些代表屏幕的 x 轴和 y 轴，以像素为单位。我们将从 x : 0 和 y : 0 处的`origin`开始。`translation`和`lastTranslation`，我们稍后会详细讨论，将从我们的`startingPosition`变量中设置的值开始。

屏幕上还没有发生任何变化，但我们正在前进。

# 3.添加 handleMouseDown

处理鼠标事件时，一个非常常见的模式是将逻辑分解为三个函数，处理鼠标按钮被单击、鼠标被移动和鼠标按钮被释放时的逻辑。我们将利用这个模式的三个功能，称为`handleMouseDown`、`handleMouseMove`和`handleMouseUp`。

观察下面的代码。

在我们开始之前，我们将从我们的`dragInfo`状态变量中析构`isDragging`，以使事情更容易处理。

`handleMouseDown`函数将接受两个参数`clientX`和`clientY`，这两个参数是由触发鼠标事件构造的。这些是我们单击想要拖动的元素时 X 和 Y 轴上的坐标。不太复杂吧？

如果`isDragging`设置为假，我们将进入我们的功能。我们将调用我们的`setDragInfo`方法来更新我们的`dragInfo`状态，将`isDragging`更改为`true`，将`origin`坐标更改为`clientX`和`clientY`。

# 4.添加 handleMouseMove

无论我们按住鼠标多长时间，只要我们移动鼠标，`handleMouseMove`功能就会一次又一次地触发。所以，我想我们应该试着把它做好，你说呢？

看哪！

像我们最后一个函数一样，`handleMouseMove`将析构的`clientX`和`clientY`属性作为参数。如果`isDragging`解析为`true`，则`origin`和`lastTranslation`值从`draginfo`开始被析构，我们再次调用`setDragInfo`动作来更新我们的状态。你知道这实际上是如何一次又一次地快速更新状态的吗？

我们现在唯一感兴趣改变的值是`translation`。我们将添加我们在`mouseDown`函数中存储的`origin`值，将它们添加到我们最后的翻译值中，并从鼠标事件触发的`client`参数值中减去它们。为了避免得到负值，我们将把整个等式放在`Math.abs`中。这个函数调用绝对值，所以只有正值会从这个函数返回，因此它会留在屏幕上。

下一步，我们将把`translation`值直接附加到可拖动对象的 CSS 值上，在每次渲染时更新它们。

# 5.操作鼠标并把它们绑在一起

对于我们的最后一个函数，我们将*不*需要任何参数，因为我们要做的就是关闭我们的拖动，并为我们的下一次拖动设置一个值。

为此，如果我们的`isDragging`变量解析为`true`，我们将从`dragInfo`析构`translation`。请记住，这是我们刚刚在`handleMouseMove`函数中更改了很多次的值。再次调用我们的`setDragInfo`方法，我们将把`isDragging`更改为`false`，结束拖动循环，将`lastTranslation`的值设置为从`handleMouseMove`返回的`translation`的最后一个值，并传播其余的值。

是啊！真的就这么简单。差不多就是用鼠标更新 x 和 y 的值。但是在它工作之前，我们需要以某种方式将所有这些元素附加到我们的元素上。我们用内嵌样式变量`picturePosition`来做这件事。它将我们的`position`规则改为`absolute`，并将`right`和`bottom`规则附加到`translation`的 x 和 y 值上。因为它是在每次渲染时生成的，所以当我们拖动它时，它会为我们拖动的项目生成一个新的位置。

然后，在`picturePosition`和`pictureStyle`中展开内联样式对象，并将鼠标事件附加到我们希望被拖动的元素上适当的合成事件监听器，这是一件简单的事情。

但是等一下，为什么有两个事件连着`handleMouseMove`？当鼠标离开元素时，我们不希望它，你知道，离开吗？

尝试在没有侦听器的情况下使用此代码。大部分情况下，这是可行的。问题是当你移动指针太快时，通常鼠标移动的速度会超过函数的速度，你会失去拖动。或者，您不会失去拖动，但可能会发生其他奇怪的行为，如不再能够悬停在元素上。将`handleMouseMove`连接到`onMouseMove`和`onMouseLeave`上消除了这种头痛。由于我们使用布尔值来检测拖动是否发生，我们不必担心拖动状态会陷入永久的“开”状态。

就是这样！一个可拖动组件附加到一个元素！

虽然它不是非常模块化。在我们的下一步中，我们将看到钩子真正闪光的地方，并将它抽象成一个可重用的`useDrag`钩子

# 6.使用抹布

在您的`src`文件夹中创建一个名为`useDrag`的新文件，并将下面的代码移入其中。

在这里，我们把所有的逻辑都放在了拖出我们的组件上，并把它放在一个我们可以在需要的时候一次又一次重用它的地方。

完成之后，您可以将`App`更改为以下内容。

# 最后的想法

今天，我们已经知道如何用一个简单的三函数过程来处理鼠标按下事件、鼠标移动事件和鼠标抬起事件，从而使任何元素都可以拖动。我们还学习了如何将该模式抽象成一个可重用的钩子，因此我们可以将它用于任何我们需要的组件。我希望这篇文章对你来说是令人愉快和有启发性的，并且你喜欢阅读它就像我喜欢写它一样。