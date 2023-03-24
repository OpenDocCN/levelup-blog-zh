# 浅层拷贝和深层拷贝的区别

> 原文：<https://levelup.gitconnected.com/difference-between-shallow-and-deep-copy-c0a968e89c44>

## 面向对象编程指南

## 这种差异非常微妙，但却有着巨大的影响

![](img/c4288c01e7d488e00d14db528d036c67.png)

freestocks.org 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[的照片](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral)

在我以前的一些职位中，特别是在申请后端角色时，我经常被问及对象克隆。解释深层和浅层拷贝之间的区别常常是讨论的话题之一。我将解释两者的区别，以及何时应该使用一个而不是另一个，并提供 Java 示例代码。

在 Java 中，对于一个可克隆的类，它必须实现`**Cloneable**`接口，该接口只有一个返回对象的方法名克隆。因此，在克隆对象时，必须将其显式转换回适当的类型。让我们开始吧。

# 浅拷贝

> 浅副本是通过复制所有基本类型值和所有相关引用对象类型的副本引用，从现有对象创建的对象。

这仅仅意味着，如果你的对象拥有所有的原始数据类型，那么所有的值都将被复制。然而，如果它有引用类型数据，那么你的新对象将指向与原始对象相同的引用。这意味着对原始对象的更改将反映在复制的对象中。

假设您有一个 ***Car*** 类，其属性包括年份、品牌、型号和装饰。它们都是基本类型，所以您的 ***汽车*** 的克隆将复制所有赋值。但是，如果您的 Car 类有一个 engine 类型的 Engine 属性(这是一个类),那么只会复制对原始对象位置的引用。因此它被称为浅拷贝。对复制的引擎类属性所做的更改也会反映在原始属性中。让我们来看一个代码示例。

为了简洁起见，我们将只向 Engine 类添加两个属性。这是我们的引擎类代码片段。

```
public class Engine implements Cloneable {
    //SN stand for Serial Number
    String radiatorSN;
    String oilPumpSN;

    public Engine(String radiatorSN, String oilPumpSN){
        this.radiatorSN = radiatorSN;
        this.oilPumpSN = oilPumpSN;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException
    {
        return super.clone();
    }
}
```

这是我们的汽车代码片段。

```
public class Car implements Cloneable {
    int year;
    String make;
    String model;
    String trim;
    Engine engine; public Car(int year, String make, String model, String trim, 
           Engine engine){
        this.year = year;
        this.make = make;
        this.model = model;
        this.trim = trim;
        this.engine = engine;
    } @Override
    protected Object clone() throws CloneNotSupportedException
    {
        return super.clone();
    } @Override
    public String toString() {
        return "Year: " + year +
                "\nMake: " + make +
                "\nModel: " + model +
                "\nTrim: " + trim +
                "\nEngine.RadiatorSN: " + engine.radiatorSN +
                "\nEngine.OilPumpSN: " + engine.oilPumpSN;
    }
}
```

我已经覆盖了 Car 类的`toString`方法来打印所有属性。让我们使用我们的 car 类，展示一个浅拷贝的行为。下面是 main 方法的代码片段。

```
public static void main(String[] args) {

        Engine engine = new Engine("abc123","zyx987");
        Car originalCar = new Car(2019,"BMW","328", "i", engine);
        Car copiedCar = null;

        try{

            System.*out*.println("\n----printing originalCar ----");
            System.*out*.println(originalCar);

            copiedCar = (Car)originalCar.clone();
            copiedCar.engine.radiatorSN = "abc222";

            System.*out*.println("\n----printing copiedCar ----");
            System.*out*.println(copiedCar);

            System.*out*.println("\n----printing originalCar ----");
            System.*out*.println(originalCar);
        }
        catch (CloneNotSupportedException ex){
            System.*out*.println(ex.getMessage());
        }
}
```

运行 main 方法后的输出如下所示

```
----printing originalCar ----
Year: 2019
Make: BMW
Model: 328
Trim: i
Engine.RadiatorSN: abc123
Engine.OilPumpSN: zyx987----printing copiedCar ----
Year: 2019
Make: BMW
Model: 328
Trim: i
Engine.RadiatorSN: abc222
Engine.OilPumpSN: zyx987----printing originalCar ----
Year: 2019
Make: BMW
Model: 328
Trim: i
Engine.RadiatorSN: abc222
Engine.OilPumpSN: zyx987
```

请注意我们原始对象的 ***oilPumpSN*** 属性的值也被更改为“abc222 ”,尽管我们只是在复制的对象中更改了它。对于浅层复制，您总是可以预料到这样的行为，因为如前所述，两个对象都指向同一个引擎对象。

*在处理只包含只有原始数据类型的属性的类时，建议使用浅层拷贝。如果您的对象包含非原始数据类型，您将会遇到这种行为，并且如果两个对象都被使用，它可能会与您的程序流发生偏差。*

另一件要注意的事情是，默认情况下，调用`**super.clone()**`会产生现有对象的浅层副本。

# 深层拷贝

> 深层副本是通过复制所有原始和非原始数据类型值从现有对象创建的对象。

浅层拷贝的定义与深层拷贝非常相似，但有一点不同。对于非基本类型属性，实际值被复制，而不是仅仅指向它们的对象的原始引用。所有属性的复制都独立于原始对象。这意味着，与浅层复制不同，对复制对象的更改不会影响原始对象。这也是一个成本更高的操作，应该只相应地使用，这就引出了我的下一个观点。当您想要复制的对象不仅仅包含原始数据类型时，您应该使用它。

在前面的例子中，我们注意到我们得到了一个不希望的行为。你可能会问自己哪些不受欢迎的行为？嗯，我们只想为复制的对象设置引擎 ***油泵*** 值，但是，因为它们都指向同一个对象，所以我们不能这样做。对复制对象中引擎属性的任何更改也会反映在原始对象中。这就是深层拷贝的必要性。

让我们修改上一个例子中使用的代码，当我们在 Car 类上调用 clone 方法时，进行深度复制而不是浅层复制。一切保持不变。我们唯一需要修改的地方是受保护的克隆方法。

这是它以前的样子。

```
@Override
protected Object clone() throws CloneNotSupportedException
{
    return super.clone();
}
```

这是我们做了深入复制的更改后的样子。

```
@Override
protected Object clone() throws CloneNotSupportedException
{
    Car clonedCar = (Car)super.clone();
    clonedCar.engine = (Engine)this.engine.clone();
    return clonedCar;
}
```

这里的不同之处在于，我将引擎值显式地赋给了新克隆的对象。因此，复制的对象引擎属性将不会指向原始对象，而是指向其引擎对象。所以如果我们现在运行 main 方法，输出应该是这样的。

```
----printing originalCar ----
Year: 2019
Make: BMW
Model: 328
Trim: i
**Engine.RadiatorSN: abc123**
Engine.OilPumpSN: zyx987----printing copiedCar ----
Year: 2019
Make: BMW
Model: 328
Trim: i
**Engine.RadiatorSN: abc222**
Engine.OilPumpSN: zyx987----printing originalCar ----
Year: 2019
Make: BMW
Model: 328
Trim: i
**Engine.RadiatorSN: abc123**
Engine.OilPumpSN: zyx987
```

一旦我们的代码被更改为深层副本而不是浅层副本，我们就获得了想要的行为。因此，当您的类由原始和非原始数据类型组成时，应该使用深度复制。

谢谢你坚持到最后。 ***快乐编码*** 。