# UI，控制的组合和反转

> 原文：<https://levelup.gitconnected.com/ui-composition-and-inversion-of-control-d65e04eaddf9>

![](img/4327876004d874e9932c3629d5b8abe5.png)

图:[Rob Oo 的俄罗斯方块](https://www.flickr.com/photos/105105658@N03/40266973032/in/photolist-24mfLcJ-9SGsgT-56BHp8-oJrmRq-NWSncE-SZQVz5-4DXCaN-CV292-4tqFJb-2jbqXFF-5onNFP-6LaEqM-9dHrCP-2inDJdd-2jbqXCp-QYemba-66yfJr-5WBXST-fKBDq-4E6dEv-29Q5mD1-fKBSX-e6CAxw-4zrJhK-bomyHo-ed7h8g-6aLwnr-acdDBL-fKBDn-uwPrx-k24um-s2Zia-4rGX72-2gnaUcZ-6c6ZAL-8sA5U1-dKN3G-tjLE-6dW2nh-ca9oWL-tjN9-5Da8Z-eQ8a5-7xzNz1-4USC8j-991Mwh-991MvJ-KsTWg7-qupew-b1s2gg)

设计健壮的软件通常需要将复杂的问题分成更小更灵活的部分，然后将它们组合成一个连贯的整体。

在本文中，我们将通过一个用 web 组件构建的、呈现问候消息的示例来了解软件合成的不同方面:著名的“hello world”代码示例。

Web components specification 提供了一个低级的 API，并没有像流行的 UI 框架那样在各种抽象层后面隐藏太多的复杂性(这就是为什么您会使用这样的框架),因此这种技术非常适合本教程中的架构概念。
请记住，在本文的上下文中，web 组件技术只是一种理解这些概念本质的工具，并不强制要求您具备该技术的先验知识。

让我们先来看看下面的两个函数

```
const filterEvenNumbers = (numbers) => {
    const output = [];
    for (const number of numbers) {
        if (number % 2 === 0) {
            output.push(number);
        }
    }
    return output;
};const filterStringsWithE = (strings) => {
    const output = [];
    for (const string of strings) {
        if (string.includes('e')) {
            output.push(string);
        }
    }
    return output;
};
```

两者的工作方式相似，但仍有很大不同，并且依赖于完全不同的假设:一个对数字进行操作，而另一个对字符串进行操作。它们都遵循一种*命令式*风格，你可以很容易地理解为一系列基本指令。

尽管它们完成了工作，但您很快会发现它们并不十分灵活，因为它们将与数据结构和条件检查上的迭代相关的代码混合在一起。它阻止我们在两个功能之间共享任何逻辑。然而，我们可以很快让出现一个模式，特别是如果我们把它们改写成这样:

```
const filterEvenNumbers = (numbers) => {
    const output = [];
    const predicate = (number) => number % 2 === 0;
    for (const number of numbers) {
        if (predicate(number)) {
            output.push(number);
        }
    }
    return output;
};

const filterStringsWithE = (strings) => {
    const output = [];
    const predicate = (string) => string.includes('e');
    for (const string of strings) {
        if (predicate(string)) {
            output.push(string);
        }
    }
    return output;
};
```

现在我们可以将一个*模板*绘制成一个过滤操作符:

```
const filter = (predicate) => (items) => {
    const output = [];
    for (const item of items) {
        if (predicate(item)) {
            output.push(item);
        }
    }
    return output;
};
```

并编写我们的两个函数

```
const ***filterEvenNumbers*** = filter((number) => number % 2 === 0);
const ***filterStringsWithE*** = filter((string) => string.includes('e'));
```

我们的谓词变得完全独立于使用它们的上下文，而过滤操作符不需要对它将操作的数据结构的性质做任何假设(除了它们需要实现迭代器协议之外)。不知何故，我们可以把过滤操作符看作是一个有漏洞的过程，需要由调用者来填补。

这个原则通常被称为[控制反转](https://en.wikipedia.org/wiki/Inversion_of_control)，并且是许多设计模式的基础，例如[模板方法](https://en.wikipedia.org/wiki/Template_method_pattern)、[插件](https://en.wikipedia.org/wiki/Plug-in_(computing))、[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection)等

## 用户界面、数据获取和责任

现在让我们考虑以下 web 组件:

```
// component.js
import {createService} from './service.js';

export class Greetings extends ***HTMLElement*** {

    static get *observedAttributes*() {
        return ['name'];
    }

    get name() {
        return this.getAttribute('name');
    }

    set name(val) {
        this.setAttribute('name', val);
    }

    attributeChangedCallback() {
        this._render();
    }

    constructor() {
        super();
        this._fetch = createService();
    }

    async _render() {
        this.textContent = await this._fetch(this.name);
    }
}
```

对于不了解 web 组件的读者:
web 组件规范迫使我们通过扩展常规的 [HTMLElement 类](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement)来声明组件。借助静态 getter*observed attributes*，我们可以定义希望浏览器为我们监视哪些 HTML 属性；以及当它们的值由于 *attributeChangedCallback 而改变时该做什么(*这相当于在许多 UI 框架*中可以找到的 *reactivity/watch* 机制)。*在我们的例子中，我们调用一个定制的渲染函数，它依赖于组件在其构造函数中创建的数据获取服务。

这里的服务实现是一个细节，但是您可以想象类似于

```
// service.js
export const createService = (opts = {}) => async (name) => `Hello ${name || 'Mr. Nobody'}`;
```

(一个基本的异步函数，它将一个字符串作为参数，并返回一个格式化的问候消息)。

除了声明式 API(通过 HTML 属性)，我们还通过属性访问器(“name”)提供了编程式 API。

然而，我们不能自己调用构造函数(它将抛出一个错误)，必须通过将我们的自定义元素注册到一个全局注册表(这是规范的一部分)来将这个操作委托给浏览器:

```
// injector.js
export const define = (tag, klass) => ***customElements***.define(tag, klass);
```

这将允许浏览器简单地通过解析 HTML 文档中的标签来创建我们的定制组件的实例，或者作为任何常规的 HTML 元素，通过调用*document . createelement(tag)*。

```
<!DOCTYPE html>
<html lang="en">
<!-- ... ->
<body>
<app-greetings name="lorenzofox"></app-greetings>
<script type="module">
    import {define} from './injector.js';
    import {Greetings} from './component.js';
    define('app-greetings', Greetings);
</script>
</body>
</html>
```

您可以通过更改 *name* 属性或使用提供的开发工具环境，在下面的[代码沙箱](https://codesandbox.io/s/unruffled-flower-k8kmo?file=/index.html)中进行试验。

尽管这个例子工作得很好，但它远非完美:我们的组件实现与给定的 fetch 服务紧密耦合。例如，如果您希望隔离测试组件，这可能会很困难:服务可能需要进行一些网络调用，等等。为了抽象出服务实现，你需要*劫持*导入(用服务工作者、代理等)来提供模拟或类似的东西。 [Jest](https://jestjs.io/) 允许你通过全局模仿来做到这一点，但在我看来这是一种反模式，只是一种隐藏了你软件中更深层次问题的黑客行为。

编写测试本身并不是目的，但是如果你在测试你的一部分代码时遇到一些困难，这可能是一种[代码味道](https://en.wikipedia.org/wiki/Code_smell)，你的不同组件紧密耦合在一起。

假设需求已经改变，我们希望根据查询字符串参数显示不同的本地化消息。我们现在有各种服务:

```
// en.js
export const createService = (opts = {}) => async (name) => `Hello ${name}`;// fr.js
export const createService = (opts = {}) => async (name) => `Bonjour ${name}`;// es.js
export const createService = (opts = {}) => async (name) => `Hola ${name}`;etc;
```

最糟糕的情况可能是开发人员匆忙“解决”问题:

```
export class Greetings extends ***HTMLElement*** {
 // ... 
    constructor() {
        super();
        const query = ***window***.location.search;
        const lang = new ***URLSearchParams***(query).get('lang');
        switch (lang) {
            case 'fr':
                this._fetch = createFrService();
                break;
            case 'es':
                this._fetch = createEsService();
                break;
            default:
                this._fetch = createEnService();
        }
    }
// ... 
}
```

现在，我们的组件被耦合到几个实现和全局对象。构造函数带有相当多的逻辑，几乎不可能测试。我们可以通过为我们的服务引入一个间接点来改进代码库:一个函数( *createService* )根据一些参数返回正确的服务。但是，如果现在我们希望基于用户设置而不是查询参数来选择服务，那该怎么办呢？同样，这将要求我们更改组件代码。

## 注入依赖性

理想情况下，我们不希望组件(属于某种表示层)承担创建/配置服务的责任，这可能依赖于组件上下文之外的许多参数…并且无论如何属于某种业务层。

由于我们不能调用 web 组件的构造函数并依赖浏览器来创建组件的实例，这听起来很有挑战性，但事实并非如此。首先，我们仍然可以使用默认参数编写我们的构造函数来解决这个问题:

```
import {createService} from './service.js';

export class Greetings extends ***HTMLElement*** {
    //...
    constructor(service = createService()) {
        super();
        this._fetch = service;
    }
    //...
}
```

这样做的原因是，引擎会将传递的服务解析为 *createService* 函数的结果:我们已经将创建数据获取服务的逻辑移出了组件。

更好的是:如果我们稍微修改一下将组件注册到全局注册表中的代码，我们就可以传递任何服务:

```
// injector.js
import {createEnService, createEsService, createFrService} from './service.js';

const resolveService = () => {
    const search = ***window***.location.search;
    const lang = new ***URLSearchParams***(search).get('lang');
    switch (lang) {
        case 'fr':
            return createFrService();
        case 'es':
            return createEsService();
        default:
            return createEnService();
    }
}

export const define = (tag, klass) => {
    const service = resolveService();
    ***customElements***.define(tag, class extends klass{
        constructor() {
            super(service);
        }
    });
};
```

我们修饰了常规的 *customElements.define* 函数来传递一个组件，该组件在我们的组件中注入了依赖关系。现在组件完全独立于任何上下文，所以服务也是。如果需求发生变化，我们唯一需要修改的部分是 *resolveService* 函数！依赖关系代码的注入是唯一负责以“全知”方式解析适当实例的代码。

你可以在这里看到完整的[代码](https://codesandbox.io/s/unruffled-volhard-52bvv?file=/src/injector.js)

## 测试组件

我们现在可以轻松地将服务的任何实现(包括模仿)传递给组件，并完全隔离地测试它，而不是依赖于全局模仿攻击:

```
import stub from 'sbuts';
import {***test***} from 'zora';
import {flush, mountComponent} from './utils.js';
import {Greetings} from '../component.js';

***test***(`when component is mounted, it should not render anything if no name attribute is set`, async t => {
    // given
    const service = stub().resolve(`hello world`);

    // do
    const comp = mountComponent(Greetings, service);
    await flush();

    // expect
    t.eq(comp.textContent, '');
    t.notOk(service.called);
});

***test***(`when component is mounted, it should render the service message when the name attribute changes`, async t => {
    // given
    const service = stub().resolve(`hello world`);
    const attributeValue = 'lorenzofox';
    const comp = mountComponent(Greetings, service);

    // do
    comp.setAttribute('name', attributeValue)
    await flush();

    // expect
    t.eq(comp.textContent, 'hello world');
    t.eq(service.calls, [[attributeValue]], `service should have been called once with ${attributeValue}`);
});
```

为了记录:mountComponent 是一个测试工具函数，它基本上做我们的应用程序中的注入器所做的事情，而 flush 用于确保在我们做出断言之前任何未完成的承诺都被清空。
如果你想了解详情，可以看看下面的 [*代码沙箱*](https://codesandbox.io/s/inspiring-euler-zyoyh?file=/test/runner.html) *。*

这是一个好的测试吗？

是…也不是。这是一个很好的**单元**测试，因为它完全隔离地测试组件代码，抽象出服务代码，并确保无论服务实现是什么，都用正确的参数调用。但是，如果出于某种原因，您必须更改给定服务实现的接口

```
// from
export const createServiceA = (opts) => async (name) => `hello ${name}` // to
export const createServiceA = (opts) => async ({name}) => `hello ${name}`;
```

尽管您的应用程序被破坏了，您的测试将继续通过:测试没有捕捉到回归。但毕竟，捕捉依赖关系**接口**中的变化不是它的责任，因为它意味着只测试与 web 组件相关的代码单元。
要点是:当你想要松耦合和引入依赖注入之类的模式时，你必须通过**接口**和**抽象**类型连接不同的部分。在 Javascript 中，由于接口的概念不是内置的，所以不太明显，但是如果你在它上面添加一个类型系统(如 Typescript ),你的代码将无法编译，回归将被捕获。

然后，注射器的作用是修复这种差异。例如，您可以使用一个[适配器](https://en.wikipedia.org/wiki/Adapter_pattern):

```
const adapter = (fetch) => (name) => fetch({name});

const resolveService = () => {
    const lang = new ***URLSearchParams***(***window***.location.search);
    switch (lang) {
        case 'fr':
            // the service with a different interface
            return adapter(createFrService());
        case 'es':
            return createEsService();
        default:
            return createEnService();
    }
};
```

同样，不需要更改组件代码或服务代码:注射器将点连接在一起！

## 结论

通过这个基本的例子，我们已经看到了一组架构模式如何帮助创建一个健壮而灵活的软件，而不需要触及许多代码分支(if … else …等):我们通过**组合**来解决问题。