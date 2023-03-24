# JavaScript 设计模式:装饰模式

> 原文：<https://levelup.gitconnected.com/javascript-design-patterns-decorator-pattern-fc278652bd03>

## 从 ES6: Decorator 模式重新连接 JavaScript 设计模式

![](img/b45f8e15df77a3cf13dd1c158b099155.png)

## 1.什么是装饰者模式

向现有对象添加新功能而不改变其结构的设计模式称为装饰模式，它充当现有类的包装器。

装饰者可以理解为游戏角色购买的装备。比如 LOL 中的英雄，刚开始游戏的时候只有基础攻击力和魔法力。但购买装备后，触发攻击和技能时，可以享受装备带来的输出加成。我们可以理解为购买的装备装饰了英雄的攻击和技能的相关方法。

## 2.ESnext 中的装饰模式

ESnext 中有一个 Decorator 建议，它使用一个以@开头的函数来修饰 ES6 中的类及其属性和方法。

目前，Decorator 的语法只是一个建议。如果现在想使用装饰器模式，需要安装并配合`babel` + `webpack` 并结合插件实现。

*npm 安装依赖关系*

```
npm install babel-core babel-loader babel-plugin-transform-decorators babel-plugin-transform-decorators-legacy babel-preset-env
```

*配置* `*.babelrc*`文件*文件*

```
{
  "presets": ["env"],
  "plugins": ["transform-decorators-legacy"]
}
```

在`webpack.config.js`中增加`babel-loader`

```
module: {
    rules: [
      { test: /\.js$/, exclude: /node_modules/, loader: "babel-loader" }
    ],
  }
```

如果您的 IDE 是 Visual Studio 代码，您可能还需要在项目根目录中添加下面的`tsconfig.json`文件来组织 ts 检查错误。

```
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "allowJs": true,
    "lib": [
      "es6"
    ],
  }
}
```

下面我将实现 3 个装饰者，`@autobind, @debounce, **@**deprecate**.**`

**2.1** `**@autobind**` **实现** `**this**` **指向原对象**

在 JavaScript 中，`this` 指向的问题一直是一个常见的话题。新手在使用`Vue` 或`React`等框架的过程中，很可能会不小心失去`this` 的指向，导致方法调用错误。例如下面的代码:

```
class Person {
  getPerson() {
    return this;
  }
}

let person = new Person();
let { getPerson } = person;

console.log(getPerson() === person); // false
```

在上面的代码中，`getPerson` 方法中的`this` 默认指向`Person` 类的一个实例，但是如果`Person`是通过析构赋值提取的，那么`this` 指向`undefined`。所以最终打印结果是假的。

此时，我们可以实现一个`autobind` 函数来修饰`getPerson` 方法，使`this`始终指向`Person`的一个实例。

```
function autobind(target, key, descriptor) {
  var fn = descriptor.value;
  var configurable = descriptor.configurable;
  var enumerable = descriptor.enumerable;

  // return descriptor
  return {
    configurable: configurable,
    enumerable: enumerable,
    get: function get() {
      // bind this
      var boundFn = fn.bind(this);
      // Use Object.defineProperty to redefine the method
      Object.defineProperty(this, key, {
        configurable: true,
        writable: true,
        enumerable: false,
        value: boundFn
      })

      return boundFn;
    }
  }
}
```

我们通过`bind`实现`this` 的绑定，用`Object.defineProperty`重写`get`中的方法，将值定义为 bind 绑定的`boundFn` 函数，这样这个就一直指向实例。接下来我们装饰并调用`getPerson`方法。

```
class Person {
  @autobind
  getPerson() {
    return this;
  }
}

let person = new Person();
let { getPerson } = person;

console.log(getPerson() === person); // true
```

**2.2 @去抖实现**去抖**功能**

去抖功能在前端项目中有很多应用，比如在 resize 或 scroll 等事件中操纵 DOM，或者实现用户输入的实时 ajax 搜索，这些都会被频繁触发。前者会对浏览器性能产生直观的影响。后者会给服务器造成很大压力。我们期待这样高频率的连续触发事件在触发结束后有所反应。这就是函数防抖的应用。

```
class Editor {
  constructor() {
    this.content = '';
  }

  updateContent(content) {
    console.log(content);
    this.content = content;
    // There are some performance-consuming operations behind
  }
}

const editor1 = new Editor();
editor1.updateContent(1);
setTimeout(() => { editor1.updateContent(2); }, 400);

const editor2= new Editor();
editor2.updateContent(3);
setTimeout(() => { editor2.updateContent(4); }, 600);

// result: 1 3 2 4
```

在上面的代码中，我们定义了`Editor` 类，其中`updateContent` 方法将在用户输入时执行，并且可能有一些消耗性能的 DOM 操作。这里我们打印方法内部的传入参数来验证调用过程。可以看到 4 次调用的结果分别是 1 3 2 4。

下面我们实现一个`debounce` 函数，它传入一个数值超时参数。

```
function debounce(timeout) {
  const instanceMap = new Map(); 
// Create a Map data structure with the instantiated object as the key

  return function (target, key, descriptor) {

    return Object.assign({}, descriptor, {
      value: function value() {

        clearTimeout(instanceMap.get(this));
        instanceMap.set(this, setTimeout(() => {
          descriptor.value.apply(this, arguments);
          instanceMap.set(this, null);
        }, timeout));
      }
    })
  }
}
```

在上述方法中，我们使用 ES6 提供的`Map` 数据结构来实现实例化对象和延迟层之间的映射。在函数内部，首先清除 delayer，然后设置延迟执行函数。这是实现去抖的常用方法。让我们测试一下`debounce` 装饰器。

```
class Editor {
  constructor() {
    this.content = '';
  }

  @debounce(500)  
  updateContent(content) {
    console.log(content);
    this.content = content;
  }
}

const editor1 = new Editor();
editor1.updateContent(1);
setTimeout(() => { editor1.updateContent(2); }, 400);

const editor2= new Editor();
editor2.updateContent(3);
setTimeout(() => { editor2.updateContent(4); }, 600);

//result： 3 2 4
```

上面调用了 4 次`updateContent` 方法，打印结果为`3 2 4`。`1`不打印是因为它在 400ms 内被反复调用，这符合我们对参数 500 的预期。

**2.3 @deprecate 实现警告提示**

在使用第三方库的过程中，我们时不时会遇到一些控制台的警告。这些警告用于提醒开发人员所调用的方法将在下一版本中被弃用。这样的一行打印信息可能是我们通常的做法，在方法内部加一行代码，实际上对源代码阅读并不友好，也不符合单一责任原则。在需要抛出警告来实现警告的方法前面添加一个@deprecate 装饰器会友好得多。

让我们实现一个@deprecate 装饰器。其实这种类型的 decorator 还可以扩展到打印日志 decorator @log，报表信息 decorator @fetchInfo 等。

```
function deprecate(deprecatedObj) {return function(target, key, descriptor) {
    const deprecatedInfo = deprecatedObj.info;
    const deprecatedUrl = deprecatedObj.url;
    // Warning message
    const txt = `DEPRECATION ${target.constructor.name}#${key}: ${deprecatedInfo}. ${deprecatedUrl ? 'See '+ deprecatedUrl + ' for more detail' : ''}`;

    return Object.assign({}, descriptor, {
      value: function value() {
        // print warning message
        console.warn(txt);
        descriptor.value.apply(this, arguments);
      }
    })
  }
}
```

上面的 deprecate 函数接受一个 object 参数，该参数有两个键值:`info` 和`url`，其中`info` 填充了警告信息，`url`是可选的详细信息网页地址。让我们将这个装饰器添加到名为`MyLib`的库的`deprecatedMethod` 方法中！

```
class MyLib {
  @deprecate({
    info: 'The methods will be deprecated in next version', 
    url: 'https://www.medium.com'
  })
  deprecatedMethod(txt) {
    console.log(txt)
  }
}const lib = new MyLib();
lib.deprecatedMethod('Called a method that will be removed in the next version'); 
```

## 3.摘要

通过`ESnext` 中的 decorator 实现 decorator 模式，不仅具有扩展类的功能的功能，还在读取源代码的过程中起到了提示的作用。上面提到的例子只是一个简单的封装，结合了装饰器和装饰器模式的新语法，不应该在生产环境中使用。如果您现在已经意识到了 decorator 模式的好处，并且希望在您的项目中大量使用它，那么请看一下`core-decorators`库，它封装了许多常用的 decorator。

关于设计模式的其他文章，看下面的文章，如果你对我的文章感兴趣，可以在 [Medium](https://hyhwell.medium.com/) 或者 [Twitter](https://twitter.com/Maxwell_hyh) 上关注我。

[](https://javascript.plainenglish.io/javascript-design-patterns-factory-pattern-4fa7914f7ff6) [## JavaScript 设计模式:工厂模式

### 工厂模式是什么？工厂模式是用于创建对象的最常见的设计模式之一。

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-design-patterns-factory-pattern-4fa7914f7ff6) [](/javascript-design-patterns-observer-pattern-1cf90cffb1e2) [## JavaScript 设计模式:观察者模式

### 观察者

图案 Observerlevelup.gitconnected.com](/javascript-design-patterns-observer-pattern-1cf90cffb1e2) [](/javascript-design-patterns-strategy-pattern-c013d3dbc059) [## JavaScript 设计模式:策略模式

### 学习设计模式的目的是代码的可重用性，使代码更容易被其他人理解，并且…

levelup.gitconnected.com](/javascript-design-patterns-strategy-pattern-c013d3dbc059) [](/javascript-design-patterns-singleton-pattern-7ada98be9a10) [## JavaScript 设计模式:单例模式

### Singleton 模式:将类实例化的次数限制为一次，一个类只有一个实例，并且…

levelup.gitconnected.com](/javascript-design-patterns-singleton-pattern-7ada98be9a10)