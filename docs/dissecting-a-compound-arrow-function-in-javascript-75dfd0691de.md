# 剖析 JavaScript 中的复合箭头函数

> 原文：<https://levelup.gitconnected.com/dissecting-a-compound-arrow-function-in-javascript-75dfd0691de>

![](img/d65cf489d6b9a3e1a8326ed5530b5290.png)

照片由 [Jungwoo Hong](https://unsplash.com/@hjwinunsplsh?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

与传统函数相比，ES6 中引入的箭头函数非常简洁。如果你不喜欢传统函数的额外语法，箭头函数的精简特性会非常吸引人。此外，如果您需要执行稍微复杂一点的任务，将其中的几个放在一起可以让您获得想要的结果，而不会让您的代码看起来太复杂。我举个例子说明一下。

假设我有一个包含来自一个表的数据的数组，我想用来自另一个表的额外数据元素增加每个表行，假设有外键将两个表联系起来。示例代码是用 React/Redux 编写的，但是我将绕过用来自后端 API 的 JSON 数据填充 Redux 存储的部分。请记住，我们讨论的两个 Redux 存储项包含我们正在使用的两个表。

![](img/fc2e159839cf43f717d73ce09f6803dc.png)

“buckets”，第一个 Redux 存储项，是一个包含具有多个元素的对象的数组，其中一个元素是 course_id。第二个 Redux 存储项“courses”是一个数组，它包含像球场 GPS 位置这样的对象元素，而 course_id 也是标识符。我在这里所做的是对于 buckets 数组中的每个数组条目，我用 courses 数组中找到的 course GPS 位置来扩充它。

因为我要从一个现有的数组创建一个新的数组，所以 map 函数是完美的解决方案，因为该函数的结果是一个新的数组。在这种情况下，我将地图的结果赋给一个常量 newBuckets。这是代码开始的地方，但具有讽刺意味的是，这实际上是结束，因为超过这一点发生的任何事情都会导致该常数的内容。

接下来是 map 函数，也是用 arrow 函数语法写的。让我们简化一下，看看它是如何工作的:

this . props . buckets . map(b = > {…return {...} }

因为我们正在“映射”一个数组，所以我们需要总是在单个元素级别上查看代码。在这种情况下，b 是指数组中的每个单独的元素。我们在箭头右侧所做的一切都会影响这一元素。此外，因为 map 将对数组中的每个元素重复，所以对于 b 的整个集合将确保相同的结果。关键是无论我在返回语句中包含什么，都将是新数组中每个元素的结果。我们稍后将对此进行研究。

现在，让我们看看在 return 语句之前发生了什么。

const course = this . props . courses . find(c = > c . course _ id = = = b . course _ id)

嘿，又一个箭头功能。在这里，我所做的就是试图找到与我的桶(b)中的 id 相匹配的课程。记住，这一行代码在 map arrow 函数中，所以 b 的范围只是 buckets 中的一个单独的元素。对于我们遍历的每个 b，我们在 courses 数组中查找匹配的 course_id。至于 find 函数本身，c 引用 courses 数组中的每个元素。当 course_id 等于我们在外循环中使用的 bucket 元素中的 course_id 时，将返回匹配的元素(分配给常量“course”)。现在，在执行 return 语句之前，我们有一个与 bucket 元素(b)的 course_id 相匹配的 course 对象。

现在，最后一部分—返回语句。

return(…b，course_lat: course.lat，course_lng: course.lng)

无论我们在这里返回什么，都代表 newBuckets 数组中的每个元素条目。因为我们正在扩充一个对象(b)，所以我们再次需要使用 spread 操作符(…)来添加到现有的对象。我们已经在 find 函数中提取了匹配的课程对象。因此，我们现在可以提取与该课程相关的任何信息，并在我们的运营中使用它。在本例中，我们为每个桶对象(b)添加了路线 GPS 信息(纬度和经度)。

这就是了，将箭头函数串联起来执行一点点数据扩充。我希望这有助于你理解箭头的功能。