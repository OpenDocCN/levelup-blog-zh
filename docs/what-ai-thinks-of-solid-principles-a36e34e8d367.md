# 艾对坚实原则的看法

> 原文：<https://levelup.gitconnected.com/what-ai-thinks-of-solid-principles-a36e34e8d367>

## 询问 ChatGPT 关于成熟的编程概念

![](img/85faa08cf89e8f8cdc430c4f4575cfdd.png)

照片由 [DeepMind](https://unsplash.com/@deepmind?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 介绍

我的[与 chat GPT](https://medium.com/@jeffreybakker/what-ai-thinks-of-cyclists-5c647e47b502)(open ai 的最新趋势人工智能)的第一次相遇是一次非常不同的经历；它聚焦于一个有争议的话题，即社会如何看待某种交通方式。

这一次，我想看看人工智能“理解”编程最佳实践的程度。我们已经看到了提供工作代码片段是多么聪明的例子，但是它真的了解如何编写好的、持久的代码吗？

我将下面的聊天分成了 3 个部分:

*   询问关于在面向对象编程中使用可靠原则的有效性的一般问题。这是为了测试人工智能对这个话题的了解程度。人工智能显然有能力谈论这个话题——也许和一个有经验的开发者一样，尽管我不完全同意它的一些答案。
*   要求为 5 项原则中的每一项提供代码示例。人工智能足够聪明，不仅可以编写代码示例，还可以解释概念。在某些情况下，使用多个段落和片段。对同一原则的多次询问不仅会导致不同的代码片段，还会导致应用该原则主要目的的概念上不同的方式(本文中未显示)。
*   要求提供一个 IoC(控制反转)容器的代码示例，这通常伴随着 DIP(依赖反转原则)。第一个答案很简洁，使用了 boost 库。在又问了两次之后，它从头开始滚动自己的 IoC 容器(尽管是非常基础的一个)，每次都使用完全不同的代码(本文中没有显示)。

目的是发现人工智能是否能够很好地了解高级编程主题，以便将它们教给人类程序员。阅读 ChatGPT 如何回答我的问题，自己判断。

对于一个 TL 来说；外卖博士，我的结论在文章底部。各节由下列分隔符分隔。

> 坚实的原则能让程序员更有效地编写单元测试吗？*

****注*** *:我相信确实如此，不过还是看看 AI 怎么说*

SOLID 是面向对象软件设计的一组原则，旨在使软件设计更易于理解、灵活和维护。这些原则并不特别与单元测试相关，但是它们有助于编写有效的单元测试。

其中一个原则，单一责任原则，规定一个类应该只有一个改变的理由。这意味着一个类应该有一组严格定义的职责，并且不应该负责多个不相关的任务。这使得为一个类编写单元测试变得更加容易，因为测试的范围更加集中，并且这个类需要测试的内容更少。

另一个原则，依赖倒置原则，声明高级模块不应该依赖于低级模块，而是两者都应该依赖于抽象。这也使得编写单元测试变得更加容易，因为可以根据抽象而不是具体的实现细节来编写测试。

总的来说，遵循坚实的原则有助于创建更加模块化和易于测试的软件设计，这使得编写有效的单元测试更加容易。然而，SOLID 并不能代替良好的测试实践，遵循编写和维护单元测试的最佳实践也很重要。

> 你能举例说明使用可靠的原则编写面向对象程序导致的问题比它解决的问题多吗？*

*** ***注*** *: IME，那些不想使用 SOLID 的人当中，很少有人能实际给出它失败的例子，而且往往不能证明他们在专业能力上练习过。*

人们普遍认为，遵循面向对象编程的坚实原则可以产生更易于维护和扩展的代码。然而，在有些情况下，严格遵守这些原则可能会导致比它解决的问题更多的问题。这里有几个例子:

1.  过于复杂的设计:虽然坚实的原则鼓励干净和模块化的代码，但也有可能将它们发挥到极致，最终导致过于复杂的设计。这可能会使代码难以理解和维护，特别是对于加入项目的新开发人员。
2.  不灵活的设计:坚实的原则有时会导致难以改变或扩展的不灵活的设计。例如，单一责任原则(SRP)规定一个类应该只有一个改变的理由。虽然这可以产生更稳定的代码，但是当项目的需求发生变化时，它也会使更改变得困难。
3.  不必要的抽象:坚实的原则鼓励使用抽象来分离组件并减少变更的影响。然而，过度使用抽象会导致不必要的复杂性，使代码更难理解。
4.  性能问题:在某些情况下，可靠的原则会导致代码效率更低或者性能更差。例如，开闭原则(OCP)鼓励使用继承和多态，这在某些情况下会对性能产生负面影响。

重要的是要记住，坚实的原则是指导方针，而不是严格的规则，由开发人员决定设计原则和项目特定需求之间的适当平衡。

> 对于 OOP 实践来说，坚实的原则过时了吗？或者说，它们经受住了时间的考验吗？*

*** ***注*** *:我相信它们仍然成立，有些稍加调整或添加了上下文。有些人可能认为它们过时了，比如 Dan North，我是他 BDD 工作的忠实粉丝。*

SOLID principles 是一套设计面向对象软件的指导原则，最初由 Robert C. Martin 在 21 世纪初提出。它们已经被广泛采用，并且今天仍然被认为是设计面向对象软件的相关和有用的原则。

坚实的原则基于面向对象设计的基本概念，如封装、抽象和模块化，它们旨在帮助开发人员创建可理解、灵活和可维护的软件设计。这些原则并不特定于任何特定的编程语言或技术，它们可以应用于广泛的软件开发环境。

虽然坚实的原则并不是所有设计问题的万能解决方案，并且在应用它们时使用良好的判断和考虑项目的具体环境是很重要的，但是它们作为设计面向对象软件的一组有用的指导原则经受了时间的考验。

> 就长期代码可维护性而言，目前是否有任何 OOP 范例或设计模式可以更好地替代坚实的原则？为什么？

SOLID 是一套设计面向对象软件的原则，旨在使软件设计更易于理解、灵活和维护。这些原则基于面向对象设计的基本概念，例如封装、抽象和模块化，它们旨在帮助开发人员创建易于理解、易于更改和易于扩展的软件设计。

还有许多其他的设计模式和软件设计原则也可以用来提高代码的长期可维护性。一些例子包括:

*   设计模式:设计模式是软件开发中常见设计问题的可重用解决方案。它们提供了一种方法来描述如何在给定的上下文中以解决特定问题的方式来构造类和对象。一些有助于提高代码可维护性的设计模式示例包括工厂模式、单例模式和观察者模式。
*   干净的架构:干净的架构是一种软件设计哲学，强调关注点的分离和抽象的使用，以使软件设计更加灵活和可维护。它提倡在系统的不同部分之间使用定义明确的边界，并鼓励使用依赖注入来分离组件。
*   领域驱动设计(DDD): DDD 是一种软件设计方法，它集中于对软件系统的问题领域进行建模，以便创建与业务需求紧密结合的设计。它鼓励使用特定领域的语言，并关注系统不同部分之间的关系。

总的来说，坚实的原则仍然被广泛使用，并被认为是设计面向对象软件的一套有价值的指导原则。然而，有许多其他方法和原则也可以用于提高代码的长期可维护性，并且使用适合项目特定上下文的技术和原则的组合是很重要的。

> 就代码可维护性而言，CUPID 比 SOLID 更有效吗？

丘比特并不是一个众所周知的软件设计原则或模式。当您提到与代码可维护性相关的丘比特时，不清楚您指的是什么。

*ChatGPT 接着重复了之前对 SOLID 的描述，然后提到了设计模式、干净的架构和 DDD，以最后一个答案的缩写形式。注:ChatGPT 只有截至 2021 年的知识；丘比特可能太新了。*

> 你能给出 5 个坚实原则的 C++例子吗？*

**注:由于答案长度原因，被截断。我不得不反复要求 ChatGPT 完成它的最后一个回答。后来，当被要求单独给出每个原理的例子时，它给出了完全不同的源代码例子，并更详细地解释了它是如何工作的。为了简洁起见，我发布了简短的版本。*

## 1.单一责任原则(SRP):一个类应该只有一个改变的理由。

例如，考虑一个负责管理雇员信息的类`Employee`，比如姓名、薪水和职位。这个类违反了 SRP，因为它有多种原因需要更改——例如，如果我们需要为雇员地址添加一个新字段，我们就必须修改`Employee`类。

下面是一个我们如何重构`Employee`类来遵守 SRP 的例子:

```
class EmployeeInfo {
 public:
  EmployeeInfo(const std::string& name, double salary, const std::string& job_title)
      : name_(name), salary_(salary), job_title_(job_title) {}

  std::string GetName() const { return name_; }
  double GetSalary() const { return salary_; }
  std::string GetJobTitle() const { return job_title_; }

 private:
  std::string name_;
  double salary_;
  std::string job_title_;
};

class EmployeeAddress {
 public:
  EmployeeAddress(const std::string& street, const std::string& city, const std::string& state,
                  const std::string& zip)
      : street_(street), city_(city), state_(state), zip_(zip) {}

  std::string GetStreet() const { return street_; }
  std::string GetCity() const { return city_; }
  std::string GetState() const { return state_; }
  std::string GetZip() const { return zip_; }

 private:
  std::string street_;
  std::string city_;
  std::string state_;
  std::string zip_;
};

class Employee {
 public:
  Employee(const EmployeeInfo& info, const EmployeeAddress& address)
      : info_(info), address_(address) {}

  const EmployeeInfo& GetInfo() const { return info_; }
  const EmployeeAddress& GetAddress() const { return address_; }

 private:
  EmployeeInfo info_;
  EmployeeAddress address_;
};
```

## 2.开闭原则(OCP):软件实体(类、模块、函数等)。)应该对扩展开放，但对修改关闭。

例如，考虑一个具有计算形状面积的方法`double GetArea()`的类`Shape`。我们可以通过使用继承和多态来扩展这个类以支持新的形状:

```
class Shape {
 public:
  virtual double GetArea() const = 0;
};

class Circle : public Shape {
 public:
  Circle(double radius) : radius_(radius) {}
  double GetArea() const override { return 3.14 * radius_ * radius_; }

 private:
  double radius_;
};

class Rectangle : public Shape {
 public:
  Rectangle(double width, double height) : width_(width), height_(height) {}
  double GetArea() const override { return width_ * height_; }

 private:
  double width_;
  double height_;
};
```

## 3.Liskov 替换原则(LSP):子类型必须可以替换它们的基类型。

Liskov 替换原则(LSP)是面向对象编程中的一个原则，它指出超类的对象应该能够被子类的对象替换，而不会影响程序的正确性。换句话说，如果一个程序是在假设它可以处理超类的对象的情况下编写的，那么它也应该可以处理该超类的任何子类的对象。

```
class Shape {
 public:
  virtual double GetArea() const = 0;
};

class Circle : public Shape {
 public:
  Circle(double radius) : radius_(radius) {}
  double GetArea() const override { return 3.14 * radius_ * radius_; }

 private:
  double radius_;
};

class Rectangle : public Shape {
 public:
  Rectangle(double width, double height) : width_(width), height_(height) {}
  double GetArea() const override { return width_ * height_; }

 private:
  double width_;
  double height_;
};

void ProcessShapes(std::vector<Shape*> shapes) {
  for (Shape* shape : shapes) {
    std::cout << shape->GetArea() << std::endl;
  }
}

int main() {
  std::vector<Shape*> shapes;
  shapes.push_back(new Circle(2.0));
  shapes.push_back(new Rectangle(3.0, 4.0));

  ProcessShapes(shapes);

  return 0;
}
```

在这个例子中，`Circle`和`Rectangle`类是`Shape`基类的子类型。`ProcessShapes`函数需要一个由`Shape`指针组成的向量，可以处理任何遵循`Shape`接口的派生类。这是 Liskov 替换原则的一个实例，因为派生类可以与基类互换使用。

## 4.接口隔离原则(ISP):不应该强迫客户依赖他们不使用的接口。

例如，考虑一个类`Shape`，它有许多方法用于不同的运算，比如计算面积、周长和体积。不应要求仅需要计算面积的客户依赖于计算周长和体积的方法:

```
class Shape {
 public:
  virtual double GetArea() const = 0;
  virtual double GetPerimeter() const = 0;
  virtual double GetVolume() const = 0;
};

class Circle : public Shape {
 public:
  Circle(double radius) : radius_(radius) {}
  double GetArea() const override { return 3.14 * radius_ * radius_; }
  double GetPerimeter() const override { return 2 * 3.14 * radius_; }
  double GetVolume() const override { return 0.0; }  // not applicable for circles

 private:
  double radius_;
};

class Rectangle : public Shape {
 public:
  Rectangle(double width, double height) : width_(width), height_(height) {}
  double GetArea() const override { return width_ * height_; }
  double GetPerimeter() const override { return 2 * (width_ + height_); }
  double GetVolume() const override { return 0.0; }  // not applicable for rectangles

 private:
  double width_;
  double height_;
};
```

为了遵守 ISP，我们可以将`Shape`接口分割成多个更小的接口:

```
class AreaCalculator {
 public:
  virtual double GetArea() const = 0;
};

class PerimeterCalculator {
 public:
  virtual double GetPerimeter() const = 0;
};

class VolumeCalculator {
 public:
  virtual double GetVolume() const = 0;
};

class Circle : public AreaCalculator, public PerimeterCalculator {
 public:
  Circle(double radius) : radius_(radius) {}
  double GetArea() const override { return 3.14 * radius_ * radius_; }
  double GetPerimeter() const override { return 2 * 3.14 * radius_; }
};

class Rectangle : public AreaCalculator, public PerimeterCalculator {
 public:
  Rectangle(double width, double height) : width_(width), height_(height) {}
  double GetArea() const override { return width_ * height_; }
  double GetPerimeter() const override { return 2 * (width_ + height_); }

 private:
  double width_;
  double height_;
};

void ProcessShapes(std::vector<AreaCalculator*> shapes) {
  for (AreaCalculator* shape : shapes) {
    std::cout << shape->GetArea() << std::endl;
  }
}

int main() {
  std::vector<AreaCalculator*> shapes;
  shapes.push_back(new Circle(2.0));
  shapes.push_back(new Rectangle(3.0, 4.0));

  ProcessShapes(shapes);

  return 0;
}
```

在这个例子中，`Circle`和`Rectangle`类实现了`AreaCalculator`接口，该接口只定义了`GetArea`方法。`ProcessShapes`函数需要一个`AreaCalculator`指针的向量，只需要计算面积，所以不依赖于`Shape`接口的其他方法。这符合 ISP 的要求，因为客户不会被迫依赖他们不使用的接口。

## 5.依赖倒置原则(DIP):高层模块不应该依赖低层模块。两者都应该依赖于抽象。

例如，考虑一个依赖于类`Shape`来呈现不同类型形状的类`ShapeRenderer`。为了坚持 DIP，我们可以引入一个抽象，比如接口`Shape`,`ShapeRenderer`和具体的`Shape`实现都依赖于它:

```
class Shape {
 public:
  virtual void Draw() const = 0;
};

class Circle : public Shape {
 public:
  Circle(double x, double y, double radius) : x_(x), y_(y), radius_(radius) {}
  void Draw() const override {
    // draw a circle at (x_, y_) with radius radius_
  }

 private:
  double x_;
  double y_;
  double radius_;
};

class Rectangle : public Shape {
 public:
  Rectangle(double x, double y, double width, double height)
      : x_(x), y_(y), width_(width), height_(height) {}
  void Draw() const override {
    // draw a rectangle at (x_, y_) with
```

在这个例子中，`ShapeRenderer`类依赖于`Shape`抽象，而不是像`Circle`这样的具体实现。这符合 DIP，因为`ShapeRenderer`类是高级模块而`Shape`类是低级模块，两者都依赖于`Shape`抽象而不是彼此。这允许我们在不影响`ShapeRenderer`类的情况下改变具体的`Shape`实现。

> 可以举一个使用 IoC 容器的 C++例子吗？

当然可以！下面是一个在 C++中使用控制反转(IoC)容器来管理程序中对象之间的依赖关系的例子。

首先，让我们为 logger 对象定义一个简单的接口:

```
class ILogger {
 public:
  virtual void log(const std::string& message) = 0;
};
```

接下来，我们可以定义记录器接口的具体实现:

```
class ConsoleLogger : public ILogger {
 public:
  void log(const std::string& message) override {
    std::cout << message << std::endl;
  }
};
```

现在，假设我们有一个依赖 logger 接口来记录消息的类:

```
class UserService {
 public:
  explicit UserService(std::shared_ptr<ILogger> logger) : logger_(std::move(logger)) {}

  void createUser(const std::string& name) {
    logger_->log("Creating user: " + name);
    // ...
  }

 private:
  std::shared_ptr<ILogger> logger_;
};
```

要使用 IoC 容器来管理这些对象之间的依赖关系，我们可以定义一个容器类，它知道如何创建和管理不同类型的实例:

```
class Container {
 public:
  template <typename T>
  void registerType() {
    typeMap_[typeid(T)] = [this]() { return std::make_shared<T>(*this); };
  }

  template <typename T>
  std::shared_ptr<T> resolve() {
    auto it = typeMap_.find(typeid(T));
    if (it == typeMap_.end()) {
      throw std::runtime_error("Type not registered in container");
    }
    return std::static_pointer_cast<T>(it->second());
  }

 private:
  std::map<std::type_index, std::function<std::shared_ptr<void>()>> typeMap_;
};
```

要使用容器，我们可以注册它应该能够创建和管理的类型，然后使用`resolve`方法来获取特定类型的实例:

```
int main() {
  Container container;
  container.registerType<ConsoleLogger>();
  container.registerType<UserService>();

  auto logger = container.resolve<ILogger>();
  auto userService = container.resolve<UserService>();
  userService->createUser("Alice");
  return 0;
}
```

在这个例子中，容器将创建一个`ConsoleLogger`的实例，并在创建`UserService`对象时将其注入到`UserService`中。这允许`UserService`使用记录器，而不必知道如何创建它或者它正在使用什么具体实现。

ChatGPT 对坚实的原理有相当不错的把握吗？我觉得可以；也许比一些开发商做的还要好。虽然，我个人不会从同样的角度来解释或者使用同样的例子。

它的知识面足够教你用 SOLID 编码吗？是的，但是你可能想就每个原则分别问多个问题，以获得最大收益。你可以在任何特定的 OOP 语言中找到例子，这也很好。然而，最好的学习方法是在实践它的团队中工作。

这是一个总结！让我知道你是否有问题要问或者用不同的措辞。你对坚实的原则有什么看法？