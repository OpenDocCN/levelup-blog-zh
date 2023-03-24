# 使用模块构建模块化的 JavaScript 应用程序

> 原文：<https://levelup.gitconnected.com/use-modules-to-build-a-modular-javascript-app-6aeaf82fa85d>

![](img/a201ec7487410f460ca1178d98059172.png)

弗洛里安·奥利佛在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

ES6 的一大特色是 JavaScript 支持内置模块。模块允许我们通过使用`export`和`import`语法在文件之间共享代码。与使用`script`标签和全局变量跨文件共享代码相比，这是一个很大的改进。

使用`script`标签容易出错，因为加载顺序很重要。脚本顺序错误会导致我们的程序执行尚未声明的代码。它还迫使我们编写没有真正结构或逻辑组合的意大利面条式代码。模块不存在这个问题，因为所有的东西都是在文件之间直接导出和导入的。此外，我们可以很容易地知道导入代码的定义，因为在哪个模块中被导入和引用是显式的。

# 出口和进口

为了使 JavaScript 文件中的代码可导入，我们必须用`export`语句显式导出它们。为此，我们只需将`export`放在您希望向其他文件公开的变量或常量前面。例如，我们可以写:

```
export let num = 1;
```

这将导出变量`num`,以便其他模块可以`import`使用它。

我们可以导出任何用`var`、`let`、`const`声明的东西，还有函数和类。导出的项目必须在顶层声明。`export`不能在其他地方使用，比如内部函数和类。

我们可以一次导出多个成员。我们所要做的就是将所有成员用逗号分隔的大括号括起来。例如，我们可以写:

```
const num1 = 1;
const num2 = 2;
const num3 = 3;
export {num1, num2, num3};
```

这将让我们在其他 JavaScript 文件中导入`num1`、`num2`和`num3`。

现在我们已经导出了成员，我们可以在其他 JavaScript 文件中导入它们。我们可以使用`import`语句将一个或多个成员导入到一个模块中，并使用它们。例如，如果我们在`moduleA.js`中有以下内容:

```
const num1 = 1;
const num2 = 2;
const num3 = 3;
export {num1, num2, num3};
```

然后在`moduleB.js`中，我们可以编写以下代码来从`moduleA.js`导入项目:

```
import {num1, num2, num3} from './moduleA'
```

`from`关键字后的路径以句点开始。句点表示我们在当前文件夹中。

这里假设`moduleA.js`和`moduleB.js`在同一个文件夹中。如果我们将它们放在不同的文件夹中，那么如果我们想要将导出的`moduleA.js`成员导入到`moduleB.js`中，我们需要指定`moduleA.js`相对于`moduleB.js`的路径。例如，如果`moduleA.js`比`moduleB.js`高一级，那么在`moduleB.js`中我们写:

```
import {num1, num2, num3} from '../moduleAFolder/moduleA'
```

路径前的 2 个句点意味着我们向上一级文件夹，然后得到`moduleAFolder`，然后得到`moduleA.js`。

我们也可以在`script`标签中使用 JavaScript 模块。为此，我们必须将脚本标签的`type`属性设置为`module`来使用它们。例如，如果我们想在 HTML 文件中使用`moduleA.js`，我们可以写:

```
<script type='module' src='moduleA.js'></script>
```

我们可以在 JavaScript 模块中使用`import`和`export`。它们不能与常规脚本一起工作。

脚本自动在严格模式下运行，所以我们不能意外声明全局变量，做其他不启用严格模式也能做的事情。它们还会自动异步加载，这样我们就不必担心长脚本会阻碍页面的加载。另外，`import`和`export`只发生在两个脚本之间，所以没有设置全局变量。因此，不能在控制台中直接查看它们。

# 默认导出

还有一个用于导出模块成员的默认导出选项。我们以前以通过名称导入变量的方式导出变量。还有默认的导出，它从模块中导出单个成员，而不需要在导入时通过名称显式引用它。例如，如果我们想要导出一个模块中的单个成员，我们可以在`moduleA.js`中编写以下内容:

```
const num = 1;
export default num;
```

当你使用`export default`的时候没有花括号。

然后在您想要导入成员的文件中。在`moduleB.js`中我们写道:

```
import num from './moduleA'
```

我们再次省略了花括号。这是因为每个模块只允许一个默认导出。或者，我们可以在`moduleB.js`中写下以下内容:

```
import {default as num} from './moduleA'
```

![](img/d57e050ac4b9467efaf8fa9d317e9eb0.png)

照片由 [Jen Theodore](https://unsplash.com/@jentheodore?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 重命名导入和导出

如果我们有许多模块，并且它们的导出成员具有相同的名称，那么如果我们试图导入多个模块，就会发生冲突。这将是我们需要解决的问题。幸运的是，JavaScript 有`as`关键字让我们重命名导出和导入，这样我们可以避免代码中的名称冲突。为了使用`as`关键字重命名导出，我们在`moduleA.js`中编写了以下内容:

```
export {
  num1 as numOne,
  num2 as numTwo
}
```

然后在`moduleB.js`中，我们可以通过编写以下内容来导入它们:

```
import { numOne, numTwo } from './moduleA'
```

或者，我们可以在导入时进行重命名。为此，在`moduleA.js`中，我们把:

```
export {
  num1,
  num2
}
```

然后在`moduleB.js`中，我们放入:

```
import { num1 as numOne, num2 as numTwo } from './moduleA'
```

如果我们试图导入模块中的成员，而模块中的成员具有相同的名称，例如:

```
import { num1, num2 } from './moduleA';
import { num1, num2 } from './moduleB';
import { num1, num2 } from './moduleC';
```

我们会看到我们得到了`SyntaxError`。因此，我们必须对它们进行重命名，以便模块能够运行:

```
import { num1 as num1A, num2 as num2A } from './moduleA';
import { num1 as num1B, num2 as num2B } from './moduleB';
import { num1 as num1C, num2 as num2C } from './moduleC';
```

从具有同名成员的多个模块中导入的一种更干净的方法是将模块的所有导出成员作为一个对象导入。我们可以用星号来表示。例如，代替:

```
import { num1 as num1A, num2 as num2A } from './moduleA';
import { num1 as num1B, num2 as num2B } from './moduleB';
import { num1 as num1C, num2 as num2C } from './moduleC';
```

我们可以改为写:

```
import * as moduleA from './moduleA';
import * as moduleB from './moduleB';
import * as moduleC from './moduleC';
```

然后在导入下面的代码中，我们可以写:

```
moduleA.num1;
moduleA.num2;
moduleB.num1;
moduleB.num2;
moduleC.num1;
moduleC.num2;
```

我们还可以导出和导入类。因此，如果我们有一个包含一个或多个类的文件，比如包含以下类的`Person.js`文件:

```
class Person {
  constructor(firstName, lastName) {
    this._firstName = firstName;
    this._lastName = lastName;
  }
  get fullName() {
    return `${this.firstName} ${this.lastName}`
  }
  get firstName() {
    return this._firstName
  }
  get lastName() {
    return this._lastName
  }
  sayHi() {
    return `Hi, ${this.firstName} ${this.lastName}`
  }
  set firstName(firstName) {
    this._firstName = firstName;
  }
  set lastName(lastName) {
    this._lastName = lastName;
  }
}
```

然后我们编写下面的代码来导出一个类:

```
export { Person };
```

这样导出了`Person`类，然后为了导入它，我们编写:

```
import { Person } from './person';
```

# 动态模块加载

JavaScript 模块可以动态加载。这让我们只在需要的时候加载模块，而不是在应用运行的时候加载所有的模块。为此，我们使用了返回承诺的`import()`函数。当参数中的模块被加载时，承诺就实现了。promise 解析为一个模块对象，然后可以被应用程序的代码使用。如果我们在`Person.js`中有`Person`类，那么我们可以用下面的代码动态导入它:

```
import('./Person')
.then((module)=>{
  const Person = module.Person;
  const person = new Person('Jane', 'Smith');
  person.sayHi();
})
```

或者使用`async`和`await`语法，我们可以把它放在一个函数中:

```
const importPerson = async ()=>{ 
  const module = await import('./Person');
  const Person = module.Person;
  const person = new Person('Jane', 'Smith');
  person.sayHi();
}importPerson();
```

如您所见，JavaScript 模块对于组织代码非常有用。它允许我们导出想要向其他模块公开的内容，消除了对全局变量的需求。此外，可以重命名导出和导入，以避免在导入多个模块时发生冲突。此外，所有导出的成员可以一次全部导入，这样我们就可以将整个模块作为一个对象，而不是导入单个成员。最后，如果我们只想从模块中导出一个东西，我们可以使用`export default`。