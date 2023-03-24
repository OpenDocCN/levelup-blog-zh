# Java 中有哪些不同类型的变量，如何使用它们？

> 原文：<https://levelup.gitconnected.com/learn-the-different-types-of-variables-in-java-and-how-to-use-them-efficiently-5816aaa3588d>

![](img/63c10fe514bb8ada2604bc3153685146.png)

照片由 [Fotis Fotopoulos](https://unsplash.com/@ffstop?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

根据变量在程序中的声明位置，Java 中有四种不同类型的变量。我们会用例子来解释它们，以及它们的不同之处。

1.  **实例变量或实例字段:**这些是在没有**静态**关键字的类中声明的变量，但是在方法、构造函数或代码块**之外。它们可以在类中的任何位置声明。您可以使用或不使用**访问修饰符**来声明它们，比如(public、private、protected 或 default(无关键字))。**

```
public class MyClass {

  //instance field 1
  private String instanceField1;

  public MyClass(){} //Constructor

  //instance field 2
  public int anotherInstanceField2;

  public void setInstanceField(String parameterVariable) {...} //instance method

  //instance field 3
  boolean instanceField3;

  public static void main(String[] args) {
    System.out.println("field 1 value: " + instanceField1); // = null
    System.out.println("field 2 value: " + anotherInstanceField2); // = 0
    System.out.println("field 3 value: " + instanceField3); // = 0
  }
}
```

如果一个实例字段在声明时没有赋值，如果它是一个**原始类型**比如(int，boolean，long，float，..)或者 **null** 如果是**非原语类型**比如(String，Integer，AnyClass，..)

> 它们被称为**实例字段或变量**，因为它们属于从声明它们的类中创建的任何对象的实例。

```
public Main {

  public static void main(String[] args) {

    MyClass obj1 = new MyClass();
    MyClass obj2 = new MyClass();

    //Now we can access every 'public' field declared in the MyClass class
    // from the newly created object 'obj'

    obj1.anotherInstanceField2 = 11;
    obj2.anotherInstanceField2 = 33;

    System.out.println(obj1.anotherInstanceField2); // prints '11'
    System.out.println(obj2.anotherInstanceField2); // prints '33'
  }
}
```

因此，从上面的代码片段可以看出，每个实例字段对于其对象都是唯一的。 **obj1** 和 **obj2** 具有分配给其各自实例字段的唯一值。

2.**类字段或静态字段:**是用**静态**关键字声明的字段。它们在类内部声明，但在方法、构造函数或代码块外部声明。它们也可以在类内的任何位置声明，可以有也可以没有**访问修饰符**，比如(public、private、protected 或 default(无关键字))。

```
public class MyClass {

  //static field
  public static String staticField;

  public MyClass(){} //Constructor

}

class Main {

  public static void main(String[] args) {

    MyClass obj = new MyClass();

    obj.staticField //will throw Not defined Error

   //Now we cannot access the static field declared in MyClass class from the
   // newly created object 'obj' because static fields are not attached to any
   // object. They belong solely to the class they are declared and can only be 
   // accessed from their class.

    MyClass.staticField = "I am a static field";

    System.out.println(MyClass.staticField); // prints 'I am a static field'
  }
}
```

从上面的代码片段中可以看出，静态字段只能通过它们的类访问，而不能从任何对象访问。

3.**参数或自变量变量:**它们是在方法构造内部声明的变量，位于方法签名的左“ **(** )和右“ **)** ”括号之间。它们用于向方法传递值或对象。

```
public class MyClass {

  //instance field
  public String instanceField;

  public MyClass(){} //Constructor

  //instance method with a parameter variable
  public void setInstanceField(String parameterVariable) {
    instanceField = parameterVariable;
  }
}

class Main {

  public static void main(String[] args) {

    MyClass obj = new MyClass();

    obj.setInstanceField("From a paramater variable");

    System.out.println(obj.instanceField); // prints 'From a paramater variable'

  }
}
```

4.**局部变量:**是在方法或任何代码块中声明的变量，如在 **if** 语句块、 **for** 循环、 **while** 循环、 **switch** 语句块等等。

```
public Main {

  public static void main(String[] args) {

    MyClass obj1 = new MyClass(); // 'obj1' is local reference variable

    int id = 1; // 'name' is a local variable here.

    if (id > 1) {
      String tempName = "Austin"; // 'tempName' is a local reference variable
    }

  }
}
```

你会注意到在一些变量中使用了**‘reference’**，而‘id’局部变量没有被作为引用变量。

任何非原始变量都是一个**引用**变量。

例如， **obj1** 是一个类型为“ **MyClass** 的变量， **tempName** 是一个类型为“ **String** 的变量，这两种类型都是非原始类型，而 **id** 是一个类型为“ **int** 的变量，因此是一个原始数据类型，而不是引用变量。

感谢您花时间阅读这篇文章。我希望你能找到一些相关的东西。你可以放下你的评论和评论，我一定会注意的。

Cheers…🥂✨