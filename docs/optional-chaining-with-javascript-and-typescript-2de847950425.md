# JavaScript 和 TypeScript 的可选链接

> 原文：<https://levelup.gitconnected.com/optional-chaining-with-javascript-and-typescript-2de847950425>

![](img/8f1e244ef7a69534fc102572f79d1b73.png)

我们可能以前都遇到过以下问题——对象中的可选属性，如果它存在，就需要使用，如果不存在，就做其他事情或中断。

当对象嵌套时，这有时会变得令人沮丧，因为在尝试访问下一个嵌套属性之前，我们需要确保路径上的每个属性都存在。让我们看看如何更容易地确定一个可选属性是否存在。

# 可选链接

我们现在在链接中有了一个非常有用的操作符，`?`操作符允许我们对正在访问的属性是否存在进行内联检查。让我们看看这是如何工作的。

```
const foo = {
  bar: 'data'
}
console.log(foo?.bar) // 'data'
console.log(foo.a) // undefined
```

在上面这个简单的例子中，我们看到`foo`有一个属性`bar`，它只是一个字符串。如果我们尝试访问`bar`，它将显示数据。如果我们试图访问一个不存在的属性，它会显示`undefined`。

这被认为是预期的结果。

接下来让我们看一个更复杂的例子。假设我们有一个嵌套了几层的响应。在这种情况下，可选的链接将如何帮助我们呢？

让我们假设我们从一个 API 请求中得到一个响应，这个响应将包含一个`data`属性，可能包含也可能不包含一个`meta`属性，这取决于它是否附加在请求上。我们可以很容易地使用可选的链接来检查响应对象上是否存在`meta`。

```
const response = {
  data: {
    id: 123
  }
}console.log(response.meta.requestId) // Uncaught TypeError: Cannot read property of 'requestId' of undefinedconsole.log(response?.meta?.requestId) // undefined
```

这可以很容易地在条件中使用，以触发不同的流或执行一些附加功能。

```
if (response?.meta?.requestId) {
  return {
    ...response.data,
    requestId: response.meta.requestId // We know this exists
  }
}// Old method
if (response && response.meta && response.meta.requestId)
```

正如我们从上面的例子中看到的，我们可以使用更少的评估和代码完成完全相同的结果。我们不再需要担心那些烦人的`&`来确保在访问一个属性之前它是存在的。

# 这和函数一起工作吗？

嗯，是的。如果我们有一个接受可选回调参数的函数，我们可以使用可选链接来确保它在试图调用它之前存在。让我们看看这是怎么回事，好吗？

```
function foo(data, callback) {
  console.log(data)
  callback?.(data)
}const cb = () => console.log('called')foo('bar', cb)// 'bar'
// 'called'
```

从上面我们可以看到，如果定义了`callback`参数，它会将`data`属性传递给它并调用它。然而，如果`callback`是*而不是*定义的，那么它将不会被调用。

当重载函数签名以允许 JavaScript 中的可选回调时，了解这一点会很有用。值得注意的是，TypeScript 还允许我们在函数签名中使用`?`将参数定义为“可选的”

```
function foo(bar?: string) {} // 'bar' is optional here
```

![](img/21367f860e6bd1f034f062462ff6bd1f.png)

# 可选链接何时起作用？

每当我们在试图访问一个属性或函数之前需要确保它的存在时，可选链接就会起作用，并且可以防止在属性或函数不存在时抛出错误。

但是要小心，如果函数的要求是当属性不存在时抛出一个错误，那么我们将希望确保丢失的属性得到正确处理。

让我们快速看一下最后一个例子。我们已经看到了如何确保嵌套属性的存在，以及如何确保函数已经被定义，那么 JavaScript 的其他一些特性，比如`Map`呢？

```
const method = () => console.log('bar')
const data = new Map()
data.set('foo', method)data.get('foo')?.() // invokes the function IF present
```

在上面的例子中，我们可以看到，通过使用`?`操作符，我们可以在`foo`存在时随意调用函数。然而，如果`foo`不包含函数，这将抛出一个‘类型错误’。

注意:可选链接不验证数据的类型，只验证它是否存在。所有的类型验证仍然应该独立于这个步骤来处理。这只是允许我们以安全的方式提取或访问数据。

额外的好处——这也将与 TypeScript 的 nullish 合并一起工作。Nullish 合并是一个条件表达式，它确定一个值的存在，并在该值存在时返回它，或者退回到我们可以提供的预置。

[你可以在这里阅读更多关于 nullish 合并的信息](/using-the-ternary-operator-and-nullish-coalescing-b315237770fa)

```
const countryCode = person?.country?.countryCode ?? 'Unknown'
```

在上面的例子中，我们可以选择链接到 person 的“countryCode”属性。然后，我们使用 nullish 合并作为条件表达式来确定属性是否存在以及如何处理它。如果存在，则`countryCode`将被设置为`person.country.countryCode`，如果未设置，则在评估后`countryCode`将变为`unknown`。简单。

![](img/97fbb7e004a2455d2be276744a86424e.png)

# 那将是所有的人！

这里有一些参考资料，如果你想了解更多或者开始在你的代码库中实现可选的链接，这些资料可能会引起你的兴趣。

[中等无效合并](/using-the-ternary-operator-and-nullish-coalescing-b315237770fa)

[MDN 可选链接](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining)
[MDN 无效合并](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator)
[打字脚本可选链接](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html#optional-chaining)
[打字脚本无效合并](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html#nullish-coalescing)

感谢你的阅读，我希望你喜欢并学到了一些东西。如果你碰巧有任何反馈、批评或贡献，请随意写在下面的评论区。

再见韦德森。