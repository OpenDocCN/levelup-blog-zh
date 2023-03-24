# 理解 Javascript 中的装饰器

> 原文：<https://levelup.gitconnected.com/understanding-decorators-in-javascript-912a05d7a537>

![](img/214ceeeaaea08eed19ca77b0b43e001d.png)

照片由 [Ferenc Almasi](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄，与装修工无关

Javascript 有很多特性。它实际上有很多，以至于我们容易忘记其中的一些。

今天我们将谈论**装饰者**，这是 TC39 流程(草案)第二阶段的一个特性。阶段 2 意味着语法是确定的，但它正等待在 ECMAScript 标准中实现。这也意味着该特性在标准 javascript 中不可用。
但是多亏了 [Babel](https://babeljs.io/) ，我们现在可以使用它并将其转换成标准的 [ECMAScript](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 代码！

# 什么是室内设计师

装饰器是类的方法的包装器。通过应用它，它将为这些方法添加更多的特性，并允许以更可读的方式重用外部函数。

对于语法本身，它非常类似于 Java 中的注释:

```
import { log } from './decorators';export default class Dog {

  @log
  bark(){
    console.log('Woof');
  } say(toSay){
    console.log(toSay);
  }}
```

在这段代码中，我们决定使用在文件`decorators.js`中实现的装饰器`log`

然后，每次调用函数`bark`时，另一个函数将执行方法本身(我将在下面展示更多细节)。

然而，方法`say`不会使用装饰器，因为它没有链接到它。
要在所有方法上使用`log`装饰器而不必在每个方法上设置它，它可以应用于类声明本身:

```
import { log } from './decorators';@log
export default class Dog {

  bark(){
    console.log('Woof');
  }say(toSay){
    console.log(toSay);
  }}
```

像这样，每次调用现有的和未来的方法都会调用装饰器`log`。

# 练习时间到了

让我们首先设置我们的项目。正如我所说的，原生 javascript 还不支持装饰器。所以我们必须将它包含在我们的项目中。

首先，让我们创建 javascript 环境。创建一个新文件夹，移入并运行

```
npm init
```

默认情况下，您可以保留 CLI 询问的所有信息。

完成后，在`devDependencies`中安装巴别塔的核心包:

```
npm install --save-dev @babel/core @babel/cli @babel/preset-env @babel/node
```

还因为我们需要多次刷新我们的更改，所以让我们安装 Nodemon，它是一个节点包，可以检测我们代码中的更改，并为我们重新启动服务器:

```
npm install --save-dev nodemon
```

为了工作，babel 需要一个配置文件。创建一个名为`.babelrc`的文件，并在其中粘贴以下内容

```
{
   "presets": [
     "@babel/preset-env"
   ]
}
```

这将告诉 Babel 解析标准的 ECMAScript Javascript，因为 NodeJS 还不兼容它。它将提供本机没有的功能。

现在，将以下脚本添加到您的 package.json 的`scripts`对象中，以运行 dev 服务器

```
"**start**": "nodemon *--exec babel-node index.js"*
```

我们的项目现在已经准备好使用 node 运行 ES6+ javascript，并且拥有一个使用 Nodemon 的开发服务器。

现在让我们为代码创建文件。如果您在运行`npm init`命令时没有改变条目文件的默认选项，那么您的条目文件应该是`index.js`。

创建此文件，并将以下内容粘贴到其中:

```
(
  function() {
    console.log('Hello World');
  }()
)
```

这段代码可能使用了你从未见过的语法。通过像这样将函数放在括号内，当文件被导入时，函数将被调用。这里，该文件是 Nodemon 的入口文件，这意味着它是主函数。

试试运行`npm start`，看看你的控制台。你应该有一个`Hello World`出现！

然后，在一个`src`文件夹中，创建文件`Dog.js`，并在其中粘贴以下内容:

```
export default class Dog {

  bark() {
    console.log('woof');
  }

  say(toSay) {
    console.log(toSay)
    return 'The dog said ' + toSay
  }
}
```

这个类`Dog`是一个非常基础的类，显示一些输出。第一个函数只显示`woof`，第二个函数显示通过参数传递的内容。

在`index.js`中，将 console.log 替换为:

```
const dog = new Dog()
dog.bark();
console.log('\n\n')
dog.say('hello wo.. Woof!');
```

不要忘记将您的`Dog.js`文件放入您的`index.js`中:

```
//index.js
import Dog from "./src/Dog";
```

您可以尝试运行您的程序，并通过运行`npm start`来查看输出。

现在我们将创建一个装饰器。我们将要创建的装饰器是一个日志装饰器，它将显示传递给方法的参数及其执行结果。

首先，我们在`src`文件夹下创建一个`decorators.js`文件，粘贴以下内容:

```
export function log(target) {
  if (typeof target.descriptor.value === 'function') {
    const original = target.descriptor.value;
    target.descriptor.value = function(...args) {
      console.log('arguments: ', args);
      const result = original.apply(this, args);
      console.log('result: ', result);
      return result;
    }
  }
  return target;
}
```

让我们进一步看看这段代码。根据设计，装饰者接受一个参数`target`。`target`是一个对象，它可以有两个定义——类和方法。

## 班级

```
{
  kind: 'class',
  elements: [
    Object [Descriptor] {
      kind: 'method',
      key: 'bark',
      placement: 'prototype',
      descriptor: [Object]
    },
    Object [Descriptor] {
      kind: 'method',
      key: 'say',
      placement: 'prototype',
      descriptor: [Object]
    }
  ]
}
```

`kind`:定义的类型。这里是`class`,这意味着装饰器已经放在了类原型上。

它包含一个带有`method`定义的元素数组。

## 方法

```
{
  kind: 'method',
  key: 'say',
  placement: 'prototype',
  descriptor: {
    value: [Function: say],
    writable: true,
    configurable: true,
    enumerable: false
  }
}
```

`kind`:这里是`method`，表示装饰器放在方法原型上。如果类型是`class`,它也可以是元素数组中的方法。记住，这意味着你在方法层。

`key`:这是方法的名称。这里是`say`。

`placement`:可以是`prototype`是把装饰器放在一个方法的原型上。如果方法是静态的，也可以是`static`。

`descriptor`:这个对象包含了所有与被执行方法相关的信息。

`descriptor.value`:由装饰者执行的功能。

`descriptor.writable`:该值与其他 babel 插件的高级用法有关，与本文无关。

`descriptor.configurable`:该值与其他 babel 插件的高级用法有关，与本文无关。

`descriptor.enumerable`:该值与其他 babel 插件的高级用法有关，与本文无关。

既然已经详细描述了目标对象，让我们回到我们之前的地方。

```
export function log(target) {
  if (typeof target.descriptor.value === 'function') {
    const original = target.descriptor.value;
    target.descriptor.value = function(...args) {
      console.log('arguments: ', args);
      const result = original.apply(this, args);
      console.log('result: ', result);
      return result;
    }
  }
  return target;
}
```

为了捕捉该方法并在其周围包含更多的逻辑，我们首先需要在目标中覆盖它。为了长期的目的，让我们检查描述符的值是否是一个函数。如果不是(例如，如果使用正确的巴别塔插件，类属性)，那么我们不想在这里应用任何覆盖。

```
const original = target.descriptor.value;
```

我们希望保留原始值，因为它仍然是我们想要执行的方法。我们把它保存在`original`变量下。

```
target.descriptor.value = function(...args) {
```

然后我们用一个新函数覆盖这个值。
出于打印目的，我们使用 spreading 获取参数。

```
console.log('arguments: ', args);
```

然后我们可以打印这些参数

```
const result = original.apply(this, args);
```

这一点很有趣。通过只执行原始函数，它将在一个新的作用域中执行，而不是在类 1 中。你可能听说过`bind(this)`。这里的原理是一样的，把这个权利赋予这个方法并执行它。

```
console.log('result: ', result);
return result;
```

然后我们可以打印结果并返回。

干得好！我们现在可以将装饰器应用到我们的方法中。您的`Dog.js`文件现在应该是这样的。

```
import { log } from "./decorators";

export default class Dog {

  @log
  bark() {
    console.log('woof');
  }

  @log
  say(toSay) {
    console.log(toSay)
    return 'The dog said ' + toSay
  }
}
```

如果你运行你的程序，你会得到一个可怕的错误信息！别担心，这很正常。现在，我们的巴别还不能理解这一点。让我们给它知识吧！

停止您的开发环境并运行命令:

```
npm install --save-dev @babel/plugin-proposal-decorators
```

这将安装 babel 插件来支持装饰者。

我们还需要修改我们的`.babelrc`文件来告诉 babel 使用这个插件。您的文件现在应该是这样的:

```
{
  "presets": [
    "@babel/preset-env"
  ],
  "plugins": [
    ["@babel/plugin-proposal-decorators", {
      "decoratorsBeforeExport": true
    }]
  ]
}
```

我们添加了一个`plugins`部分，它是一个数组，然后我们在里面添加了相应的插件。我们必须实现这些选项，并将`decoratorsBeforeExport`设置为 true，以支持装饰者的阶段 2。

现在，您可以再次运行您的程序，这一次，您将得到以下输出

```
arguments:  []
woof
result:  undefinedarguments:  [ 'hello wo.. Woof!' ]
hello wo.. Woof!
result:  The dog said hello wo.. Woof!
```

我们的装饰器正在工作，现在您可以看到额外的输出！

现在还有最后一件事要做。如果您尝试在类级别应用装饰器，它不会给你一个错误。这是因为我们只在方法层次上处理，而不是在类层次上(记住`kind`)。

所以让我们回到装饰者那里，把它改成:

```
function doSingleLog(methodTypeTarget) {
  if (typeof methodTypeTarget.descriptor.value === 'function') {
    const original = methodTypeTarget.descriptor.value;
    methodTypeTarget.descriptor.value = function(...args) {
      console.log('arguments: ', args);
      const result = original.apply(this, args);
      console.log('result: ', result);
      return result;
    }
  }
  return methodTypeTarget;
}

export function log(target) {
  if (target.kind === 'method') {
    return doSingleLog(target)
  } else if (target.kind === 'class') {
    target.elements = target.elements.map((element) => doSingleLog(element))
  }
  return target;
}
```

这里，我们仍然有`log`函数，但是这一次，我们将逻辑导出到一个`doSingleLog`函数中，它将只处理方法级别，而`log`函数将完成分配正确元素的工作。

现在，您可以在类级别应用`@log`并将其从方法中移除。并且输出将保持不变！

装饰者可以用在很多不同的情况下，只有你的想象力可以成为你的极限。这个日志示例是您拥有的大量可能性之一！

我希望你达到了它的结尾，并在未来使用装饰。我认为 decorators 是考虑使用 Javascript 的 OOP 的另一个原因！你可以在我的 github repo [这里](https://github.com/psyycker/decorator-test)找到最终代码。

感谢您的阅读！