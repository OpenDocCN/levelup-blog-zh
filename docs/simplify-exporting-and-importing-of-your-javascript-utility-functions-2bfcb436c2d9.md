# 简化 JavaScript 实用函数的导出和导入

> 原文：<https://levelup.gitconnected.com/simplify-exporting-and-importing-of-your-javascript-utility-functions-2bfcb436c2d9>

![](img/ff924876937d240a919b7a51aeb77b3c.png)

布雷特·乔丹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

作为一个“好”的开发者，你知道不要在你的项目中重复代码。为了做到这一点，我通常会创建可以在整个项目中轻松调用的实用函数。

本文解释了我如何构建我的实用函数，以便新函数可以立即使用，而不需要执行额外的导出/导入步骤。

# “旧”方法

假设我需要处理一些日期操作。为此，我将创建一个`src/utils/dates.js`文件:

```
exports.startOfDay = (dt) => {  
  dt = dt ? new Date(dt) : new Date()  
  return dt.setHours(0,0,0,0) 
}
```

使用这种模式，我放在这个文件中的每个函数都将被单独导出。现在我想从一个导入中获得所有的功能。我通过创建一个`src/utils/index.js`文件来做到这一点:

```
const { startOfDay } = require('./dates')module.exports = function useUtils() {  
  return { startOfDay } 
}
```

这种模式的问题是，每次在我的 dates 文件中添加或删除函数时，我都需要更新索引文件。

让我们看看一个更好的方法，也是我今天使用的方法。

# “新”方法

唯一需要更改的是我们的索引文件:

```
const dates = require('./dates')

module.exports = function useUtils() {  
  return { ...dates }
}
```

区别在于两个部分。首先，我们正在导入`dates`，而不是使用“对象析构”来指定每个函数。`dates`变量是一个包含日期文件中所有函数的对象。

其次，在导出中，我们创建了一个新函数，`useUtils()`，它使用一个扩展操作符返回`dates`中的每个函数。这才是真正的神奇！这确保了在`dates`对象中找到的每个函数都被单独导出。

# 使用

现在让我们看看如何使用我们的效用函数。

```
const useUtils = require('src/utils')const util = useUtils()const startOfDay = util.startOfDay('2022-3-1 09:09:00')
console.log(startOfDay) // (1646114400000)
```

唯一的进口是`useUtils`。为了简化使用，我们将其赋给变量`util`。现在我们可以使用`util`变量访问所有函数。比如我们可以叫`util.startOfDay()`。

现在，假设我们想在日期文件中添加另一个与日期相关的函数:

```
// Existing function
exports.startOfDay = (dt) => {  
  dt = dt ? new Date(dt) : new Date()  
  return dt.setHours(0,0,0,0) 
}// New function
exports.endOfDay = (dt) => {  
  dt = dt ? new Date(dt) : new Date()  
  return dt.setHours(23,59,59,999) 
}
```

我们的新功能`endOfDay()`立即可用。让我们使用它！

```
const useUtils = require('src/utils')const util = useUtils()const startOfDay = util.startOfDay('2022-3-1 09:09:00')
console.log(startOfDay) // (1646114400000)// New
const endOfDay = util.endOfDay('2022-3-1 09:09:00')
console.log(endOfDay) // 1646200799999
```

让我们把这个概念更进一步。在我的项目中，我需要一些实用函数来处理不同的字符串场景。为此，我创建了一个新的实用程序文件`src/utils/strings.js`:

```
exports.toCamelCase = (str) => *str.trim*()*.replace*(/[-_\s]+(.)?/g, (_, c) => (c ? *c.toUpperCase*() : ''))exports.toTitleCase = (str) => *str.replace*(/*^*(.)|\s+(.)/g, (c) => *c.toUpperCase*())
```

由于这是一个新文件，我需要将它包含在实用程序索引文件中:

```
const dates = require('./dates')
const strings = require('./strings')

module.exports = function useUtils() {  
  return { ...dates, ...strings }
}
```

当然，我只需要接触索引文件这一次。现在，我添加到字符串文件中的任何新函数都将自动包含在内。

```
const useUtils = require('src/utils')const util = useUtils()const startOfDay = util.startOfDay('2022-3-1 09:09:00')
console.log(startOfDay) // (1646114400000)const endOfDay = util.endOfDay('2022-3-1 09:09:00')
console.log(endOfDay) // 1646200799999const toCamel = util.toCamelCase('this_is_not_camel_case')
console.log(toCamel) // thisIsNotCamelCaseconst toTitle = util.toTitleCase('troy moreland')
console.log(toTitle) // Troy Moreland
```

上面的代码示例都在使用 CommonJS 语法(例如 Node.js)。这里有一种使用 ES6 语法(例如 Vue/React)实现相同功能的方法。

```
// src/utils/dates.jsexport const startOfDay = (dt) => {  
  dt = dt ? new Date(dt) : new Date()  
  return dt.setHours(0,0,0,0) 
}export const endOfDay = () => {...}// src/utils/index.jsimport * as dates from './dates'export default function useUtils() {
  return { ...dates }
}// src/some-other-file.jsimport useUtils from 'src/utils'const utils = useUtils()const startOfDay = util.startOfDay('2022-3-1 09:09:00')
console.log(startOfDay) // (1646114400000)const endOfDay = util.endOfDay('2022-3-1 09:09:00')
console.log(endOfDay) // 1646200799999const toCamel = util.toCamelCase('this_is_not_camel_case')
console.log(toCamel) // thisIsNotCamelCaseconst toTitle = util.toTitleCase('troy moreland')
console.log(toTitle) // Troy Moreland
```

# 结论

为了消除代码重复，创建一些实用函数。这也有利于跨项目的可重用性。

然后使用演示的方法来最小化管理函数库的开销。只需添加和使用！

让我知道这是不是一个有用的帖子！