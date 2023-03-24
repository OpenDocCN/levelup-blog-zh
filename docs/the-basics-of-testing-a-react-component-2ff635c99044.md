# 测试 React 组件的基础

> 原文：<https://levelup.gitconnected.com/the-basics-of-testing-a-react-component-2ff635c99044>

## 使用 Jest 和酶测试反应中的受控形式

![](img/d2a3a123efe4fa9b9ee28e02fd41eb75.png)

测试前端是出了名的痛苦。然而，一旦你掌握了基本的诀窍，如果不是很费时间的话，很多事情都会变得容易和标准化。所以，今天我想带大家了解一下在 React 中完全测试一个受控表单的过程。

受控形式是反应的核心。它们通常包含一些输入、一个按钮和支持它们的逻辑。任何在 React 中做大量工作的人都可能经常创建这些组件，所以了解如何测试它们是很重要的。以下是标准控制表单的外观:

```
import React, { Component } from "react";class ControlledForm extends Component {
  constructor() {
    super(); this.state = {
      title: "",
      description: "",
      submitActive: false
    };
  } handleChange = event => {
    this.setState({
      [event.target.name]: event.target.value
    },this.checkFields);
  }; handleSubmit = event => {
    event.preventDefault();

    if (!this.state.submitActive) {
      return
    } const submission = {
      title: this.state.title,
      description: this.state.description
    }; this.props.submit(submission); this.setState({
      title: '',
      description: '',
      submitActive: 'false'
    })
  }; checkFields = () => {
    if (this.state.title && this.state.description) {
      this.setState({submitActive: true});
    } else {
      this.setState({submitActive: false});
    }
  }; render() {
    return (
      <form 
        className="controlled-form"
        onSubmit={event => this.handleSubmit(event)}
      >
        <input 
          className="text-input title-input"
          type="text"
          name="title"
          value={this.state.title}
          onChange={event => {
            this.handleChange(event);  
          }}
        />
        <input 
          className="text-input description-input"
          type="text"
          name="description"
          value={this.state.description}
          onChange={event => {
            this.handleChange(event);  
          }}
        />
        <button 
          className={`submit-btn ${this.state.submitActive}`}
        >submit</button>
      </form>
    );
  }
}export default ControlledForm;
```

有了这个特殊的控制表单，我们有两个输入:标题和描述。它们都有调用`handleChange()`方法的`onChange`监听器。输入值也绑定到它们在`state`中的相应属性，这些属性由`handleChange()`方法修改。

我们还有一个调用`handleSubmit()`方法的`onSubmit`监听器。该方法首先调用`preventDefault()`来阻止页面刷新，检查`submitActive`是否是`state`中的`true`，创建我们将向上传递组件树的对象，并调用 props 中传递的`submit()`方法。

最后，我们有`checkFields`方法，用于将`submitActive`设置为`true`或`false`。虽然不是必需的，但它经常用于样式设计，因为我们将`submitActive`插入到按钮元素的`className`中。

对于那些学习 React 的人来说，有一些坚实的工具可供你以 [Jest](https://jestjs.io/) 和 [Enzyme](https://github.com/airbnb/enzyme) 的形式编写测试。Jest 预装了`create-react-app`，所以大多数人通常不需要担心安装它。然而，酶不会。

如果您在 React 16 中，您可以使用 npm 从命令行安装 Enzyme:

```
npm i --save-dev enzyme enzyme-adapter-react-16
```

这将安装 React 16 所需的酶和适配器。如果没有与您的 React 版本匹配的适配器，Enzyme 将无法工作。

接下来，在与 ControlledForm 组件相同的目录中创建一个测试文件，并将其命名为`ControlledForm.test.js`。当您运行测试套件时，Jest/Enzyme 会自动识别这个文件。

现在我们需要设置我们已经创建的测试文件。您可能希望从文件顶部的以下内容开始:

```
import React from "react";import { shallow } from "enzyme";import ControlledForm from "./ControlledForm";import { configure } from "enzyme";
import Adapter from "enzyme-adapter-react-16";
configure({ adapter: new Adapter() });
```

我们正在使用 React 组件，所以显然我们需要导入 React。第二行代码从 Enzyme 导入了`shallow()`方法，这允许我们创建组件的“浅层”实例。这意味着在我们测试过的组件中创建的任何子组件将只作为引用存在。因为我们不处理 ControlledForm 的任何子组件，所以这在这里一点也不重要。

接下来，我们导入组件进行测试。不言自明。

以下三行用于配置酶以与 React 16 一起工作。这段代码通常放在 setupTests.js 文件你的`/src`文件夹中，然后全局应用于所有测试，但是为了本教程，我们只是将它放在这个特定测试文件的顶部。

好了，现在我们准备写一些测试！

我们将从我们的`ControlledForm`组件的`describe`方法开始，声明我们的包装器，作为道具传递的`submit`函数的模拟，以及定义`mockSubmit`和`wrapper`的`beforeEach`:

```
describe("ControlledForm", () => {
  let wrapper;
  let mockSubmit; beforeEach(() => {
    mockSubmit = jest.fn();
    wrapper = shallow(<ControlledForm submit={mockSubmit} />);
  });
```

通过将 mockSubmit 定义为`jest.fn()`，它允许识别它何时被调用以及使用什么参数。当我们测试调用它的`handleSubmit()`方法时，这很重要。

**快照测试**

我们要编写的第一个测试将是一个简单的快照测试。快照就是它听起来的样子；它会给你的组件拍一张“照片”,然后观察它会呈现什么。这张图片作为组件的 JSON 对象表示保存在您的测试文件旁边，并与您随后每次对该文件运行测试时呈现的内容进行比较。

```
it("should match the snapshot", () => {
  expect(wrapper).toMatchSnapshot();
});
```

如果你改变了一些被渲染的东西，快照将会失败，你的测试套件会让你知道。快照的全部意义仅仅是让你知道一些东西随着被渲染的东西而改变。这在很大程度上是为了警告您，是否有流氓代码以某种方式被引入到您的 JSX 中，就像您的猫在您不注意的时候走过键盘一样。

**测试状态变化**

接下来我们将测试`handleChange()`方法。这个方法做两件事:它设置状态，并在我们的`setState()`调用中调用`checkFields`作为回调。它有一个参数:带有名称和值属性的事件对象，我们需要为测试模拟它。

```
describe("handleChange", () => {
  it("should call setState on title", () => {
    const mockEvent = {
      target: {
        name: "title",
        value: "test"
      }
    }; const expected = {
      title: "test",
      description: "",
      submitActive: false
    }; wrapper.instance().handleChange(mockEvent);

    expect(wrapper.state()).toEqual(expected);
  });
});
```

在我们模拟了事件对象之后，我们创建了一个对象`expected`，我们将用它来比较调用`handleChange()`后的状态变化。接下来，我们使用包装器上的`instance()`方法调用该方法，这使我们能够访问我们在前面的`beforeEach()`中实例化的组件实例上的所有方法。然后我们简单地将状态中的内容与我们的`expected`对象进行比较。

状态变化是 React 中经常要测试的东西。这里我要给出一个警告:当你编写方法时，不要在一个函数中对任何一个属性进行多种状态改变，因为这样做将使得不可能检查单个属性的改变是否发生了。例如，如果您正在设置状态，以便在进行 fetch 调用时有条件地呈现一个加载屏幕，然后在 fetch 调用完成后再改回来，那么您将希望在另一个函数中进行其中一个状态更改，以便可以单独测试它们。

**使用 jest.spyOn**

`handleChange()`做的第二件事是在我们的`setState()`中调用`checkFields()`作为回调，所以这需要额外测试。我们通过使用`jest.spyOn()`来做到这一点。

```
it("should call checkFields", () => {
  const spy = jest.spyOn(wrapper.instance(), "checkFields");

  wrapper.instance().forceUpdate(); const mockEvent = {
    target: {
      name: "description",
      value: "test"
    }
  }; const expected = true; wrapper.instance().handleChange(mockEvent); expect(spy).toHaveBeenCalled();
});
```

我们将声明一个变量`spy`并将它赋给我们的`spyOn()`方法。`spyOn()`方法通常接受两个参数:我们组件的实例，以及我们作为字符串观察的方法。在分配了`spy`之后，我们需要在我们的实例上调用`forceUpdate()`，以便能够正确地观察它。没有它，这个测试将失败。

接下来，我们像前面的测试一样声明我们的模拟事件，这样函数就不会出错，声明我们期望的变量并调用`handleChange()`。为了测试是否调用了`checkFields()`，我们使用了`toHaveBeenCalled()`方法。

在`handleChange()`上还有一件事要测试，那就是确保它通过事件处理被正确调用，特别是两个相关输入的任何变化。

```
it("should call handleChange on description change with the correct params", () => {
  const spy = jest.spyOn(wrapper.instance(), "handleChange");
  wrapper.instance().forceUpdate(); const mockEvent = {
    target: {
      name: "description",
      value: "test"
    }
  }; wrapper.find(".description-input").simulate("change", mockEvent); expect(spy).toHaveBeenCalledWith(mockEvent);
});
```

这个测试与前一个有很多共同之处，除了这次我们把间谍放在了`handleChange()`方法上。接下来，我们需要在 DOM 上模拟一个`change`事件，通过利用描述输入的类名来使用`simulate()`方法，我们使用上面的语法来完成，该方法可以接受一些参数。第一个参数是字符串形式的事件类型，这显然是强制性的。第二个参数是可选的，即与事件一起传递的内容(通常是事件对象)。

然后我们检查我们的`spy`是否是使用与我们之前的测试相似的方法调用的。然而，因为我们是用事件对象调用`handleChange()`，所以我们还可以通过使用`toHaveBeenCalledWith()`方法检查它是否被传入。

**测试模拟功能**

我们的下一个函数`handleSubmit()`，有一些事情与`handleChange()`有些不同。首先，它调用作为事件对象的一部分传入的`preventDefault()`。幸运的是，我们已经知道如何模拟这一点，因为我们在`beforeEach()`中做了一些非常类似的事情，当时我们设置了要传递给这个组件的道具。

```
it("should call preventDefault", () => {
  const mockPreventDefault = jest.fn(); const mockEvent = {
    preventDefault: mockPreventDefault
  }; wrapper.instance().handleSubmit(mockEvent); expect(mockPreventDefault).toHaveBeenCalled();});
```

我们声明一个变量`mockPreventDefault`，并把它赋给`jest.fn()`的值。然后我们声明我们的模拟事件对象，并将`preventDefault`属性赋给我们刚刚声明的`mockPreventDefault`变量。现在当我们调用`handleSubmit()`时，我们可以评估`preventDefault()`是否被正确调用。同样，非常简单，但你会经常遇到。

**测试函数是否返回**

`handleSubmit()`函数要测试的下一部分是，如果`submitActive`设置为 false，它将返回。这将需要我们之前使用的相同类型的代码，唯一的不同是我们的测试方法:

```
it("should return if submitActive is false", () => {
  const mockPreventDefault = jest.fn();
  const mockEvent = {
    preventDefault: mockPreventDefault
  };

  const spy = jest.spyOn(wrapper.instance(), "handleSubmit");
  wrapper.instance().forceUpdate();

  wrapper.instance().handleSubmit(mockEvent); expect(spy).toReturn();
});
```

我们像前面一样模拟我们的事件对象，以便测试在到达我们的条件语句之前不会出错，在我们调用的方法上放置一个 spy，强制更新，调用`handleSubmit()`，并在我们的 spy 上使用`toReturn()`方法。小菜一碟。

**测试作为道具流传下来的功能**

最后，我们将快速看一下如何测试一个函数，我们传递给这个组件的一个道具，特别是`submit()`函数。

在 beforeEach()中，我们将变量`mockSubmit`赋给了`jest.fn()`的值。每次我们运行测试时，mockSubmit 都会被重新分配，所以我们甚至不需要担心用 Jest 重置任何东西。

在我们开始调用 submit 之前，我们需要确保`handleSubmit()`不会简单地返回，所以我们将通过设置 state 来开始我们的测试，使`submitActive`为真，并且除了空字符串之外，`title`和`description`中还有其他内容。

```
it("should call submit with the correct params", () => {
  wrapper.setState({
    title: "test title",
    description: "test description",
    submitActive: true
  }); const expected = {
    title: "test title",
    description: "test description"
  }; const mockPreventDefault = jest.fn(); const mockEvent = {
    preventDefault: mockPreventDefault
  }; wrapper.instance().handleSubmit(mockEvent); expect(mockSubmit).toHaveBeenCalledWith(expected);
});
```

接下来，我们设置我们的期望，模拟我们的事件对象，调用`handleSubmit(),`并检查是否调用了`submit`道具上的或模拟。

当测试这个组件时，这几乎是新概念。您需要测试通过在 DOM 上模拟提交调用了`handleSubmit()`，测试了`handleSubmit()`中的`setState()`，测试了`checkFields()`中的条件状态设置，但除此之外别无他法。

如果你想在你的编辑器中查看代码，你可以在这里找到 repo。

[![](img/9914c5dd23ac08b70eea6f4f9ba6fed2.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/react) [## 学习 React -最佳 React 教程(2019) | gitconnected

### 排名前 49 的 React 教程-免费学习 React。课程由开发人员提交并投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/react)