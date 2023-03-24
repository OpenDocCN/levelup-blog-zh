# Java 中私有、公共和受保护的区别

> 原文：<https://levelup.gitconnected.com/differences-between-private-public-protected-in-java-a4b3756f7dae>

## 访问修饰符之间的差异

![](img/dabf6d6ae07b53e2833bd6b6623e59f7.png)

尼克·赖特在 [Unsplash](https://unsplash.com/s/photos/stop-sign?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

Private、Public 和 Protected 关键字是访问修饰符。根据您想要对变量或函数做什么，您可能想要将访问修饰符限制为 public、private 和 protected。

下面是每个访问修饰符的快速总结，以及可以从哪里访问它们。了解访问修饰符之间的区别并在适当的地方使用它们是很重要的。

## 公共

当方法、成员和类被声明为公共时，它们可以从任何地方被访问。它们可以通过类、包、子类(相同或不同的包)和世界来访问。

```
public class example{
     int add(int a, int b){
          return a+b;
     }
}
```

在上面的例子中，类示例是一个公共类。这意味着每个其他类都可以访问这个类并使用其中的方法和变量。没有什么可以阻止其他类与这个类进行交互。

公共访问修饰符的一个好处是它们很容易被访问。公共类的负面影响是，当我们不需要的时候，我们可能会在类外改变变量。

## 保护

受保护的访问修饰符允许类、包、子类(相同的包)、子类(不同的包)访问数据成员。public 和 protected 的区别在于，public 可以从外部类访问，而 protected 不能从外部类访问。

```
public class Addition{
    protected int addTwo(int a, int b){
        return a+b;
    }
}class Test extends Addition{
     public static void main(String args[]){
          Test obj = new Test();
     }
}
```

在上面的例子中，我们在加法类中创建了一个受保护的方法。后来，我们扩展了测试类以使用加法类。我们可以在没有编译错误的情况下运行它。受保护的方法将转移到公共类。

## 私人的

私有访问修饰符只允许类访问数据成员。类和接口不能声明为私有。

```
class outside{
     private int = 10;
     private int square(int a){ 
          return a*a;
  }
}public class inside{
     public static void main(String args[]){
        outside obj. = new outside();
    }
}
```

在上面的例子中，我们试图在内部类中创建一个新的外部对象。当我们运行这段代码时，我们会得到一个编译时错误，因为类内外的方法是私有的，这意味着其他类不能访问它。

编码时最好使用私有访问修饰符。这可以让你更好地控制你正在做的一切。

总之，我们有私有、公共和受保护的访问修饰符可以在 Java 中使用。当我们编码时，所有的访问修饰符服务于不同的需求。要记住的一个关键点是，如果可以的话，总是尽量使用 private，其次是受保护的最佳访问修饰符，然后是 public。了解不同的访问修饰符以及在编写代码时如何使用它们是很重要的。