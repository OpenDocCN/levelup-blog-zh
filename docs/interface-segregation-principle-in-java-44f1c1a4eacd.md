# Java 中的接口分离原则

> 原文：<https://levelup.gitconnected.com/interface-segregation-principle-in-java-44f1c1a4eacd>

## 权威指南

## 开发 java 时理解 SOLID 原理非常重要，因为…

![](img/70c4dd87c461bf8fab45a81fefce1104.png)

由 [Merlene Goulet](https://unsplash.com/@merlenegoulet?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 界面分离原理

接口在 Java 编程语言中起着重要的作用，它们被广泛用于抽象和支持多重继承。

接口是通过使用关键字**和接口**在其中声明方法来实现的。类可以用 **implements** 关键字实现接口，然后为声明的方法提供实现。编写接口时要记住的设计原则之一是接口分离原则。

在接口分离原则中，接口不应该有实现类不需要的方法。实现类毫无理由地被迫为那些它不需要的方法提供实现。向接口添加方法或更改方法签名需要更改该接口中实现的所有类。

接口分离原则建议将一个接口分离成更小的和[高度内聚的](https://en.wikipedia.org/wiki/Cohesion_(computer_science))接口，称为“角色接口”,每个“角色接口”为特定的行为声明一个或多个方法。因此，客户端不是实现接口，而是只实现那些方法相关的“角色接口”。

# 坏榜样

![](img/eb0f7f78fa421a044249c4a52e052d61.png)

[毛里西奥·里维奥](https://unsplash.com/@mlivio800?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

考虑构建不同类型运输工具的应用程序的需求。每辆车都有价格和颜色。车辆如**汽车**可以启动和停止移动，而有些车辆如**飞机**既可以移动又可以飞行。示例界面

## Vehicle.java

```
public interface Vehicle {
    void setPrice(double price);
    void setColor(String color);
    void start();
    void stop();
    void fly();
}
```

表示飞机的类可以实现车辆接口，并提供所有接口方法的实现。但是，想象一个代表汽车的类。这就是汽车类的外观。

## Car.java

```
public class Car implements Vehicle {
    double price;
    String color;
    @Override
    public void setPrice(double price) {
        this.price = price;
    }
    @Override
    public void setColor(String color) {
        this.color=color;
    }
    @Override
    public void start(){}
    @Override
    public void stop(){}
    @Override
    public void fly(){}    
}
```

正如你在代码中看到的，Car 需要提供一个实现 **fly** ()的方法，尽管它并不需要它们。这违反了接口隔离原则。这些可能会影响代码的可读性

# 好榜样

![](img/4840dc93d90905d32cc058b28feadf4b.png)

保罗·基亚布兰多在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

遵循接口分离原则，我们可以解决车辆接口的问题，解决方案是- *将车辆接口分离成多个角色接口，每个接口用于特定的行为*。在这种情况下，车辆接口可以分为三个接口:玩具，移动，和飞行。

## Vehicle.java

```
public interface Vehicle {
     void setPrice(double price);
     void setColor(String color);
}
```

## Movable.java

```
public interface Movable {
    void start();
    void stop();
}
```

## Flyable.java

```
public interface Flyable {
    void fly();
}
```

现在 Vehicle 与 **setPrice** ()和 **setColor** ()方法接口，因为所有的 Vehicle 都有价格和颜色，所以所有的 Vehicle 实现类都可以实现这个接口。然后，可移动和可飞行的界面来表示车辆中的移动和飞行行为。

# 履行

## Car.java

```
public class Car implements Vehicle, Movable {
    double price;
    String color;
​
    @Override
    public void setPrice(double price) {
​
        this.price = price;
    }
    @Override
    public void setColor(String color) {
​
        this.color=color;
    }
    @Override
    public void start(){
        // Implementation
    }
    @Override
    public void stop(){
        // Implementation
    }
}
```

## Aeroplane.java

```
public class Aeroplane implements Vehicle, Movable, Flyable {
    double price;
    String color;
​
    @Override
    public void setPrice(double price) {
​
        this.price = price;
    }
​
    @Override
    public void setColor(String color) {
     this.color=color;
    }
    @Override
    public void start(){
        // Implementation
    }
    @Override
    public void stop(){
        // Implementation
    }
    @Override
    public void fly(){
        // Implementation
    }
}
```

现在实现类实现了他们感兴趣的接口，这有助于删除不必要的代码，可读性更好

接下来，让我们编写一个类来创建实现类的对象。

## VehicleBuilder.java

```
public class VehicleBuilder {
    public static Car buildCar(){
        ToyHouse toyHouse=new ToyHouse();
        Car car = new Car();
        car.setPrice(15.00);
        car.setColor("green");
        car.start();
        return car;
        }
    public static Aeroplane buildAeroPlane(){
        Aeroplane aeroplane = new Aeroplane();
        aeroplane.setPrice(25.00);
        aeroplane.setColor("red");
        aeroplane.start();
        aeroplane.fly();
        return aeroplane;
    }
}
```

# 总结(界面分离原则)

接口分离原则确保了小的、集中的和高度内聚的软件组件。界面分离原理易于理解和简单遵循。但是，识别不同的接口对于获得正确的角色分离来说是一个挑战。接口分离原则是开发 Java 应用程序时需要掌握的一个非常强大的概念。

如果你喜欢这个概念并理解了这个故事，你可能会喜欢 [**命令模式**](https://medium.com/dev-genius/the-command-pattern-in-java-8a545a56d68a) ，这是我过去写的另一个行为设计模式，是 GoF 正式设计模式列表的一部分。该模式旨在将执行给定动作(命令)所需的所有数据封装在一个对象中