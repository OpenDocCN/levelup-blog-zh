# 单体设计模式和隐藏的东西

> 原文：<https://levelup.gitconnected.com/singleton-design-pattern-and-hidden-things-95ab2ba4cfa1>

![](img/05f17577a7a7418853e780d63f2bdaa4.png)

Stanislav Ivanitskiy 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

*本文将带你走过实现最常见的设计模式时的陷阱:Singleton*

# 履行

## 1.急切初始化

如果程序总是需要一个实例，或者如果创建实例的时间/资源成本不是太大，程序员可以切换到急切初始化，当类被加载到 JVM 中时**总是创建一个实例。**

**静态块初始化**

在这里使用静态块的主要优点是它支持单例类实例化的异常处理选项。

## **2。惰性初始化**

*当您的应用程序运行多线程时，您的代码可以随时在任何一行暂停。所以两个线程可能会运行在两个初始化块中。*

在多线程环境中，为了防止每个线程创建单例对象的另一个实例，从而产生并发问题，我们需要使用锁定机制。这可以通过`synchronized`关键字来实现。

每次调用`getInstance()`都会给我们带来额外的开销。**我们如何避免这种情况？**

## **双重检查锁定设计模式**

你知道上面的代码对 Java 不起作用。我不是在开玩笑，你们中的许多人(包括我)已经一遍又一遍地这样实现了。

原因如下，请仔细阅读下面的链接:

 [## “双重检查锁定被破坏”声明

### 署名:David Bacon(IBM Research)Joshua Bloch(Javasoft)，Jeff Bogda，Cliff Click (Hotspot JVM 项目)，Paul…

www.cs.umd.edu](http://www.cs.umd.edu/~pugh/java/memoryModel/DoubleCheckedLocking.html)  [## 双重检查锁定

### 在软件工程中，双重检查锁定(也称为“双重检查锁定优化”)是一种软件…

en.wikipedia.org](https://en.wikipedia.org/wiki/Double-checked_locking) 

如何解决这个问题:简单地使用`**volatile**`关键字。下面是双重检查锁定模式的真实版本:

注意局部变量`result`似乎是不必要的。这样做的效果是，在`sInstance`已经被初始化的情况下(即大多数时候)，volatile 字段只被访问一次(由于`return result;`而不是`return sInstance;`)，这可以将方法的整体性能提高 25%。

## **按需初始化持有者习语**

Java 确保静态类加载器的线程安全属性。因此，当一个线程使用类`Helper`初始化一个对象时，没有其他线程可以来创建另一个对象。

上述方法也确保了惰性初始化属性。静态实例字段的初始化不会早于`SingletonHolder`类的加载和初始化。`SingletonHolder`类的加载和初始化不会早于它第一次被引用。最后，在调用`getSingletonInstance()`方法之前，`SingletonHolder`类首先被引用。这正是我们所需要的。

**枚举**

> 这种方法在功能上等同于公共字段方法，只是它更简洁，免费提供序列化机制，并提供防止多重实例化的可靠保证，即使面对复杂的序列化或反射攻击。虽然这种方法尚未被广泛采用，但是单元素枚举类型是实现 singleton 的最佳方式。

# 序列化问题

序列化允许将对象存储在某个数据存储中，并在以后重新创建它。然而，当我们序列化一个单例类并多次调用反序列化时，我们最终会得到单例类的多个对象。

为了让序列化和 singleton 正常工作，我们必须在 singleton 类中引入`readResolve()`方法。方法让开发人员控制在反序列化时应该返回什么对象。

# 克隆问题

尽管我们已经采取了足够的预防措施来使单例对象作为单例对象工作，但是克隆对象仍然会复制它并导致一个重复的对象。可以使用对象的`clone()`方法构建单例对象的克隆。因此，建议重载 Object 类的`clone()`方法并抛出`CloneNotSupportedException`异常。

# 灵感

*   受 quang 光·thảo 和他关于媒体上的单例设计模式的文章的启发。你可以在这里查看[。](https://blog.androidcafe.in/singleton-design-pattern-2c63dfcfccf2)
*   也参考[单例设计模式](https://javarevealed.wordpress.com/tag/singleton-and-serialization/)。

开心编码~