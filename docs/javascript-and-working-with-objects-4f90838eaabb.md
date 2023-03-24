# Javascript 和使用对象

> 原文：<https://levelup.gitconnected.com/javascript-and-working-with-objects-4f90838eaabb>

## Javascript 工程:背后的科学

## 每个程序员都应该知道的 JavaScript 概念

![](img/4a7a914196d97adddbdbcd314b65e00f.png)

照片由 [Chuttersnap](https://unsplash.com/@chuttersnap) 拍摄

除了我之前的两篇关于 Javascript 的文章，我还想谈谈对象和使用对象的问题，这不仅是 Javascript 开发人员，也是大多数语言开发人员都必须处理的问题。

[](https://blog.devgenius.io/javascript-concepts-every-programmer-should-know-v1-0-2-cc87f541e05) [## Javascript 工程:背后的科学

### 每个程序员和工程师都应该知道的 Javascript 概念背后的科学故事

blog.devgenius.io](https://blog.devgenius.io/javascript-concepts-every-programmer-should-know-v1-0-2-cc87f541e05) [](https://javascript.plainenglish.io/javascript-concepts-every-programmer-should-know-d04731fe7a7c) [## 每个程序员都应该知道的 JavaScript 概念

### 了解这些 JavaScript 概念可以让你的程序员生活更轻松。

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-concepts-every-programmer-should-know-d04731fe7a7c) 

# 目标

Javascript 围绕着一个简单的基于对象的范例。我们都知道对象和类，我们可能整个职业生涯都在使用它们。

对象是一组属性，属性是该对象的属性。和*美国人类*很有可比性。我们有自己的属性，无论是名、姓、出生日期，还是身高和体重。

对象和面向对象编程实际上是受到现实生活中对象的启发。

以下示例介绍了如何处理对象，从简单到更高级的情况。

# 实例化对象的方法

实例化对象的常用方法如下

```
# Create new object
const site = {
    name: "Medium",
    domain: "medium.com",
    type: "Writing Platform"
}
```

但上面是一个对象初始化器，用花括号`{}`括起来的逗号分隔的属性列表，也是较短的语法。更严格的方法如下。

```
# Create new object
const site = new Object();
site.name = "Medium";
site.domain = "medium.com";
site.type = "Writing Platform";
```

为了设置属性本身，您可以使用该属性的键名，或者使用括号`[]`来分配它

```
site['name'] = "Medium";
```

使用这种方法的好处是可以使用变量设置属性值。

```
const propertyName = "name";
const propertyValue = "Medium";
site[propertyName] = propertyValue;
```

当我们使用集合、映射列表、对象、类型等时，知道并记住这一点可能非常有用。

# 班级

处理对象离不开处理类。

你很可能知道类是如何工作的，所以简而言之——它是对象的模板，定义了实例的属性和功能。一个对象将拥有。

```
# Classes
class Website {
    constructor(name, domain, type){
        this.name = name;
        this.domain = domain;
        this.type = type;
    } // Getter
    get url() {
        return `https://www.${this.domain}`;
    } // Method
    sayHello() {
        return `Hello from ${this.name}!`;
    }
}const medium = new Website("Medium",
    "medium.com",
    "Writing Platform");const url = medium.url;
const greeting = medium.sayHello();
```

在上面的例子中，`constructor`本身定义了三个字段。这与事先自行定义字段是一样的。

然而，有趣的是使用了被定义为`get`函数的`getters`。它不像常规函数那样可调用，但行为像一个属性。

# 菲尔茨

字段或属性实际上可以在构造函数之前声明。

```
# Fields and Private Fields
class Website {
    name;
    domain;
    #type;

    constructor(name, domain, type){
        this.name = name;
        this.domain = domain;
        this.#type = type;
    }
}const medium = new Website("Medium",
    "medium.com",
    "Writing Platform");
```

注意如何使用符号`#`来声明私有字段。该字段不能在类外公开访问。

# 生成器和产出结果

生成器和关键字`yield`是 Javascript 中相对较新的东西。`yield`关键字让我们聚合，*从一个循环中产生*结果——这在其他语言中并不常见，但在 C#中已经存在很长时间了。

```
# Generator and Yielding results
class Website {
    constructor(tabs) {
        this.tabs = tabs;
    }*getTabs() {
        for(const tab of this.tabs){
            yield tab;
    }
}

const medium = new Website(["Home",
    "Recent","About Us",
    "Register"]);const generator = medium.getTabs();
const tabs = [...generator];
```

为了从生成器中获取值，我们实际上需要*扩展*它，因为函数本身返回类型，而不是值。

# 扩展类

类也可以用 Javascript 扩展。

```
# Extending Classes
class BaseWebsite {
    constructor(name, domain, type){
        this.name = name;
        this.domain = domain;
        this.type = type;
    }

    sayHello() {
        return "Hello!";
    }
}class HttpsWebsite extends BaseWebsite {
    constructor(name, domain, type){
        super(name, domain, type);
        this.scheme = 'https';
    }

    sayHello() {
        let superHello = super.sayHello();
        return `${superHello} from ${this.name}`;
    }
}const medium = new HttpsWebsite("Medium",
    "medium.com",
    "Writing Platform");

const hello = medium.sayHello();
```

利用`extend`和`super`关键字，我们能够扩展现有类的属性和方法。

# 基类方法调用

在前面提到的例子中，注意我们如何声明基本函数`sayHello`，覆盖它，但是仍然使用关键字`super`来使用它。在`constructor`中也是如此。

# 混合食品

多重继承在 Javascript 中是不可能的，即。类最多只能扩展一个超类。继承本身发生在运行时，Javascript 搜索对象的原型链，以便找到它声明的属性和值。

然而，我们可以利用和使用 Mix-in 来实现这一点。考虑下面的例子

```
# Mixins
let WalkMixin = superclass => class extends superclass {
  walk() {
      return "I'm walking!";
  }
};let FlyMixin = superclass => class extends superclass {
  fly() {
      return "I'm flying!";
  }
};class BaseClass {}
class SampleClass extends WalkMixin(FlyMixin(BaseClass)) {};
const sample = new SampleClass();console.log(sample.walk());
console.log(sample.fly());
```

如果您检查实例`sample`，您会注意到它的原型链包含了上述混合中定义的两种方法。

注意，这不是 Javascript 特性，而更像是一种变通方法。

我希望这篇文章对你有用，让你愉快。这是我最近开始写的关于 Javascript 概念的系列文章的一部分，所以请收听！如果你喜欢它的内容，点击 follow，和我一起踏上研究和撰写更多关于 Javascript 背后的科学和工程的旅程。

感谢您的阅读！🎉