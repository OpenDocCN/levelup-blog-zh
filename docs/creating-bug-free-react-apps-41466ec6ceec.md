# 创建 bug🕷free 反应应用程序

> 原文：<https://levelup.gitconnected.com/creating-bug-free-react-apps-41466ec6ceec>

## 一个好的策略可以消除几类错误

![](img/ecc3e52c948b886fe166b2c53848e962.png)

图片由[安妮特·梅尔](https://pixabay.com/users/nennieinszweidrei-10084616/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=4517960)从[皮克斯拜](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=4517960)拍摄

根据弗雷德里克·布鲁克斯的论文*神话中的人月*，我同意开发者是乐观主义者。我们大多数人都倾向于关注某个功能的幸福之路，在热重装、VSCode 和 Chrome 开发工具的温暖中工作，相信 API 总是工作，并假设如果有任何错误，QA 会发现它。但愿如此。

在本文中，我将尝试介绍一些实用的步骤，我们可以采取这些步骤来消除大型 JavaScript 应用程序中的某些类型的错误。有些可能看起来简单明了，有些有点过度设计，或者有争议，但这些都是来自经验，而不仅仅是猜测。

# 🕷不正确的函数参数

JavaScript 是一种动态类型语言，在函数调用方面非常宽容。无论函数是如何定义的，你都可以调用一个没有参数或者有额外参数的函数。在一个小范围内，错误的使用可能看起来不是一个大问题，但是随着应用程序的增长，它的可重用功能和组件也会增长。随着时间的推移，调用带有不正确参数(对象而不是数组等)、没有参数、参数顺序错误的函数的可能性会增加。再多的单元测试也无法真正捕捉到所有这些，因为有些测试只会在运行时出现。

## 提示:使用 TypeScript

在我看来，TypeScript 最大的好处在于它能够自我记录代码和意图，并且它与 VSCode 的集成非常棒。即使没有特定的类型，TypeScript 也会通过类型和签名推断提供足够的保护。但是有一个警告:在将静态类型应用于动态类型语言(如 JavaScript)时，肯定会有一些冲突。需要有一个有效的类型脚本策略。以下文章可能会有所帮助:

[](https://rajeshnaroth.medium.com/how-i-learned-to-stop-worrying-and-love-typescript-ccda0f96801a) [## 我是如何学会停止担忧并爱上打字稿的

### 对于反应开发者来说，这并没有你想象的那么难

rajeshnaroth.medium.com](https://rajeshnaroth.medium.com/how-i-learned-to-stop-worrying-and-love-typescript-ccda0f96801a) 

# 🕷 JavaScript 坏的部分

JavaScript 是一种多范式语言，添加了 OOP 结构作为语法糖。“this”是一个有缺陷且令人困惑的关键字，构造函数和*新的*关键字也是如此。正确理解原型以及它们如何在类的构造中工作需要付出努力。没有完全理解的特性将导致有缺陷的不合格代码。

## 提示:选择使用 JavaScript 的精简版本

这里有一篇文章可以帮助你采用一种精简的 JavaScript 风格:

[](https://medium.com/swlh/trimming-javascript-for-better-dx-f043a9b01591) [## 微调 JavaScript 以获得更好的 DX

### 在 UI 开发中拥抱极简主义

medium.com](https://medium.com/swlh/trimming-javascript-for-better-dx-f043a9b01591) 

# 🕷意外突变

命令式代码(while，for，if/else)充斥着可变状态，因此成为了 bug 的温床。

## 提示:使用不可变状态

对于简单的数据类型，使用 ***const。*** *让*应该是个例外，千万别用 *var* 。这篇文章应该对此有更清晰的阐述:

[](https://rajeshnaroth.medium.com/es6-let-vs-const-304ee34114ef) [## ES6 — let vs const

### 目前公认的最佳实践是永远不要使用 var。从 const 开始，如果你必须重新分配变量…

rajeshnaroth.medium.com](https://rajeshnaroth.medium.com/es6-let-vs-const-304ee34114ef) 

**数组和对象**

数组和对象即使被定义为一个**常量**也可以变异。Object.freeze()不能处理嵌套的 JSON 对象。TypeScript 来拯救我们:

```
// Arraysconst list: ReadonlyArray<number> = [ 1, 2, 3];
// list[2] = 10; // is not allowed// Objectsinterface IPoint {
  x: number;
  y: number;
}const point: Readonly<IPoint> = { x: 3, y: 4 };
// point.x = 10; // is not possible
```

试试这里:[https://codesandbox.io/s/immutable-state-ujsc3?file=/src/App.tsx](https://codesandbox.io/s/immutable-state-ujsc3?file=/src/App.tsx)

## 提示:尽可能使用纯函数。

养成尽可能编写纯函数的习惯。纯函数没有状态突变，非常容易进行单元测试。

## 提示:写小函数。

大型函数是一种代码味道:它们做得太多了，甚至当它们自身之外没有突变时，它们可能会跟踪一大组容易出现意外错误的状态变量。通过练习，你将能够弄清楚如何将一个大的功能分成“做一件事”的更小的单元。尽可能保持小函数的纯净。

## 提示:停止使用类

停止使用类组件。在我们 HPE 的团队中，我们最近用零类组件完成了两个大型应用程序。太棒了，我们什么都没错过。

OOP 与函数式编程超出了本文的范围。就我个人而言，在编写了数据和函数之间界限清晰的代码之后，很难再回到什么都是类的 OOP。也许这篇文章会改变你的想法。

[](https://medium.com/@cscalfani/goodbye-object-oriented-programming-a59cda4c0e53) [## 再见，面向对象编程

### 我已经用面向对象语言编程几十年了。我用的第一个 OO 语言是 C++然后是 Smalltalk…

medium.com](https://medium.com/@cscalfani/goodbye-object-oriented-programming-a59cda4c0e53) 

## 提示:避免命令性循环

函数式编程工具，如 map、filter、reduce、compose 等，都是经过时间考验的结构，我们可以用它们来代替命令式迭代。因此，最小化或完全消除 while、for、forEach 等的使用，将对抑制这类错误大有帮助。我不记得上次在 JavaScript 中使用 while 或 for 循环是什么时候了。这里有一篇文章更深入地探讨了这个话题。

[](https://rajeshnaroth.medium.com/writing-resilient-unbreakable-code-using-functional-patterns-bcd63d28ac1e) [## 使用功能模式编写有弹性的、牢不可破的代码

### 应用程序的质量取决于其单元的质量

rajeshnaroth.medium.com](https://rajeshnaroth.medium.com/writing-resilient-unbreakable-code-using-functional-patterns-bcd63d28ac1e) 

# 🕷可怕的“无法读取未定义的属性”

当您试图访问一个尚未初始化的对象的属性时，例如，当一个 api 没有返回视图所期望的内容时，就会发生这种情况。因此，我们被建议写这样令人厌恶的:

```
const firstName = model && model.address && model.address.name || model.address.name.first || "";
```

或者，我们使用来自库的抽象，如 [lodash](https://lodash.com/) 或 [ramda](https://ramdajs.com/) 。

## 提示:使用？。可选链运算符-默认情况下

在我看来，在所有新的 JavaScript 特性中，可选的链接操作符是我的首选。它通过简化错误检查表达式来降低代码密度。前面的示例现在变成了:

```
const firstName =  model?.address?.name?.first || "";
```

我们的 UI 团队如此虔诚地遵循这一点，它成为了代码评审期间被调用的主要事情之一。尽管很少，我们甚至使用可选的链接进行流控制:

```
if (onClose) {
  onClose();
}
// can be substituted with:
onClose?.();
```

点击此处了解有关可选链接的更多信息:

 [## JavaScript 可选链接？。

### 它搔着巨大的痒处

rajeshnaroth.medium.com](https://rajeshnaroth.medium.com/javascript-optional-chaining-bb0a3344e5cc) 

## 提示:使用函数参数默认值。

除非您的逻辑明确需要一个 null/undefined 值，否则没有理由不初始化任何状态。使用 const 可以帮助您减轻这种情况，const 定义总是被初始化。但是，向函数传递一个未定义的值并不会被自动阻止。[功能参数默认值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters)在用未定义的值调用时将替换提供的默认值。

# 🕷运行时数据错误

REST 仍然是大量 web 应用程序事实上的 API 协议。REST 响应通常是复杂 JSON 结构的数组，它们会随着时间的推移而演变——UI 和 REST API 不同步的风险总是存在。直接在 React 视图中使用 API 结果是非常常见的，我们大多数人甚至都不会对此多想。然而，在工作过一些大型企业应用程序之后，我开始意识到许多错误都是因为这个原因而发生的。API 数据是可信的，但应该总是被验证。将视图直接与 API 响应紧密耦合是有风险的。

## 提示:将视图与 API 隔离开来

创建一个模型**接口**(使用 TypeScript)来表示**视图。与 API 数据不同，它包含的信息刚好够视图绑定到——一个简单的 JSON 对象，通常很少嵌套。创建一个模型**适配器**，一个转换 API 数据的纯转换函数。**

看看这个 [codesandbox](https://codesandbox.io/s/react-model-adapters-1-sg33j) React 例子。

在本例中，我们显示了一组地址。API 返回:

```
[
   {
      "id":0,
      "firstName":"Mathilde",
      "lastName":"Wilderman",
      "street":"697 Hudson Stream",
      "city":"Lake Ayanaport",
      "state":"North Dakota",
      "zipcode":"80363-3093",
      "status":"unread"
   },
   {
      "id":1,
      "firstName":"Anjali",
      "lastName":"Renner",
      "street":"204 Myra Keys",
      "city":"East Amparo",
      "state":"Massachusetts",
      "zipcode":"66131",
      "status":"marked"
   }
]
```

像这样直接在视图中使用这些值是很诱人的:

```
 <div >
        <span> Status: {status === "marked" ? "marked" : "unread"}</span>
        <div>
          <div className="name">{`$firstName $lastName`}</div>
          <div className="street">{street}</div>
          <div className="street">{city}</div>
          <div className="street">
            {state} {zipcode}
          </div>
        </div>
      </div>
```

但是正如您所看到的，api 以多种格式返回一些属性，比如邮政编码。名称和状态字段也需要修改。在视图中填充所有的逻辑会造成混乱，并且对单元测试不友好。请考虑改用此转换函数。

```
const **toSimpleZip** = (zipcode: string) => (zipcode?.split("-") || [])?.[0] || "n/a";const **getIcon** = (status: string) => {
  const DEFAULT = "email";
  const statusMap: object = {
    unread: "mark_email_unread",
    marked: "mark_email_read",
    read: DEFAULT
  };
  return statusMap[`${status}`] || DEFAULT;
};const **apiToModel** = (address) => ({
  name: getFullName(address.firstName, address.lastName),
  street: address.street || "",
  city: address.city || "",
  state: address.state || "",
  statusIcon: getIcon(address.status),
  zipcode: toSimpleZip(address.zipcode)
});
```

创建这样的适配器有一定的好处:

*   视图现在很简单，[可以很容易地进行单元测试](https://codesandbox.io/s/react-model-adapters-1-sg33j?file=/src/address/__tests__/Address.test.tsx)。
*   如果 API 无法提供回退值，适配器可以提供回退值。UI 中不再有难看的“未定义”或“空”字符串。
*   适配器及其相关的转换功能[可以针对正负条件进行简单的单元测试](https://codesandbox.io/s/react-model-adapters-1-sg33j?file=/src/address/__tests__/adapter.tests.ts)。
*   对 API 提供什么和视图需要什么有一个清晰的定义，会导致清晰的类型接口。这导致代码自我文档化。

## 提示:将表单与 API 隔离开来

UI 最耗时也是最困难的部分之一是编写好的表单。许多错误源于表单模型和与之交互的 API 之间的不匹配。表单不一定要模拟 API 数据的确切形状，当 API 嵌套时，表单的模型通常是平面的。因此，使用适配器将数据转换成表单有助于减少大量错误。我已经在另一篇关于 Formik 的博客中讨论过这个问题。

[](https://rajeshnaroth.medium.com/formik-forms-and-api-integration-7ebaa029d658) [## Formik 表单和 API 集成

### 在表单和 API 之间使用数据转换器

rajeshnaroth.medium.com](https://rajeshnaroth.medium.com/formik-forms-and-api-integration-7ebaa029d658) 

# 试验

这个话题已经被反复讨论了几十年，但是由于许多合理的原因，测试在许多项目中处于次要地位，成为技术债务。随着软件的老化和所有权的变更，测试变得更加重要。详细讨论超出了本文的范围，但是我们在团队中遵循以下原则:

*   尽可能写纯函数，它们容易测试。
*   如果一个逻辑可以通过移动到它自己的函数中来进行单元测试，那么它就应该是单元测试的。
*   单元测试应该有正面和负面的测试用例。如果适用，应提供空检查和默认行为。
*   为不能进行单元测试的部分编写验收测试。
*   使用代码覆盖工具来揭示缺失的测试区域。

希望这些提示对你有用。在你的团队中有没有帮助你减少 bug 密度的约定？请在评论中让我知道。