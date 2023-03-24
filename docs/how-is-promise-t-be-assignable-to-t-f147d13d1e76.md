# 如何在 TypeScript 中确保函数接受值而不是承诺

> 原文：<https://levelup.gitconnected.com/how-is-promise-t-be-assignable-to-t-f147d13d1e76>

今天我不得不在 TypeScript 中写一个函数`foo`，它将接受类型为`object`的单个参数。然后我有了另一个`async`函数`bar`，它将返回`object`。这些功能将会被其他工程师大量使用，并在我们的系统中扮演重要的角色——“核心功能”

```
function foo(arg: object) {
  // ...
}async function bar(): Promise<object> {
  // ...
}
```

这些函数的典型用法是将从`bar` 返回的**值**传递给`foo`

```
const value = bar()
foo(value) // no compile time error
```

你看问题对吗？我们没有等待`bar()`。这种设置的不好之处在于，尽管`value`是类型`Promise<object>`的，但它仍然可以赋值给类型`object`，因此编译器没有什么可抱怨的。但是我们有一个特例，只有承诺是不可接受的，其他的都是不可接受的，因为我们想确保其他工程师不会忘记在将`object`传递给`foo`之前解决它

![](img/fafb8f14a6cc05e355c415aa6609ad70.png)

那么我们如何确保`foo`传递的是一个值而不是一个承诺呢？天真的方法是创建一个类似于`arg`的包装器:

```
type FooArg = {
  value: object
}function foo({ value }: FooArg) {
  // ...
}async function bar(): Promise<FooArg> {
  // ...
}
```

有了这个设置，如果我们再次没有`await`并意外地将 promise 传递给`foo`，typescript 将捕获类型不兼容

```
const value = bar()
foo(value) // TS Error: Type 'Promise<FooArg>' has no properties in common with type 'FooArg'.
```

而用`await`就可以了

```
const value = await bar()
foo(value) // OK
```

# 更多地依赖 TypeScript

我们可以将我们的函数转换为*泛型*，使用*类型参数推断*并添加*条件类型*来将 Promise 从可接受的参数中排除。

```
function foo<T>(arg: T extends Promise<unknown> ? never : T) {
  // ...
}async function bar(): Promise<object> {
  // ...
}
```

在这里，我们通过在条件类型定义的真分支中提供`never`类型，告诉 TS 如果`T`可赋值给`Promise<unknown>`，这是不可能发生的，否则让`T`保持原样。

现在，当我们在没有明确提供类型参数`foo<SomeType>(...)` vs `foo(...)`的情况下调用`foo`时，编译器将从传入的参数类型中推断出`T`的类型。如果参数是一个承诺，那么`T`变成`never`，它不能被赋值，所以我们现在很好，我们的函数永远不会接受任何承诺。所有其他类型都是可以接受的。

> *没有*类型是`never`的子类型或可分配给`never`(除了`never`本身)

```
const value = bar()foo(value) // Argument of type 'Promise<object>' is not assignable to parameter of type 'never'.
```

在…期间

```
const value = await bar()foo(value) // All fine!
```

希望你觉得这有用！