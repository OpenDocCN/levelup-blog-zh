# React/React Native:优化 React 性能的 6 种方法+如何测试组件渲染速度

> 原文：<https://levelup.gitconnected.com/react-react-native-optimizing-react-component-performance-16a82efaa4af>

## 作者:杰夫·刘易斯

![](img/d14c40eb818fa74d0adada668859eefd.png)

**备注:**

*   本指南假设你知道**反应**和**反应本土。**

# 1.使用 useEffect()挂钩最小化渲染

在下面的例子中，`useEffect()`钩子有 3 个参数:`dropdownSetting`、`props.maricopaCountyDates`、`props.unitedStatesDates`。每当这些参数中的一个改变时，`useEffect()`钩子将运行，它将使用`setDates`修改状态。

这就是优化的切入点。当组件状态更改时，组件会重新呈现，但这些重新呈现的内容可以最小化。这个 **意味着更快的渲染，更少的计算，最少的 API 调用，等等。在下面的示例中，我们将了解为什么示例 A 没有优化，而示例 B 却优化了。**

## 示例 A: useEffect()钩子(状态改变 3 次= 3 次呈现)

*   **该组件渲染 3 次，因为每次**都会设置状态:一次为`dropdownSetting`，一次为 API 调用结束并传递数据时`props.maricopaCountyDates`，一次为 API 调用结束并传递数据时`props.unitedStatesDates`。
*   值得注意的是，在我的应用程序中，我有 **2 个 API 调用，在父组件中有一个空数组[]的默认状态。**父组件和子组件将同时挂载。然而，API 请求需要 300 到 500 毫秒。此时，子组件的`useEffect`用一个**空数组(默认状态是[])钩住`setsDates`，并将道具传递给子组件。**一旦 API 请求完成，**父组件将再次传递道具**，这是一个充满字符串的数组`[‘item1,’ ‘item2', ‘item3’, ‘item4', ‘item5']`。
*   由于`useEffect`在参数之一(`dropdownSetting`、`props.maricopaCountyDates`、`props.unitedStatesDates`)每次改变时运行，因此当属性从`[]`和`[‘item1,’ ‘item2', ‘item3’, ‘item4', ‘item5']`改变时，它将运行两次。

**父组件:**

```
**// React Hooks: State** const [ maricopaCountyDates, setMaricopaCountyDates ] = useState([]);
const [ unitedStatesDates, setUnitedStatesDates ] = useState([]);***// React Hooks: Lifecycle Methods*** useEffect(() => {
 ***// Get United States Data***const getUnitedStatesData = async () => {
    try {
 ***// Axios: API***const response = await axios.get('URL_HERE'); **// Set State**      setUnitedStatesDates(response.data);
    }
    catch (error) {
      console.log(error);
    }
  }; ***// Get United States Data***getUnitedStatesData();
}, []);
```

**子组件:**

```
***// React Hooks: Lifecycle Methods***useEffect(() => {
 ***// Dropdown Setting: Maricopa County***if (dropdownSetting === 'Maricopa County') {
 ***// Set Dates***setDates(props.maricopaCountyDates);
  }
 ***// Dropdown Setting: United States***else if (dropdownSetting === 'United States') {
 ***// Set Dates***setDates(props.unitedStatesDates);
  }
}, [dropdownSetting, props.maricopaCountyDates, props.unitedStatesDates]);
```

## 示例 B:优化的 useEffect()钩子(状态改变 1 次= 1 次渲染)

*   下面优化的`useEffect()`钩子执行得更好是由于当我们在 `**useEffect()**` **钩子子组件内`// Check If Data Exists` **的时候。****

```
***// React Hooks: Lifecycle Methods***useEffect(() => {
 ***// Check If Data Exists***if (props.maricopaCountyDates.length >= 1 && props.unitedStatesDates.length >= 1) {
 ***// Dropdown Setting: Maricopa County***if (dropdownSetting === 'Maricopa County') {
 ***// Set Dates***setDates(props.maricopaCountyDates);
    }
 ***// Dropdown Setting: United States***else if (dropdownSetting === 'United States') {
 ***// Set Dates***setDates(props.unitedStatesDates);
    }
  }
}, [dropdownSetting, props.maricopaCountyDates, props.unitedStatesDates]);
```

*   检查`props.maricopaCountyDates.length >= 1 && props.unitedStatesDates.length >= 1`是我们可以**管理和最小化我们运行** `**setDates**` **，**的次数的地方，它设置状态以降低组件重新渲染的性能。
*   由于`props.maricopaCountyDates`和`props.unitedStatesDates`的初始状态是空数组，**我们可以通过检查这两个数组的长度是否都大于等于 1**(`props.maricopaCountyDates.length >= 1 && props.unitedStatesDates.length >= 1`)来减少任何重新渲染。
*   **代码已优化为仅设置一次状态，这将导致 1 次渲染，而不是 3 次渲染。**如果你是道具钻取，这个数字甚至会更高，这会重新渲染嵌套组件。

# 2.useMemo()挂钩

React 允许开发人员使用一个`useMemo()`钩子，**在这里我们可以使用内存化的优化技术。**

## **记忆化:**

在代码中，**记忆化通过存储昂贵的函数调用的结果并在相同的输入再次出现时返回缓存的结果来加速计算机程序。**在记忆化中，当相同的精确参数随后被传入时，结果被记忆。如果我们有一个函数 compute 1 + 1，它将返回 2。但是如果我们使用 **useMemo()** 钩子，下一次我们通过函数运行 1 时，它不会把它们加起来，它只会记住答案是 2，而不会执行加法函数。

## useMemo 只应在某些情况下使用:

但是，`**useMemo()**` **只应在特定情况下使用。**即使我们用它来加 1 + 1 作为例子，**加 1 + 1 不是一个昂贵的函数，** `**useMemo()**` **不应该使用。**这只是为了演示，但在考虑`useMemo()`时，这里有一些事情需要考虑。

*   当考虑实现`useMemo()` hook 时，**确定它是否真的是一个昂贵的函数，它会消耗内存等资源。**
*   你正在**render**的函数中定义大量的变量，用`useMemo()`钩子来记忆可能是最佳的。
*   如果`useMemo()`钩子依赖数组为空，**这意味着不可能记忆**，它将在每次渲染时计算一个新值。那将违背`useMemo`的目的。

## 示例 A:标准值

```
**// Standard Value**
const standardValue = computeExpensiveValue(a, b);
```

## 示例 B:优化的 useMemo()值

```
**// Memoized Value**
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

# 3.useCallback()挂钩

与`useMemo()`类似，`useCallback()` **也采用记忆化，只是方式不同。**

`**useCallback()**` **不是保存从函数返回的记忆化值，而是返回回调的记忆化版本，该版本仅在其中一个依赖关系改变时才会改变。**

## **例子 A:回调函数**

```
**// Increment** const increment = (() => {
  setCount(count + 1);
});**// Decrement** const decrement = (() => {
  setCount(count - 1);
});
```

## 示例 B:优化的 useCallback()函数

```
**// Increment**
const increment = useCallback(() => {
  setCount(count + 1);
}, [count]);**// Decrement**
const decrement = useCallback(() => {
  setCount(count - 1);
}, [count])
```

# 4.格式化日期

如果您在应用程序中使用日期，很可能需要设置日期的格式。**现在大多数人会立即直奔**[**moment . js**](https://www.npmjs.com/package/moment)**而去，但那会给你的项目增加一个额外的库(App 大小)，而且很可能有一个比**[**moment . js**](https://www.npmjs.com/package/moment)**更快的原生解决方案。**

假设我有一个格式为`06/23/2020`的日期，并希望日期被格式化为`6/23`。在下面的例子中，我们将看看为什么例子 A 没有被优化而例子 B 被优化了。

另外，查看示例 C 和 D，了解如何用不同的格式(日期、纪元/Unix 时间戳)正确格式化日期。

## 示例 A:使用 Helper 函数格式化日期

*   我没有选择 [moment.js](https://www.npmjs.com/package/moment) ，而是首先选择创建一个助手函数(`formatDate`)。它将在每个找到的`/`处分割字符串，我将提取月和日。之后，我会查找是否有任何 0，如果需要的话删除它们，并返回一个新的字符串。
*   然而，**由于额外的代码，这可能会变得非常昂贵，但主要是由于** `**string.split**`。`string.split`的时间复杂度是`0(n)`，其中字符串越大，在字符串上迭代完成这个过程需要的时间就越多。
*   在我的应用程序中，我格式化了大约 500 个日期，这样额外的时间加起来就是运行额外的代码+迭代 500 个日期，长度为 10 ( `06/23/2020` ) = **最少 5000 次不必要的迭代**。

**formatDate.js**

```
***// Helper Function: Format Date***export const formatDate = (string) => {
 ***// Check If String Exists***if (string) {
 ***// Splitted String***let splittedString = string.split('/'); ***// Month + Day***let month = splittedString[0];
    let day = splittedString[1]; ***// Check For Zeros***if (month.charAt(0) === '0') {
      month = month.charAt(1);
    } ***// Check For Zeros***if (day.charAt(0) === '0') {
      day = day.charAt(1);
    } **// Return Date String (6/23)**    return `${month}/${day}`;
  }
  else {
    console.warn('removeZeros: String is not valid');
  }
};
```

## **示例 B:优化的日期格式**

*   这里我们用 [**原生新日期()**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) 与`dateOptions`来格式化`06/23/2020`到`6/23`。**代码已经过优化，可以格式化 500 个日期，使用更少的代码，0 个额外的库，减少 5000 次迭代(500 个日期 x 字符串长度(10))。**

```
***// Date Options***const dateOptions = {
  day: 'numeric',
  month: 'numeric',
  year: 'numeric', // Optional if you want the date 6/2/2020
  timeZone: 'UTC',
};***// Formatted Start Date (Format: 9/25 | 10/7)***const formattedStartDate = new Date(startDate).toLocaleDateString('en-US', dateOptions);
```

## **示例 C:优化的日期格式(附加日期类型)**

*   现在假设我们有一个格式为`2020–06–23T12:00:00.000Z`或 Epoch/Unix Time `1592913600`的日期？**可以不用**[**moment . js**](https://www.npmjs.com/package/moment)**来格式化这些日期。以下是不同的日期类型以及我们如何在没有**[**moment . js**](https://www.npmjs.com/package/moment)**的情况下格式化它们。**

## 2020 年 6 月 23 日 12 时 00 分至 2020 年 6 月 23 日:

```
**// Formatted Date** const date = new Date('06/23/2020').toLocaleDateString();console.log(date);  // 6/23/2020
```

## 1592913600(纪元/Unix 格式)到“2020 年 6 月 23 日”:

*   **注意:**日期应该是毫秒的格式，所以我们需要将秒乘以 1000。

```
**// Formatted Date** const date = new Date(1592913600 * 1000).toLocaleDateString();console.log(date);  // 6/23/2020
```

## 2020 年 6 月 23 日 12:00:00.000 至 2020 年 6 月 23 日星期四:

```
**// Formatted Date** const date = new Date('06/23/2020').toDateString();console.log(date);  // Tue Jun 23 2020
```

## 2020–06–23t 12:00:00.000 z 至 2020–06–23t 12:00:00.000 z:

```
**// Formatted Date** const date = new Date('06/23/2020').toISOString();console.log(date);  // 2020–06–23T12:00:00.000Z
```

# 5.let vs. const

*   `**let**` **:** 表示**变量** **可能被重新分配**的信号，如循环中的计数器，或算法中的值交换。
*   `**const**`:不会重新分配**标识符的信号**。

这只是一个小进步，但小进步就是小进步。它在代码质量和代码可读性方面也有所改进。

当使用时，您应该**只使用** `**const**` **来标识不会被重新分配的常量**，但是花费额外的时间有一些好处:

## JavaScript 引擎速度:

对于`const`，**代码明确告诉 JavaScript 引擎值不能改变**。因此，它可以自由地做任何它想要的优化，包括对使用它的代码发出一个文字而不是变量引用，知道这些值是不能改变的。

## **代码质量:**

使用`const`将导致 **VS 代码突出显示值被不恰当地重新分配的问题**，这可能是偶然的错误或错误。

## 代码可读性:

声明一个`const` **通知读者，当浏览代码时，它不会在以后被重新赋值为不同的值**。

# 6.碎片

[**React 片段**](https://reactjs.org/docs/fragments.html) 在 React 16.2 中被引入，让开发者可以选择使用，而不是 React 中的`<div></div>`和 React Native 中的`<View></View>`。

这也只是一个微小的改进，但微小的改进就是微小的改进。这主要对深度树有好处，但是渲染少一个 DOM 节点仍然是这个过程中少一个步骤。另一个好处是 DOM 检查器会稍微少一些混乱。

## **未优化渲染:**

```
<div>
  <p>Hello world</p>
</div>
```

## **例题 A(长语法):**

```
<React.Fragment>
  <p>Hello world</p>
</React.Fragment>
```

## **例子 B(短语法):**

```
<>
  <p>Hello world</p>
</>
```

# **测量渲染速度的性能测试**

下面是测试一个组件的格式，我们用`timeStart`和`timeEnd`来跟踪它。`**timeStart**` **应该从第一个** `**useEffect()**` **钩子**的顶端开始，`**timeEnd**` **应该在最后一个** `**useEffect()**` **钩子**的末端。

```
***// Performance Testing: Time Start***let timeStart = 0;***// React Hooks: Lifecycle Methods***useEffect(() => {
 ***// Performance Testing: Begin***timeStart = performance.now(); **// Do Something** }, []);***// React Hooks: Lifecycle Methods***useEffect(() => {
 **// Do Something** }, []);***// React Hooks: Lifecycle Methods***useEffect(() => {
 **// Do Something** ***// Performance Testing: Time End***const timeEnd: number = performance.now();
  console.log(`Render Speed: ${((timeEnd - timeStart) / 1000).toFixed(2)} seconds`);
}, []);
```

# **结论**

这些是优化 React/React 本机应用程序性能和减少代码的几种方法。

没有人是完美的。如果您发现了任何错误，想要提出改进建议，或者扩展某个主题，请随时给我发消息。我一定会包括任何改进或纠正任何问题。