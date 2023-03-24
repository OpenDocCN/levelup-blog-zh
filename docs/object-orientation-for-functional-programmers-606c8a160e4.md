# 面向函数式程序员的对象

> 原文：<https://levelup.gitconnected.com/object-orientation-for-functional-programmers-606c8a160e4>

![](img/b264b4ad5f8b8d4988e5504748669a2d.png)

亲爱的函数式程序员朋友们，看来面向对象是由停留决定的，所以是时候了解一下它了。在本文中，我们将这个函数式程序转变为面向对象的语言，保留了不变性、模式匹配、可组合性和类型安全。这些例子是用 OCaml 和 Java 编写的，但是应该可以无缝地翻译成任何函数式和面向对象的语言。

```
type 'a list =
  | Nil
  | Cons of 'a * 'a list(* fold_right : ('a -> 'b -> 'b) -> 'b -> 'a list -> 'b *)
let rec fold_right step base xs =
  match xs with
    | Nil -> base
    | Cons (x, xs') -> step x (fold_right step base xs')(* map : ('a -> 'b) -> 'a list -> 'b list *)
let map f xs = 
  fold_right (fun x xs' -> Cons (f x, xs')) Nil xs
```

因为我们正在构建一个“列表库”，所以我们将所有内容放在一个 Java 类中。

```
public class List { }
```

为了清晰起见，我还保留了 OCaml 命名风格，尽管它在 Java 中看起来很可怕。让我们依次看每一部分。

![](img/2833c6f1d4289ac8b8a4d999c3c6f931.png)

# 不可变数据类型

我们首先需要移动的是构造函数`Nil`和`Cons`。我们可以使用具有公共接口的类来实现这些。在这种情况下，我们选择不通过使它们成为静态内部类来公开它们，但这并不重要。

OCaml 类型是多态的，所以我们的 Java 接口(和类)需要是通用的。`Nil`没有参数，所以 Java 类没有字段，`Cons`有两个，所以 Java 类有两个字段。

最后，因为数据类型在 OCaml 中是不可变的，所以我们也需要继承这个属性。在 Java 中，我们可以通过使用`final`关键字来使字段不可变。

我们最终会得到:

```
public class List {
  private interface list<A> { }
  private static class Nil<A> implements list<A> { }
  private static class Cons<A> implements list<A> {
    public final A element;
    public final list<A> rest;
    public Cons(A element, list<A> rest) {
      this.element = element;
      this.rest = rest;
    }
  }
}
```

![](img/05212edb2d1dbe191a98fef48b3135ed.png)

# 模式匹配

是时候处理复杂的`fold_right`功能，从而进行模式匹配了。面向对象的语言有一个复杂的变通方法，以*设计模式*的形式来弥补语言缺乏模式匹配的缺陷；具体来说，访问者模式。我们用另一个接口实现了这一点，并向构造函数添加了一个`visit`方法。

```
public class List {
  private interface listVisitor<A, B> {
    B visit(Nil<A> xs);
    B visit(Cons<A> xs);
  }
  private interface list<A> {
    <B> B visit(listVisitor<A, B> visitor);
  }
  private static class Nil<A> implements list<A> {
    public <B> B visit(listVisitor<A, B> visitor) {
      return visitor.visit(this);
    }
  }
  private static class Cons<A> implements list<A> {
    // ...
    public <B> B visit(listVisitor<A, B> visitor) {
      return visitor.visit(this);
    }
  }
}
```

忽略高阶函数`step`直到下一节，使用 Visitor 模式，直接实现`fold_right`方法。它比 OCaml 略显繁琐，但不难看出相同的结构:

```
<A, B> B fold_right(??? step, B base, list<A> xs) {
  return xs.visit(new listVisitor<A, B>() {
    public B visit(Nil<A> xs) {
      return base;
    }
    public B visit(Cons<A> xs) {
      return step.apply(xs.element, xs.rest.visit(this));
    }
  });
}
```

![](img/26a44b97843e3bb655853ff81a1e05df.png)

# 高阶函数

谜题的最后一部分是高阶函数。它需要是我们可以作为参数的东西，我们只有一样东西符合这个标准:对象。所以对于每个函数签名，我们简单地为它创建一个类。这个符合`fold_right`的目的:

```
private interface FoldFunc<A, B> {
  B apply(A elem, B rest);
}
```

这意味着我们也可以实现`map`方法:

```
private interface MapFunc<A, B> {
  B apply(A elem);
}
<A, B> list<B> map(MapFunc<A, B> f, list<A> xs) {
  return fold_right(new FoldFunc<A, list<B>>() {
    public list<B> apply(A elem, list<B> rest) {
      return new Cons<B>(f.apply(elem), rest);
    }
  }, new Nil<B>(), xs);
}
```

# 结论

尽管在 Java 中有点笨拙和冗长，但进行函数式编程是可能的。使用 lambdas 和 diamond 操作符确实让它看起来漂亮了一点，但是它们没有增加任何表现力。

在我的书中，我讨论了如何从已经面向对象的代码转向通过重构从函数式编程中获得的许多相同的好处:

[![](img/ac2e56b71c80f26c090280c2ea7d0b06.png)](https://www.manning.com/books/five-lines-of-code)