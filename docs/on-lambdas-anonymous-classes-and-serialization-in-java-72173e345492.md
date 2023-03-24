# Java 中的 Lambdas、匿名类和序列化

> 原文：<https://levelup.gitconnected.com/on-lambdas-anonymous-classes-and-serialization-in-java-72173e345492>

## 或者，使用 lambdas 的另一个原因

![](img/bb02da7a4134be101c407880fd7106fe.png)

# Java 中的序列化

Java 中的序列化是一种机制，通过这种机制，对象可以在字节流之间进行封送，例如，允许它们在套接字中发送或存储在文件中。作为一个例子，考虑一个远程服务，它通过套接字发送一个要在别处执行的任务(使用特定代码或现有的框架，如 RMI):

```
public void execute(Runnable task) throws IOException {
  // sends the task somewhere to be executed
}
```

实现了`Runnable`和`java.io.Serializable`接口的任务对象可以用作这个远程服务的目标。它们将在方法`execute`中被序列化，字节被发送到别处并在那里被反序列化以重新创建要执行的任务。任务的远程执行类似于`service.execute(new SomeTask(...)`，其中`SomeTask`被定义为:

```
class SomeTask implements Runnable, Serializable {
  private final String name;

  public SomeTask(String *name*) {
    this.name = *name*;
  }

  public void run() { /* do something */ }
}
```

序列化的一个重要方面是，它不适用于单个实例，而是适用于对象的图。在不发送相应的`String`对象`name`的情况下，发送构成`SomeTask`实例的字节没有什么价值。因此，序列化过程遵循对象中的所有字段，并递归地序列化被引用的对象。

如果此图中的对象无法序列化，这将导致问题。如果进程遇到不可序列化的字段，它将在运行时失败。例如，`SomeTask`的这种变体将不起作用:

```
class SomeTask implements Runnable, Serializable {
  private final String name;
  private Timer timer = new Timer();

  public SomeTask(String *name*) {
    this.name = *name*;
  }

  public void run() { /* do something */ }
}
```

当将此类的实例提供给远程服务时，会出现错误:

```
Exception in thread "main" java.io.NotSerializableException: java.util.Timer
```

`Timer`类是不可序列化的，因为它包含一个负责执行调度给计时器的任务的运行线程，而线程是不可序列化的。`SomeTask`中任何不可序列化类型的字段都会产生类似的错误。

解决这个困难的一个方法是使用*瞬态*字段，序列化过程不*而*遵循这个字段。这个修改后的声明产生了可以序列化的任务:

```
transient private Timer timer = new Timer();
```

因为有了`transient`关键字，所以在序列化过程中会跳过`timer`字段。然而，实际上，在远程站点上通过反序列化重新创建的任务在其计时器字段中会有`null`。为了避免这种情况，可以定制反序列化过程，以便在从字节流创建任务对象时重新创建新的计时器:

```
private void readObject(ObjectInputStream *in*) throws IOException, ClassNotFoundException {
  *in*.defaultReadObject(); // recreate a task object from bytes
  timer = new Timer();    // replace null with a new timer
}
```

# 匿名类

对于足够简单和小的任务，通常情况下可以在匿名类中实现`Runnable`接口。人们可能会写道:

```
service.execute(new Runnable() {
  public void run() { /* do something */ }
});
```

这不起作用，因为匿名类的实例没有实现`Serializable`接口。由于匿名类不能在 Java 中实现多个接口，因此有必要定义一个中间接口，如下所示:

```
interface SerializableRunnable extends Runnable, Serializable {}
```

使用它，可以编译和执行以下代码:

```
service.execute(new SerializableRunnable() {
  public void run() { /* do something */ }
});
```

但是，即使匿名类不包含不可序列化的字段，它也很可能会因序列化异常而失败。原因是匿名类的实例包含了对定义匿名类的外部类实例的隐式引用(如果`Outer`是外部类的名称，则称为`Outer.this`)。序列化将遵循这个引用(它不能成为`transient`)，这在一般情况下是不可序列化的(即使是可序列化的，也可能会产生大量的字节流)。例如，上面的代码摘录可以在一个更大的`BigApp`类中定义:

```
class BigApp {
  // non serializable stuff, e.g., 
  private Timer mainTimer = ...; void someMethod(RemoteService *service*) throws IOException {
    *service*.execute(new SerializableRunnable() {
      public void run() { /* do something */ }
    });
  }
```

匿名类实例`execute`的参数包含一个隐式引用`BigApp.this`，该引用指向`BigApp`的封闭实例，包括一个计时器和许多其他不可序列化的组件。代码因序列化错误而失败:

```
Exception in thread "main" java.io.NotSerializableException: BigApp
```

目的是将一个小任务发送到远程服务，而不是将整个应用程序转换成字节流！

# 使用 lambdas

Java 8 引入了 *lambdas* ，在很多场景下已经取代了匿名类。Java 的 lambdas 没有与之关联的类型。相反，它们可以用作各种*函数类型*的目标值，这些函数类型是具有单一方法的接口，如`Runnable`。下面的调用可以编译:

```
service.execute(() -> /* do something */);
```

但是它在运行时会产生一个错误，因为 lambda 被赋予了`execute`方法的参数类型— `Runnable` —这是不可序列化的。

为了解决这个问题，必须重新定义`execute`以使用类型`SerializableRunnable`来代替:

```
public void execute(SerializableRunnable *task*) throws IOException {
  ...
}
```

因为`Serializable`接口是空的(它只是一个*标记*接口)，`SerializableRunnable`只包含一个方法`run`，因此可以是 lambda 的目标类型。调用`service.execute(() -> ...)`仍然是可能的，但是 lambda 现在被分配了类型`SerializableRunnable`而不是`Runnable`。以下代码在远程站点上工作并打印`hello`:

```
class BigApp implements Serializable {
  // non serializable stuff
  private Timer mainTimer = ...;

  void someMethod(RemoteService *service*) throws IOException {
    *service*.execute(() -> System.*out*.println("hello"));
  }
}
```

# 对外对象的隐式引用发生了什么变化？

此时，敏锐的读者可能会想:“等一下。由于对外部对象的隐式引用，它对匿名类不起作用。如果使用 lambda，它为什么会工作？”

事实证明，在 Java 中，lambdas 的实现不同于匿名类(使用一个字节码指令`INVOKEDYNAMIC`),并且可以在不隐式引用外部对象的情况下创建。

但是这种解释不会让我们精明的读者满意，他们现在在想:“匿名类维护对外部对象的引用是有原因的。如果一个 lambda 实际上使用了外部实例呢？”事实上，以下代码在运行时尝试序列化外部对象时会失败:

```
*service*.execute(() -> System.*out*.println(mainTimer));
```

这是因为`mainTimer`实际上是`BigApp.this.mainTimer`，并且需要`BigApp`实例的序列化。

简而言之，在 Java 中——撰写本文时是 Java 13——lambda 的实现比匿名类更智能。如果需要，它们只包含对外部对象的引用。如果一个 lambda 引用一个(非静态)字段(如`mainTimer`)或外部类的一个(非静态)方法(从而导致一个*闭包*，那么这个引用就变得必要并被生成。但除此之外，默认情况下并不包含它，这使得一些 lambdas 可以序列化，而匿名类则不能。与 lambdas 同时引入的*方法引用*也是如此。

```
*service*.execute(BigApp::*doSomething*)
```

如果`doSomething`是类`BigApp.`的一个静态方法，这就有效

# 避免`SerializableRunnable`

对于要用作可序列化对象的 lambdas，我们必须引入一个`SerializableRunnable`目标类型作为接口。这是不可取的，因为它改变了`execute`方法的签名，因此需要像`SomeTask`这样的类来显式实现`SerializableRunnable`接口。这可以通过使用*交集类型*来避免，要么在调用点类型转换中，要么在方法`execute`的参数化变体中。

类型转换的想法是使用类型转换强制 lambda 获得`Runnable` *和* `Serializable`类型:

```
public void execute(Runnable *task*) throws IOException { ... }service.execute((Serializable & Runnable) () -> ...);*service*.execute((Serializable & Runnable) BigApp::*doSomething*);
```

lambda 和方法引用在被类型转换为交集类型`Serializable & Runnable`后，都可以用作方法`execute`的目标。注意`execute`又回到了在它的签名中使用`Runnable`。

# 避免类型转换

最后，因为类型参数化(泛型)比类型转换更好，所以可以在方法`execute`的定义中使用交集类型，以允许直接使用 lambdas 和方法引用:

```
public <R extends Serializable & Runnable>
  void execute(R *task*) throws IOException { ... }service.execute(() -> ...);*service*.execute(BigApp::*doSomething*);
```

这种方法不需要任何类型转换(至少在 Java 11 和更高版本中是这样；它过去不能与早期版本的 Java 一起工作)。来自远程服务的方法`execute`由类型`R`参数化，类型`R`表示可序列化任务的类型，并且要求是`Serializable`和`Runnable`的子类型。代码依赖于`extends`关键字，在 Java 中用于类型上限。

# 结论

在 Java 中，lambdas 不是匿名类上的语法糖。它们独立于匿名类实现(比匿名类更智能)。特别是，只有当代码中的闭包需要这样做时，它们才维护对外部对象的引用。否则，它们会避免这种情况，因此可以在匿名类的实例不可序列化的情况下进行序列化。

本文主要关注序列化(作者首先意识到 lambda 和匿名类的行为不同)，但是 lambda 避免了对外部对象的不必要引用，这一事实也有利于垃圾收集，与序列化问题无关。另一个更喜欢 lambdas 而不是匿名类的原因。

Java 8 中引入的交集类型，在我们需要一个类型为`Serializable`的 lambda 以及一些函数类型(如`Runnable`)时，帮了我们大忙。交集类型不是 Java 的一个常用特性，但是当需要一个方法参数来实现多个不相关的超类型时，应该记住它们。