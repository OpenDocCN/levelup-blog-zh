# 现在，您如何在现实世界的应用程序中使用 React Suspense 获取数据

> 原文：<https://levelup.gitconnected.com/how-you-can-use-react-suspense-for-data-fetching-in-real-world-applications-now-9fda8138f687>

![](img/da24a9c29ddbe2f86a5bc3f036a5b934.png)

由 [Ferenc Almasi](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

React 暂停数据获取仍然是 React 中的一个实验性特性。虽然不太可能，但规范可能会改变，您的代码可能会中断。

也就是说，这项技术非常有用。如果您的应用程序使用存储在数据库中的数据，与 REST api 通信，或者以任何其他方式异步加载数据，那么使用暂记获取数据可以从多方面改进您的代码:

*   它可以**清除样板文件**
*   它可以让你的代码**更易读**
*   它可以让你的代码**更具声明性**
*   最重要的是，它可以**让你的应用更快**

目前，只有几个库可以帮助你实现悬念。如果您的应用程序改变数据，监听实时数据，或者如果您想要实现任何更高级的悬念，您基本上只能自己解决。

在本文中，我将向您展示如何在您的应用程序中实现这些功能。为此，我们将使用传统的 React 编写一个简单的 To do 列表。然后，我们将分两步重构这个应用程序。首先通过引入误差边界，然后通过引入悬念。我们最终得到了一个速度更快、可读性更强、功能相同的应用程序。

**读完这篇文章，你就能够实现:**

*   带暂停提取数据
*   暂停变异数据
*   带着悬念听连续的数据流

# 经典(协作)待办事项列表

我们从一个使用传统 React 编写的超级简单的待办事项列表应用程序开始。在此应用程序中，您只能完成待办事项。其他人每隔几秒钟就会添加一个新的待办事项。

让我们列出构成这个简单应用程序的要素:

首先是我们的`<Todo />`组件:

```
function Todo({ todo, onComplete }) {
  const [isLoading, setLoading] = useState(false);
  const isMounted = useIsMounted();
  return (
    <li>
      <label>
        <button
          onClick={async () => {
            setLoading(true);
            await onComplete();
            if (isMounted()) {
              setLoading(false);
            }
          }}
          disabled={isLoading}
        >
          {isLoading ? <div className="spinner" /> : "Complete"}
        </button>{" "}
        {todo.label}
      </label>
    </li>
  );
}
```

注意`isMounted`的使用。因为`onComplete`是异步的，组件可能在`onComplete`完成之前被卸载。当组件被卸载时调用`setLoading`会导致内存泄漏。调用`isMounted`可以防止这种情况。

接下来，我们的`<Todos />`组件:

```
function Todos({ todos, completeTodo }) {
  return (
    <ul>
      {todos.map((todo) => (
        <Todo
          key={todo.id}
          todo={todo}
          onComplete={() => completeTodo(todo.id)}
        />
      ))}
      {todos.length === 0 && <li>You're all done!</li>}
    </ul>
  );
}
```

该组件列出所有 todo 项目，并在没有剩余项目时显示一条消息。

最后，我们的 todo 存储在键值存储中。这里的实现模拟了位于服务器上的数据库。因此，读取和更新数据是异步进行的。

```
*// Key-value database* class KeyValueStore {
 *// Read the value at some key* async read(key) *// Update the value at some key* async update(key, newValue) *// Register an observer for some key
  // onNext is called every time the value in "key" changes
  // Returns unregister function* onUpdate(key, onNext)
}
```

有了这些组件，我们就可以构建我们的应用程序了。请注意，这段代码相当复杂。之后我们一步一步地走过去。

```
function App() {
  const [isLoading, setLoading] = useState(true);
  const [error, setError] = useState();
  const [todos, setTodos] = useState();
  const isMounted = useIsMounted(); *// Fetch todos*
  useEffect(() => {
    setLoading(true);
    keyValueStore
      .read("todos")
      .then((todos) => {
        if (isMounted()) {
          setTodos(todos);
          setError(null);
          setLoading(false);
        }
      })
      .catch((error) => {
        if (isMounted()) {
          setError(error);
          setLoading(false);
        }
      });
  }, [isMounted]); *// Complete todo*
  const completeTodo = useCallback(async (todoId) => {
    try {
      await keyValueStore.update("todos", (todos) =>
        todos.filter((todo) => todo.id !== todoId)
      );
      setError(null);
    } catch (error) {
      setError(error);
    }
  }, []); *// Listen to incoming updates to todos*
  useEffect(() => keyValueStore.onUpdate("todos", setTodos), []); return (
    <>
      <h1>Todo list</h1>
      <p>Someone else may be adding todo's...</p>
      {isLoading ? (
        <div className="spinner" />
      ) : error ? (
        <ErrorFallback error={error} />
      ) : (
        <Todos todos={todos} completeTodo={completeTodo} />
      )}
    </>
  );
}
```

让我们仔细分析这段代码。发生了四件事:

## 1.正在提取待办事项:

```
useEffect(() => {
  setLoading(true);
  keyValueStore
    .read("todos")
    .then((todos) => {
      if (isMounted()) {
        setTodos(todos);
        setError(null);
        setLoading(false);
      }
    })
    .catch((error) => {
      if (isMounted()) {
        setError(error);
        setLoading(false);
      }
    });
}, [isMounted])
```

当应用程序挂载时，这个`useEffect`钩子触发。它从键值存储的“todos”键中读取数据。成功时，数据保存在组件的状态中。当错误发生时，该错误也存储在状态中。

## 2.完成待办事项

```
const completeTodo = useCallback(async (todoId) => {
  try {
    await keyValueStore.update("todos", (todos) =>
      todos.filter((todo) => todo.id !== todoId)
    );
    setError(null);
  } catch (error) {
    setError(error);
  }
}, []);
```

该函数更新键值存储中“todos”键的值。它通过从当前待办事项中过滤来完成待办事项。

请注意跟踪错误所需的样板文件。我们稍后将对此进行重构…

## 3.用键值存储保持组件状态最新

数据突变尚未反映在组件状态中。为了实现这一点，我们需要监听数据库更新:

```
useEffect(() => {
  const unsubscribe = keyValueStore.onUpdate("todos", setTodos);
  return () => {
    unsubscribe();
  }
}, []);
```

这个`useEffect`钩子就是这样做的。当组件挂载时，它设置一个到键值存储的监听器。每当“todos”键中的数据发生变化时，就会用新数据调用`setTodos`。当组件被卸载时，它会再次删除该侦听器。这种模式在 React 中很常见。即使不使用悬念，我们也可以将其重构为一行:

```
useEffect(() => keyValueStore.onUpdate("todos", setTodos), []);
```

## 4.呈现待办事项列表

```
isLoading ? (
  <div className="spinner" />
) : error ? (
  <ErrorFallback error={error} />
) : (
  <Todos todos={todos} completeTodo={completeTodo} />
)
```

最后，我们的组件的状态是最新的，我们可以渲染。对于这样一个简单的组件，相当多的代码。

在我们开始重构之前，让我们来看看 todo 应用程序的运行情况:

# 第一次重构:引入一个错误边界

重构我们的应用程序来直接使用悬念会变得混乱。我们需要首先处理一件事:错误处理。

目前，错误在多个地方被捕获。这降低了代码的可读性。而且，它对我们施加了一个限制:组件中抛出的错误只能在组件本身中处理。

为了绕过这个限制，我们可以将我们的 todo 列表包装在一个`<ErrorBoundary />`中。当一个组件抛出一个错误时，这个错误沿着组件树向上传播，直到某个组件使用[componentiddcatch](https://reactjs.org/docs/error-boundaries.html#introducing-error-boundaries)捕获到它。这种捕捉错误的组件称为错误边界。错误边界允许我们在错误发生时轻松控制应用程序的哪一部分退出。此外，我们可以使用它们来指定向用户显示什么样的回退。

你可以[自己写一个错误边界](https://reactjs.org/docs/error-boundaries.html)。然而，对于这个应用，我们使用来自 [react-error-boundary](https://github.com/bvaughn/react-error-boundary) 的流行实现。

我们可以在应用程序中使用错误边界，如下所示:

```
async function completeTodo(todoId) {
  await keyValueStore.update("todos", (todos) =>
    todos.filter((todo) => todo.id !== todoId)
  );
}function Todolist() {
  const [isLoading, setLoading] = useState(true);
  const [todos, setTodos] = useState();
  const isMounted = useIsMounted(); *// Fetch todos*
  useEffect(() => {
    setLoading(true);
    keyValueStore.read("todos").then((todos) => {
      if (isMounted()) {
        setTodos(todos);
        setLoading(false);
      }
    });
  }, [isMounted]); *// Listen to incoming updates to todos*
  useEffect(() => keyValueStore.onUpdate("todos", setTodos), []); return isLoading ? (
    <div className="spinner" />
  ) : (
    <Todos todos={todos} completeTodo={completeTodo} />
  );
}function App() {
  return (
    <>
      <h1>Todo list</h1>
      <p>Someone else may be adding todo's...</p>
      <ErrorBoundary FallbackComponent={ErrorFallback}>
        <Todolist />
      </ErrorBoundary>
    </>
  );
}
```

基本结构保持不变。在挂载时，我们仍然获取 todos，并且在挂载时，我们仍然监听来自键值存储的更新。然而，`completeTodo`已经干净了很多，因为它现在只需要担心更新键值存储。所以渲染，现在只需要关注加载状态。

通过将`<Todolist />`组件包装在`<ErrorBoundary />`中，所有在`<Todolist />`组件中抛出的错误都会被自动处理。

# 一些基本的悬念—获取数据一次

悬念与错误界限非常相似。就像错误边界一样，您可以在某个组件周围包裹一个悬念边界。错误边界和暂停边界有一个很大的区别。悬念边界不捕捉错误，它捕捉承诺。

要了解这一点，请考虑以下示例:

```
<Suspense fallback={<p>Searching for food...</p>}>
  <ErrorBoundary FallbackComponent={ErrorFallback}>
    <RandomFood />
  </ErrorBoundary>
</Suspense>
```

这里我们有以下 3 种状态:

1.  加载:`RandomFood = () => { return "Eggs"; }`在这种情况下，我们实际上会渲染`"Eggs"`
2.  错误:`RandomFood = () => { throw new Error("No food left"); }`在这种情况下，我们实际上会渲染`<ErrorFallback error={new Error("No food left")} />`
3.  待定:`RandomFood = () => { throw new Promise(() => {}) }`在这种情况下，我们实际上会渲染`<p>Searching for food...</p>`

这种三态模式在加载任何资源时都很常见。事实上，它是如此普遍，以至于它有一个名字。任何包含类似这种三态模式的`read`函数的对象都被称为`resource`。

让我们看看最基本的资源:

```
class BaseResource {
  data = undefined;
  status = "pending";
  error = undefined;
  promise = null; read() {
    switch (this.status) {
      case "pending":
        throw this.promise;
      case "error":
        throw this.error;
      default:
        return this.data;
    }
  }
}
```

`BaseResource`本身不做任何事情，但是我们可以把它作为基础来构建`AsyncResource`。`AsyncResource`接受任何承诺，并将其转换为符合资源规范的对象:

```
class AsyncResource extends BaseResource {
  constructor(promise) {
    super();
    this.promise = promise
      .then((data) => {
        this.status = "success";
        this.data = data;
      })
      .catch((error) => {
        this.status = "error";
        this.error = error;
      });
  }
}
```

仅仅使用这个简单的`AsyncResource`，我们现在可以编写一个简单的应用程序。这个应用程序从随机食物 api 中读取一个随机食物，并在准备好的时候显示结果:

```
const foodResource = new AsyncResource(
  fetch("/api/random_food").then((data) => data.json())
);function RandomFood() {
  const food = foodResource.read();
  return (
    <fieldset>
      <h2>{food.dish}</h2>
      <p>{food.description}</p>
    </fieldset>
  );
}function App() {
  return (
    <>
      <h1>Random food</h1>
      <Suspense fallback={<div className="spinner" />}>
        <ErrorBoundary FallbackComponent={ErrorFallback}>
          <RandomFood />
        </ErrorBoundary>
      </Suspense>
    </>
  );
}
```

请注意，这里没有使用任何逻辑语句，而是使用了声明性的优点。

另外，`foodResource`被定义在需要数据的组件之外。通过这样做，可以在呈现组件之前获取数据。这节省了一些宝贵的时间。

看看这个随机食物应用程序的运行情况:*(重新加载以查看不同的食物)*

如果您的应用程序只需要这种类型的数据获取，一定要查看库 [use-async-resource](https://github.com/andreiduca/use-async-resource) 。这个库允许这样做，甚至更多。

# 暂停数据更新和实时数据

有了基础知识，是时候将悬念引入我们的待办事项列表应用程序了。

我们可以实现`todosResource = new AsyncResource(() => keyValueStore.read("todos"))`。然后我们可以使用`todos = todosResource.read()`获得 todos 数据。然而，通过这种方式，我们只能接收到初始的 todos 数据。

为了处理数据突变和实时数据更新，我们需要另一种类型的资源。由于这种资源必须是“可观察的”，我们称之为`ObservableResource`。它这样延伸`BaseResource`:

```
class ObservableResource extends BaseResource {
  observers = []; constructor(promise) {
    super();
    this.promise = promise
      .then((data) => this.onNext(data))
      .catch((error) => this.onError(error));
  } onNext(data) {
    this.status = "success";
    this.data = data;
    this.observers.forEach(({ onNext }) =>
      typeof onNext === "function" && onNext(this.data)
    );
  } onError(error) {
    this.status = "error";
    this.error = error;
    this.observers.forEach(({ onError }) =>
      typeof onError === "function" && onError(this.error)
    );
  } observe(onNext, onError) {
    const observer = { onNext, onError };
    this.observers.push(observer);
    return () => {
      this.observers = this.observers.filter((other) =>
        other !== observer);
    };
  }
}
```

要消耗这样一种可观察的资源，我们需要做两件事:

1.  我们需要`read()`它。这样我们可以在必要的时候抛出错误和承诺。
2.  我们需要解决它。这样，我们就可以在数据发生变化时得到通知。

我们可以在监听更新的每个组件中显式地编写这两个操作。然而，这又会导致一些样板文件。这就是我们推出`useResource`挂钩的原因:

```
function useResource(resource) {
 *// Use state to force re-renders when data changes*
  const [, forceUpdate] = useState();
  *// On every change, forceUpdate is called*
  useLayoutEffect(() => resource.observe(forceUpdate), [resource]);
  *// Actually read the resource to get the correct data / error*
  return resource.read();
}
```

让我们的 todo 应用程序运行起来，我们需要最后一个要素:一个与我们的键值存储一起工作的具体的可观察资源。我们可以这样定义它:

```
class KeyValueStoreResource extends ObservableResource {
  constructor(key) {
    super(keyValueStore.read(key));
    this.key = key;
 *// subscribe to mutations*
    keyValueStore.onUpdate(key, (data) => this.onNext(data));
  } async update(value) {
    try {
      this.onNext(await keyValueStore.update(this.key, value));
    } catch (error) {
      this.onError(error);
    }
  }
}
```

总之，我们的类层次结构如下:

```
BaseResource               *base class*
├ AsyncResource            *use for simple promise,*
|                          *consume with* ***.read()***
└ ObservableResource       *use for mutable / real-time data,*
  |                        *consume with* ***useResource***
  └ KeyValueStoreResource  *concrete implementation for KeyValueStore*
```

好了，我们准备将这些元素组合成一个优雅的待办事项列表应用程序:

```
const todosResource = new KeyValueStoreResource("todos");const completeTodo = async (todoId) =>
  await todosResource.update((todos) =>
    todos.filter((todo) => todo.id !== todoId)
  );function TodosList() {
  const todos = useResource(todosResource);
  return <Todos todos={todos} completeTodo={completeTodo} />;
}function App() {
  return (
    <>
      <h1>Todo list</h1>
      <p>Someone else may be adding todo's...</p>
      <Suspense fallback={<div className="spinner" />}>
        <ErrorBoundary FallbackComponent={ErrorFallback}>
          <TodosList />
        </ErrorBoundary>
      </Suspense>
    </>
  );
}
```

同样，所有的逻辑都是抽象的。这使得发现正在发生的事情变得非常容易。我们得到了一个漂亮的、声明性的待办事项列表应用程序。

# 这就是现在如何在应用程序中使用 React Suspense 获取数据。

当然，还有更多的东西可以探究。有趣的下一步包括`[<SuspenseList />](https://reactjs.org/docs/concurrent-mode-patterns.html#suspenselist)`、`[useTransition](https://reactjs.org/docs/concurrent-mode-patterns.html#transitions)`和`[useDeferredValue](https://reactjs.org/docs/concurrent-mode-patterns.html#deferring-a-value)`。这些比`<Suspense />`更具实验性，因此更有可能改变。

这就是你的待办事项列表应用程序。我鼓励你[用它来玩](https://codesandbox.io/s/todo-list-with-suspense-v5xkx)，我很想听听你用悬念创造了什么。