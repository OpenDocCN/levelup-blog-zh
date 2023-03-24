# 使用 Javascript 数组时提高代码可读性

> 原文：<https://levelup.gitconnected.com/improve-code-readability-while-working-with-javascript-arrays-419d48dd21b>

## Javascript 数组方法完全指南

![](img/e3578c447c252464a3c729281d24095d.png)

在 [Unsplash](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上[马扎尔·赞德萨利米](https://unsplash.com/@m47h4r?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)的照片

多年来，在与许多其他开发人员一起创建 Javascript 应用程序时，我发现最引人注目的一件事是开发人员在使用数组时采用的方法缺乏多样性。在过去的几年里，Javascript `Array`全局对象中增加了许多有用的函数。

除了消除在如此多相似或相同的编码情况下不断重新发明轮子的需要之外，这些功能还为开发人员提供了将代码留给其他开发人员的能力，这些代码*更加整洁*并且*更加可读*。

在我的职业生涯中，当指导其他开发人员时，我一直强调的一件事是编写像任何简单的英语句子一样易读的代码的重要性。不仅您的逻辑应该根据您正在编写的软件的要求运行，而且对于您或将来阅读您的代码的任何人来说，弄清楚代码到底在做什么应该是一项快速而简单的任务。

作为开发人员，我们每天都要查看成千上万行代码。即使代码写得很好，仔细检查大型复杂的应用程序以弄清楚它们是如何工作的，这远不是一件容易的事情，在许多情况下是一件相当痛苦的事情。当代码晦涩难懂时，它肯定不会让这些任务变得更容易或更痛苦。在本文中，我将讨论一些 Javascript `Array`函数，您不仅可以用它们在代码中注入一些变化，更重要的是，当使用数组时，可以提高代码的整体可读性。

# for 循环

说到使用数组，所有现代技术的鼻祖是循环的[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Loops_and_iteration#for_statement)*。在 Javascript 中，一个用于循环的*有三大特点:**初始表达式**，允许你设置循环迭代的开始标准；**条件表达式**，允许您设置循环迭代的结束标准；以及**增量表达式**，它允许您设置从循环迭代的开始到结束的增量推进的标准。**

**for loop* s 的另一个有用特性是能够使用 [*break*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Loops_and_iteration#break_statement) 语句，这允许您在满足循环逻辑中定义的特定标准时提前结束循环迭代。实际上，在 Javascript 中， *for 循环*并没有特别绑定到数组上，但是它们是最早的可以有效地、有逻辑地使用数组的方法之一。*

*为了完整起见，下面的代码示例演示了在 Javascript 中使用循环的*来计算费用列表的总成本:**

*传统 Javascript for 循环*

*作为一名开发人员，在看到这段代码时，我需要停下来回顾一下逻辑，弄清楚这段代码到底想做什么。首先，我需要查看在循环的*顶部定义的三个表达式，以确定它们在做什么。如果这些表达式中的任何一个被定义为与这个代码示例中的不同，这将被认为是定义循环*的*的典型和最常见的方式，那么对于这个代码示例到底试图实现什么可能会有一些混淆。在检查完表达式后，我需要检查循环逻辑*的*来弄清楚它试图做什么。**

*这个代码示例没有这样做，但是如果代码示例还包含了一个 *break* 语句，甚至可能需要更多的时间来弄清楚整个 *for 循环*到底想要实现什么，因为这会给 *for 循环*逻辑带来额外的复杂性。*

*在这个特定的代码示例中，对一个费用列表的总计进行求和，以生成所有费用的总计。如果您将这个代码示例作为一个句子来阅读，它可能看起来像这样:*对于费用列表中的每一笔费用，从第一笔费用到最后一笔费用，费用总计被添加到总计中。**

*我提供这个典型的 *for 循环*用法的例子的主要目的是，与我们将在本文后面看到的其他代码示例相比，展示其较低的可读性。此外，为了简化比较过程，本文中的所有后续代码示例都将使用第一个代码示例中定义的相同数据集。*

# *Array.forEach()函数*

*我想看的第一个函数是`[forEach()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)`函数，如果您要从前面的代码示例中以函数形式创建循环的*，它基本上就是您将得到的。如果您想通过将循环*直接转换成函数来提高所编写的循环*的可读性，那么`forEach()`函数就是您在该转换过程中要使用的函数。**

*这个函数很简单，因为它只接受一个参数，一个应用于原始数组中每一项的参数函数。这个参数函数的唯一参数是每个数组项。我将在本文中介绍的大多数函数都有与这个函数相同的函数签名。让我们来看一个使用`forEach()`函数的代码示例，以及用于比较的循环代码的等效*:**

*Javascript Array.forEach()函数*

*正如您在这个代码示例中看到的，传递给`forEach()`函数的参数函数中的逻辑几乎与循环代码的等效*中包含的逻辑相同。通过将该逻辑转移到一个具有更清晰名称的函数中，可以更容易地阅读和理解整个费用列表中每个*项目的*总和被添加到函数外部的变量中，以便产生所有费用的总计。**

*由于给这个函数起了一个合适的名字，您可以这样阅读这个代码示例中的`forEach()`函数代码:*对于费用列表中的每笔费用，将费用总计添加到总计中。*尽管两个不同版本的代码中的逻辑几乎相同，但这句用于`forEach()`功能代码的话比用于描述循环代码的等效*的话更清晰简洁地表达了正在发生的事情。**

*使用`forEach()`函数以及本文中介绍的所有函数的一个缺点是，您不能显式地指定一个 *break* 语句来提前结束函数的执行。然而，这不是问题，因为一些函数实际上有内置的 *break* 语句，在大多数情况下，它们会在迭代整个数组之前提前结束循环执行。以这种方式运行的函数取决于它们的预期目的。在本文后面讨论函数的时候，我会在适当的地方说明这种行为何时可能发生。*

# *数组.映射()函数*

*除了循环的*之外，我们可以用来提高代码可读性的另一个函数是`[map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)`函数。除了`forEach()`函数，`map()`函数可能是我在其他开发人员编写的 Javascript 代码中遇到的使用最广泛的数组函数之一。除了函数返回值之外，`map()`函数与`forEach()`函数基本相同。`forEach()`函数不返回任何结果，只是对数组中的每一项执行一个参数函数。**

*`map()`函数对数组中的每一项执行参数函数，但是它返回一个新的列表，该列表包含该参数函数每次迭代的返回值。该参数函数是`map()`函数接受的唯一参数。*

*选择使用`map()`函数还是`forEach()`函数通常取决于您是否需要生成一个包含与原始数组相同数量项目的全新数组。正如函数名所示，使用`map()`函数时，您所做的是*将原始数组中的每一项映射*到结果数组中一个全新的项。让我们来看一个使用`map()`函数的代码示例，以及用于循环代码的等效*进行比较:**

*Javascript Array.map()函数*

*在这个代码示例中，我们使用了与`forEach()` function 代码示例相同的输入，但是我们没有使用外部数组，而是生成了一个填充了更新后的费用列表的全新数组。除了原始数组中包含的所有数据之外，这个新数组中的每一项还包含税前费用总额。*

*与在`forEach()`功能代码示例中相比，`map()`功能代码的逻辑和循环代码的等价*更加明显不同。在函数执行完成时，函数简单地产生它自己的新数组，而不是必须使用外部数组。该代码示例中的`map()`函数代码可以这样理解:*更新一个费用列表，这样每笔费用也包含税前费用总额。***

*这只是一个例子，说明了`map()`函数与`forEach()`函数相比是如何有用的。任何时候，当您想要基于现有数组中的项目生成一个新的项目数组时，使用`map()`函数。新的项目数组可能是我们在此代码示例中看到的原始数组中的更新项目列表，也可能是与原始数组没有直接结构连接的全新列表。*

*使用`map()`函数时要记住的关键是，新数组的长度将与原始数组的长度相同。`forEach()`函数更适合于当您想要处理一些现有的外部值或数据结构，并且不需要该函数产生一个全新的数组时。*

# *数组.过滤器()函数*

*与`forEach()`和`map()`函数相似但产生不同结果的函数是`[filter()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)`函数。像`forEach()`和`map()`函数一样，`filter()`函数遍历原始数组中的每一项。类似于`map()`函数，它产生一个新的项目数组，但是不同之处在于新数组中的项目与原始数组中的项目相同，对于原始数组，`filter()`函数的参数函数返回一个真值。让我们来看一个使用`filter()`函数的代码示例，以及用于比较的循环代码的等效*:**

*Javascript Array.filter()函数*

*在这个代码示例中，我们使用与以前相同的输入，但是这次我们将费用的完整列表过滤到一个只包含食物费用的新列表中。就像在`map()`功能代码示例中一样，`filter()`功能代码和循环代码的等效*之间的差异是实质性的。就像使用`map()`函数一样，不需要使用外部数组，因为`filter()`函数在函数执行完成后会产生自己的新数组。该代码示例中的`filter()`函数代码可以这样理解:*将费用列表过滤成食物费用列表。***

*与`map()`函数不同，它保证返回一个包含与原始数组相同项数的数组，`filter()`函数可以返回一个包含零个或与原始数组一样多的项数的数组。这完全由传递给`filter()`函数的参数函数中包含的逻辑决定。*

# *数组. reduce()函数*

*在介绍了`map()`和`filter()`函数之后，另一个可以帮助我们提高代码可读性的有用函数是`[reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)`函数。这个函数比我们到目前为止讨论过的其他函数稍微复杂一点，但是在很多方面，它是`map()`和`filter()`函数的结合。对于参数,`reduce()`函数接受一个将应用于数组中每一项的参数函数，以及一个将在第一次迭代时传递给该参数函数的初始值。*

*parameter 函数接受两个参数。第一个参数是一个累加器，它从第一次循环迭代开始，作为传递给`reduce()`函数的初始值。该累加器参数将继续被带入下一次循环迭代，作为参数函数前一次迭代返回的值，一旦执行完成，它将最终成为`reduce()`函数返回的值。为了更好地理解这是如何工作的，让我们来看一个使用`reduce()`函数的代码示例，以及用于比较的等价的 *for 循环*代码:*

*Javascript 数组. reduce()函数*

*`reduce()`函数很特殊，因为它不像这里的循环代码中的等价*那样依赖于外部变量的使用，甚至在`forEach()`函数的情况下，该函数在循环迭代中保持自己的内部变量。在这个代码示例中，变量是参数函数第一个参数`total`，它将从传递给`reduce()`函数的第二个参数`0`的值开始。该变量相当于`forEach()`功能代码示例中的总计变量。该代码示例中的`reduce()`函数代码可以这样理解:*将费用列表简化为食物费用总计。***

*我之前提到的`reduce()`函数几乎就像是`map()`和`filter()`函数的结合体的原因是，它能够在一个函数中实现与这两个函数相同的目的。这消除了将这两个函数分别应用于单个数组的需要，将一个函数执行的结果通过管道传递给另一个函数。*

*`reduce()`函数的作用与`map()`函数相同，因为它允许您根据数组中每一项的值返回不同的值。`reduce()`函数的作用与`filter()`函数相同，因为它允许您选择数组中的哪些项将导致一个值反映在最终的函数返回值中。*

*这个`reduce()`函数代码样本表面上看起来可能比循环的*代码样本更复杂，但我想介绍这个函数的一个原因是，我认为它实际上优于循环*实现的等效*。这种优势源于这样一个事实，即`reduce()`函数不需要依赖于函数外部任何变量的使用来在循环迭代中保持其状态。**

*初始值参数允许您指定一个工作起点，并且您可以在整个函数执行期间以您认为合适的方式在该初始值的基础上进行构建。这使得函数的使用更加自包含，我认为这最终增加了它的可读性，超过了循环实现的*。**

# *Array.every()函数*

*`[every()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every)`函数是本文中讨论的第一个函数，它不能保证在函数执行完成之前遍历原始数组中的每一项。它也不同于前面的大多数函数，因为它不返回与原始数组中的项目相关的项目数组。相反，它返回一个简单的布尔值。*

*`every()`函数接收一个参数函数作为其唯一的参数，该函数的唯一参数是原始数组中的每个连续项。该函数将遍历数组中的每一项，直到遇到 parameter 函数返回一个 [falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) 值的项。如果发生这种情况，函数会提前结束执行并立即返回`false`。否则，它遍历原始数组中的每一项并返回`true`。让我们来看一个使用`every()`函数的代码示例，以及用于比较的循环代码的等效*:**

*Javascript Array.every()函数*

*在这个代码示例中，我们使用与以前相同的输入，但是这一次我们检查费用列表中的每笔费用是否都低于预算阈值。从正在执行`every()`函数的费用列表中，很容易看出该函数将在第一次迭代后立即结束执行并返回`false`。当与等效的 *for 循环*代码示例(也将在第一次迭代后结束循环的执行)相比时，我相信使用`every()`函数比使用 *for 循环*的好处是显而易见的。*

*在循环代码的等价*的情况下，需要一个外部变量来跟踪循环迭代的最终结果，我认为这立即使它比`every()`函数实现的可读性差得多，后者将所有东西都放在内部，直到产生最终的函数结果。该代码示例中的`every()`函数代码可以这样理解:*检查费用列表中的每笔费用的总费用是否低于 100。***

*`every()`函数的真正好处是当你在代码中使用这个函数时你所说的话。在一个数组上使用这个函数意味着您正在验证该数组中的每一个项都通过了您在参数函数中实现的任何逻辑检查。*

*此外，您还表示，如果其中一项没有通过逻辑检查，您希望立即得到通知。这种逻辑检查相当于短路，可以比检查数组中的每一项更快地为您的查询提供答案。唯一需要遍历整个数组的时候是数组中的所有项都通过了逻辑检查，这是完全可以接受的，也是逻辑上必须的。*

# *数组. some()函数*

*虽然`every()`函数的主要用途是检查单个逻辑失败情况下的数组，但是`[some()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)`函数的主要功能是检查单个逻辑成功情况下的数组。这两个函数本质上是截然相反的，它们的目的和处理数组的方式完全不同。像前面的大多数函数一样，`some()`函数接受单个参数，一个参数函数，该函数的唯一参数是原始数组中的每个连续项。*

*该函数将遍历数组中的每一项，直到遇到 parameter 函数返回一个 [truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) 值的项。如果发生这种情况，函数提前结束执行并立即返回`true`。否则，它遍历原始数组中的每一项并返回`false`。让我们来看一个使用`some()`函数的代码示例，以及用于比较的循环代码的等效*:**

*Javascript 数组. some()函数*

*在这个代码示例中，我们始终使用相同的输入，同时检查费用列表中是否至少有一项费用的总额超过了预算阈值。从我们现在已经习惯使用的开销列表中，很容易看出该函数将在第一次迭代后立即结束执行并返回`true`。该代码示例中的`some()`函数代码可以这样理解:*检查费用列表中的某项费用的总费用是否超过 100。**

*就像使用`every()`函数代码示例一样，在这个代码示例中使用`some()`函数比使用 *for 循环*具有明显的优势。事实上，如果您比较来自两个代码样本的 *for loop* 等价代码，您会发现它们几乎是相同的，除了外部变量的初始值与在 *for loop* 中定义的短路逻辑值进行了交换。这是一个真正的问题，因为如果您看到两个相邻代码样本中的 *for 循环*等价代码，并不能立即看出它们实际上在预期的逻辑目的上完全相反。*

*将`some()`和`every()`函数放在一起使用不会出现同样的问题，这在一定程度上是因为参数函数中包含的逻辑显然是相反的，但主要是因为实际函数的名称明显非常不同，并且指示相反的行为。*

*就像使用`every()`函数一样，`some()`函数的真正好处是当你在代码中使用这个函数时你所说的话。在数组上使用这个函数意味着您正在验证该数组中的某个*项目是否通过了您在参数函数中实现的任何逻辑检查。**

*此外，您还表示，一旦其中一个项目通过逻辑检查，您希望立即得到通知。这种逻辑检查相当于短路，可以比检查数组中的每一项更快地为您的查询提供答案。唯一需要遍历整个数组的时候是当数组中没有一项通过逻辑检查时，从逻辑的角度来看这是有意义的。*

# *数组. find()函数*

*我想讨论的最后一个函数是`[find()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find)`函数，它与`some()`函数非常相似，但是它不像那个函数那样返回一个简单的布尔值。`find()`函数接受单个参数，一个参数函数，该函数的唯一参数是原始数组中的每个连续项。*

*像`some()`函数一样，`find()`函数将遍历原始数组中的每一项，直到遇到参数函数返回真值的项。但是，如果发生这种情况，函数会提前结束执行，并立即返回为其返回真值的数组项，而不是简单的布尔值。否则，该函数遍历原始数组中的每一项，并返回`undefined`。让我们来看一个使用`find()`函数的代码示例，以及用于比较的等价 for 循环代码:*

*Javascript Array.find()函数*

*该代码样本与`some()`函数代码样本几乎相同，无论是在`find()`函数的使用上，还是在 *for 循环*等价代码上。事实上，唯一真正的区别在于函数执行结束时作为最终值返回的内容。从我们在本文中使用的开销列表中可以清楚地看到，该函数将在第一次迭代后结束执行并返回列表中的第一项。该代码示例中的`find()`函数代码可以这样理解:*在费用列表中查找费用合计超过 100 的费用。**

*就像在`some()`函数代码示例的情况下一样，这里的`find()`函数代码显然更胜一筹，因为它简洁，能够在内部维护所有必要的结果，直到函数执行完成，并且函数名清楚地表明了在原始数组上正在执行什么类型的操作。就像`every()`和`some()`函数一样，如果你看到`find()`函数的 *for 循环*等价代码紧挨着其中一个函数的代码，那就需要更多的注意力和思考来区分这两个 *for 循环*的最终目的。*

*由于`find()`函数和`some()`函数除了它们的返回值之外工作方式相同，所以您选择使用它们是由您希望对函数返回值做什么来决定的。如果您只需要知道一个数组包含至少一个通过逻辑检查的项，并且不需要知道该项的更多细节，那么使用`some()`函数。如果您需要了解该项目的更多详情，请使用`find()`功能。*

# *结论*

*在本文中，我们介绍了我观察到的开发人员在 Javascript 中与数组交互的几种最常见的方式(`forEach()`和`map()`)。此外，我还介绍了这些常用函数的几种替代方法，不仅是为了强调它们的存在，也是为了解释在什么样的情况下可以使用它们来更好地传达代码的意图，并最终提高代码的整体可读性。我希望这篇文章能够帮助许多开发人员在涉及到所有 Javascript 数组相关的编码情况时拓宽他们的视野。*

# *资源*

*[Javascript for 循环](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Loops_and_iteration#for_statement)*

*[Javascript break 语句](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Loops_and_iteration#break_statement)*

*[为循环代码样本](https://gist.github.com/keith-victor-dawson/5b82701cf4285ab15e63be23979a835c)*

*[Javascript Array.forEach()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)*

*[forEach()函数代码示例](https://gist.github.com/keith-victor-dawson/511085fa6f46a4a1c67ffcf14bfdb517)*

*[Javascript Array.map()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)*

*[map()函数代码示例](https://gist.github.com/keith-victor-dawson/109ebc877414a83d471b739bda8d675d)*

*[Javascript Array.filter()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)*

*[filter()函数代码示例](https://gist.github.com/keith-victor-dawson/e66e3156762510a90ccfad583bab54d7)*

*[Javascript Array.reduce()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)*

*[reduce()功能代码示例](https://gist.github.com/keith-victor-dawson/c674f75ca71b6c002f89cce25d5cad70)*

*[Javascript Array.every()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every)*

*[Javascript Falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)*

*[every()功能代码示例](https://gist.github.com/keith-victor-dawson/a568a9d3993084a062793f45e1860030)*

*[Javascript Array.some()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)*

*[Javascript Truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)*

*[一些()功能代码示例](https://gist.github.com/keith-victor-dawson/6a9f3b9cd48a3ab6b7c426714fd9a827)*

*[Javascript Array.find()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find)*

*[find()函数代码示例](https://gist.github.com/keith-victor-dawson/8076de185468138b4f92520254df550c)*