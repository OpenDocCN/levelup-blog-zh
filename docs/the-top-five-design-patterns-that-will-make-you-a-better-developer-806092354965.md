# 让你成为更好的开发人员的五大设计模式

> 原文：<https://levelup.gitconnected.com/the-top-five-design-patterns-that-will-make-you-a-better-developer-806092354965>

![](img/4e9b1fbcb8926aea10b5e5bdf01fb874.png)

照片由克里斯蒂娜·wocintechchat.com 在 Unsplash 上拍摄

设计模式是解决软件开发中常见问题的最佳实践。他们还会让你的代码在几个月不接触它之后，对其他开发者和你自己来说更容易理解。在本文中，您将深入了解五种设计模式，它们将为您节省大量时间和精力。

所以让我们开始吧！

**1。单例模式**

![](img/d86374501bc6e5aa3c29a1b913f49aa9.png)

罗伯特·v·鲁杰罗在 Unsplash 上的照片

Singleton 模式是一种设计模式，它涉及创建一个类的单个实例，并提供一个全局访问点。这种模式在只需要特定对象的一个实例的情况下非常有用，因为它有助于减少内存使用并提高性能。

在中实现单例模式的一种方法是使用自调用函数来创建单个实例。该函数在定义后会立即被调用，从而创建对象的单个实例。然后，实例被存储为函数的属性，允许从应用程序中的任何地方访问它。

这里有一个用 JavaScript 实现的单例模式的例子:

```
const Singleton = (function() {
  let instance;

  function createInstance() {
    // create the single instance of the object
    const object = new Object("I am the single instance");
    return object;
  }

  return {
    getInstance: function() {
      // return the single instance of the object, creating it if necessary
      if (!instance) {
        instance = createInstance();
      }
      return instance;
    }
  };
})();

// usage
const instance1 = Singleton.getInstance();
const instance2 = Singleton.getInstance();

console.log(instance1 === instance2); // true 
```

在这个例子中，Singleton 函数是一个自调用函数，它用 getInstance 方法返回一个对象。getInstance 方法用于检索对象的单个实例。如果实例尚未创建，则调用 createInstance 函数来创建它。

以这种方式使用 Singleton 模式可以确保对象只有一个实例，而不管 getInstance 方法被调用了多少次。这在各种情况下都很有用，例如当您希望确保某个特定服务只有一个实例时，或者当您希望将某个特定对象的实例数量限制为一个时。

总的来说，Singleton 模式是一种有用的设计模式，有助于提高应用程序的性能和效率。通过创建对象的单个实例并提供对它的全局访问，可以避免创建同一对象的多个实例的开销，并确保应用程序的所有部分都使用同一个实例。

**2。工厂模式**

![](img/4bfb41c9e6aab179fa8830fa4ee0a931.png)

帕特里克·亨德利在 Unsplash 上的照片

工厂模式是中的一种设计模式，它涉及通过工厂函数而不是直接使用构造函数来创建对象。这允许您抽象对象的创建，并为创建对象创建一致的界面。

使用工厂模式的主要好处之一是，它允许您创建对象，而无需指定它们的特定类。当您需要创建大量相似的对象时，这可能很有用，因为您可以使用工厂函数通过相同的接口生成它们。

例如，考虑一个简单的工厂函数，它创建一个具有名称和值的新对象:

```
function createObject(name, value) {
  return {
    name: name,
    value: value
  };
}

const obj1 = createObject(‘object 1', 1);
const obj2 = createObject(‘object 2', 2); 
```

在本例中，createObject 函数充当工厂，它用指定的名称和值属性创建一个新对象。然后，您可以使用该函数创建任意数量的对象，所有对象都具有相同的结构。

工厂模式的另一个好处是，它允许您封装对象的创建，并对应用程序的其余部分隐藏实现细节。这可以更容易地改变对象的创建方式，而不会影响代码的其余部分。

例如，您可能开始使用如上所示的简单工厂函数，但是后来决定切换到使用更复杂的对象创建过程。使用工厂模式，您可以进行这种更改，而不必更新使用工厂功能的应用程序的每个部分。

除了为创建对象提供一致的接口之外，工厂模式还可以用来创建具有不同类型或行为的对象。例如，您可能有一个工厂函数，它根据接收到的参数创建不同类型的对象。

```
function createObject(type, name, value) {
  if (type === 'object') {
    return {
      name: name,
      value: value
    };
  } else if (type === 'array') {
    return [name, value];
  }
}

const obj1 = createObject('object', 'object 1', 1);
const obj2 = createObject('array', 'object 2', 2); 
```

在本例中，createObject 函数根据类型参数创建一个对象或一个数组。这允许您使用相同的工厂函数来创建具有不同行为的不同类型的对象。

就我个人而言，我喜欢结合使用工厂模式和存储库模式来管理我在大型项目中的所有 axios 调用。这样，我就有了一个简单的接口，可以在一个地方统一定义我的应用程序中需要的任何 API 调用。这极大地减少了 API 更改时的工作量。API 很可能会随着时间而改变。

总的来说，工厂模式是创建对象的有用工具。它允许您抽象创建过程，创建具有一致接口的对象，以及创建具有不同类型或行为的对象。通过使用工厂模式，您可以使您的代码更加灵活和易于维护。

**3。观察者模式**

![](img/5f40c92bce4a0ff0753adc99a964fab8.png)

照片由 abigail low 在 Unsplash 上拍摄

观察者模式是一种设计模式，在这种模式中，一个称为主题的对象维护一个称为观察者的从属对象列表，并自动通知它们任何状态变化，通常是通过调用它们的方法之一。这种模式对于实现反应式编程很有用，在反应式编程中，对象可以自动对其他对象的变化做出反应。

观察者模式的一个常见用例是在用户界面(UI)开发中，其中 UI 元素(观察者)可能需要根据应用程序数据(主题)的变化来更新它们的显示。例如，如果用户向待办事项列表添加了新项目，列表显示(观察者)可能需要更新以显示新项目。

举个例子，在 JavaScript 中，观察者模式可以使用发布-订阅模式来实现，其中主体向其观察者发布更新，然后观察者可以订阅以接收这些更新。这可以使用事件和事件监听器，或者使用更正式的发布-订阅库来完成。

为了使用事件和事件侦听器实现观察者模式，主体可以定义一个自定义事件，并在其状态发生变化时触发它。然后，观察者可以为该事件添加事件侦听器，并在事件被触发时更新他们自己的状态。

下面是一个例子，以待办事项列表应用程序为例，说明如何在 JavaScript 中实现观察者模式:

```
// Define the subject (to-do list)
class ToDoList {
  constructor() {
    this.tasks = [];
    this.addTaskEvent = new Event('addTask');
  }

  // Method to add a new task to the list
  addTask(task) {
    this.tasks.push(task);
    document.dispatchEvent(this.addTaskEvent);
  }
}

// Define the observer (to-do list display)
class ToDoListDisplay {
  constructor(list) {
    this.list = list;
    this.element = document.getElementById('todo-list');
  }

  // Method to update the display when a new task is added
  update() {
    this.element.innerHTML = '';
    this.list.tasks.forEach((task) => {
      const taskElement = document.createElement('li');
      taskElement.innerHTML = task;
      this.element.appendChild(taskElement);
    });
  }

  // Method to set up the event listener for new tasks
  init() {
    document.addEventListener('addTask', () => this.update());
  }
}

// Create the subject and observer, and set up the event listener
const toDoList = new ToDoList();
const toDoListDisplay = new ToDoListDisplay(toDoList);
toDoListDisplay.init();

// Add a new task to the list and trigger the event
toDoList.addTask('Take out the trash'); 
```

在这个例子中，ToDoList 类表示主题，ToDoListDisplay 类表示观察者。ToDoList 类维护一个任务列表，并定义一个名为“addTask”的自定义事件，每当添加一个新任务时都会触发该事件。ToDoListDisplay 类有一个 update 方法，用于更新显示以显示当前任务列表，还有一个 init 方法，用于设置“addTask”事件的事件监听器。当 ToDoList 对象添加一个新任务时，它会触发“addTask”事件，从而导致待办事项列表更新。

我使用了 observer 模式来实现一个强大的撤销重做系统，该系统像 todo list 示例那样向撤销堆栈添加操作。

**4。构建器模式**

![](img/7d7b8c40c456669e91f3ed35e398ab8f.png)

Ralph (Ravi) Kayden 在 Unsplash 上拍摄的照片

构建器模式是一种创造性的设计模式，允许您逐步创建复杂的对象。它将对象的构造与其表示分离，允许您使用相同的构造过程创建同一对象的各种表示。

构建器模式中有两个主要组件:构建器和控制器。构建者负责构建对象，而导演负责指定构建过程中的步骤。

当您想要创建具有多个部分或需要多个步骤来构建的复杂对象时，构建器模式特别有用。它允许您封装构造过程，使更改构造过程或创建同一对象的不同表示更加容易。

构建器模式的主要好处之一是它允许您创建根据特定需求定制的对象。例如，您可以使用 builder 模式创建一个具有不同选项的汽车对象，例如引擎类型、汽车颜色或车轮类型。

要实现构建器模式，首先要定义一个构建器接口，它概括了创建对象所需的步骤。接下来，您将创建一个具体的构建器类，它实现了构建器接口并提供了构建过程的实际实现。最后，您将创建一个 director 类，该类使用生成器来创建对象。

以下是 JavaScript 中构建器模式的一个示例:

```
class CarBuilder {
  constructor() {
    this.car = {};
  }

  setType(type) {
    this.car.type = type;
    return this;
  }

  setColor(color) {
    this.car.color = color;
    return this;
  }

  setWheels(wheels) {
    this.car.wheels = wheels;
    return this;
  }

  build() {
    return this.car;
  }
}

class Director {
  constructSportsCar(builder) {
    return builder
      .setType('sports')
      .setColor('red')
      .setWheels('low profile');
  }

  constructSUV(builder) {
    return builder
      .setType('SUV')
      .setColor('blue')
      .setWheels('off road');
  }
}

const director = new Director();
const sportsCarBuilder = new CarBuilder();
const suvBuilder = new CarBuilder();

const sportsCar = director.constructSportsCar(sportsCarBuilder).build();
console.log(sportsCar); // { type: 'sports', color: 'red', wheels: 'low profile' }

const suv = director.constructSUV(suvBuilder).build();
console.log(suv); // { type: 'SUV', color: 'blue', wheels: 'off road' } 
```

在本例中，CarBuilder 类定义了创建汽车对象所需的步骤，包括类型、颜色和车轮。Director 类指定了两种不同类型汽车的构造过程:一辆跑车和一辆 SUV。构建器模式允许您重用相同的构造过程来创建同一对象的多个表示，从而可以轻松地创建定制的对象，而不必修改构造过程。

这是设计模式中我个人最喜欢的。我喜欢用它来生成测试数据。在处理具有大量默认值的对象时，它也非常有用。

总的来说，构建器模式是以灵活和模块化的方式创建复杂对象的强大工具。它允许您封装构造过程，使更改或定制正在创建的对象变得容易。

**5。装饰图案:**

![](img/a38f83c6e0048239cc0125e3316f42c0.png)

凯利·西克玛在 Unsplash 上的照片

装饰模式是一种设计模式，允许您向现有对象添加新行为，而无需修改它们的代码。这是通过将对象包装在装饰对象中实现的，装饰对象可以为原始对象添加新的功能。

装饰模式的好处之一是，它允许您在运行时添加或删除对象的行为，而不必修改对象的代码。这在需要向对象添加新功能，但不想修改对象本身的情况下特别有用。

例如，考虑一个显示产品列表的应用程序。每个产品都有名字和价格。您可能希望向应用程序添加一个新功能，以显示列表中所有产品的总价。使用 decorator 模式，您可以创建一个 decorator 对象来计算产品的总价格，并将其显示在页面上。

要实现装饰模式，首先要定义一个接口或抽象类，它定义了要添加到原始对象的行为。然后，您将创建一个装饰类，它实现这个接口并保存对原始对象的引用。decorator 类会将大部分行为委托给原始对象，但它也会添加您想要实现的新行为。

下面是一个如何在 JavaScript 中使用装饰模式的例子:

```
class Car {
  constructor() {
    this.price = 10000;
  }

  getPrice() {
    return this.price;
  }
}

class CarWithSunroof {
  constructor(car) {
    this.car = car;
  }

  getPrice() {
    return this.car.getPrice() + 2000;
  }
}

const car = new Car();
console.log(car.getPrice()); // 10000

const carWithSunroof = new CarWithSunroof(car);
console.log(carWithSunroof.getPrice()); // 12000 
```

在本例中，我们有一个基本价格为 10，000 美元的汽车类。然后，我们可以创建一个 CarWithSunroof 装饰器，在汽车的基础价格上增加 2000 美元的附加费。当我们创建一个新的 CarWithSunroof 对象并调用 getPrice 方法时，它返回汽车的基本价格加上附加费。

装饰模式的另一个好处是，它可以用来在运行时动态地添加或删除行为。例如，您可能有一个汽车对象，可以用不同的功能进行装饰，如天窗、导航系统或高级音频系统。然后，您可以为这些特性中的每一个创建装饰对象，并根据需要从 Car 对象中添加或删除它们。

总的来说，decorator 模式是以灵活和模块化的方式向对象添加新行为的有用工具。它可以用来在运行时动态地添加或删除行为，使其成为创建高度可定制对象的有用模式。

# 结论

设计模式太神奇了！这些概念已经被证明了无数次，会让你的代码更容易理解，也更容易阅读。当计划一个新的特性时，如果有一个设计模式能够完成这个任务，你应该总是看看你的工具箱里面。我希望这次深入探讨能够为您的工具箱添加一些新工具，并全面提升您的编码技能。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)